---
title: "Walkthrough: Introduction to Amazon Web Services"
---

Follow along with the instructor as we configure an app to run inside a VPC.

## Setup

This project uses the [Simple Map App](https://gitlab.com/LaunchCodeTraining/simple-map-app)

Remember, in order to connect to an EC2 in a VPC you need to have the following pieces configured:
0. Your EC2 instance should have been created with a public IP address.
1. Port 22 open on the security group of the EC2 instance.
2. Port 22 open on the subnet of the EC2 instance.
3. An Internet Gateway needs to be attached to your VPC.
4. There needs to be a custom route directing traffic from `0.0.0.0/0` to your Internet Gateway.

Here is an [AWS Article on Troubleshooting SSH connections](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-linux-ssh-troubleshooting/) if you have any trouble.

## Goals

There are two goals for this walkthrough:

1. Connect to a remote Postgres database.
2. Load balance an application.

### Setting up a simple VPC.

Create a VPC with a CIDR address of `10.0.0.0/16`.

### Setting up an RDS

An RDS needs multiple Subnets in different Availability Zones in order to be high availability.

Create two Subnets, `Walkthrough-DBSubnet` and `Walkthrough-AltDBsubnet`, in your VPC with respective CIDR blocks `10.0.1.0/24` and `10.0.2.0/24`.  Be sure that each one is in a different Availability Zone.  

Create an RDS Subnet Group with `Walkthrough-DBSubnet` and `Walkthrough-AltDBsubnet`.  

Create an RDS Intances with a Postgres server. Make the databasename `my_db`.  Make the username `launchcode_admin`.  Make the password `launchcode`.

Take a look at the RDS monitoring. It's cool.

### Setting up the server (without a LoadBalancer)

Create a public subnet for your server. `10.0.3.0/24` 

Create a new EC2 instance.  Be sure to make the instance as "Assign Public IP" and include the bash script in "Advanced".

We can include all of the setup instructions for an EC2 at startup by including a BASH script.    

Use this BASH script when you spin up your server:
```
#!/bin/bash
# Install Java
apt-get update -y && apt-get install -y openjdk-8-jdk

useradd -M simpleApp
mkdir /opt/simpleApp
mkdir /etc/opt/simpleApp
chown -R simpleApp:simpleApp /opt/simpleApp /etc/opt/simpleApp
chmod 777 /opt/simpleApp

# Write SimpleApp config file
cat << EOF > /etc/opt/simpleApp/simpleApp.config
APP_DB_HOST={your database here}
APP_DB_PORT=5432
APP_DB_NAME=myApp_db
APP_DB_USER=launchcode_admin
APP_DB_PASS=launchcode
EOF

# Write systemd unit file
cat << EOF > /etc/systemd/system/simpleApp.service
[Unit]
Description=SimpleApp Walkthrough
After=syslog.target

[Service]
User=simpleApp
EnvironmentFile=/etc/opt/simpleApp/simpleApp.config
ExecStart=/usr/bin/java -jar /opt/simpleApp/app.jar SuccessExitStatus=143
Restart=always

[Install]
WantedBy=multi-user.target
EOF

systemctl enable simpleApp.service
```

Create a new Security Group called `Walkthrough-WebAppSecurityGroup`.

You will also need to add a Internet Gatway and attach it to the VPC.  After that, configure the route table to push all traffic `0.0.0.0/0` to the gateway. You will need to create a `Walkthrough-InternetRouteTable`.  Edit the route table to send `0.0.0.0/0` to the internet gateway.

Finally, configure the Route Table on the `Walkthrough-PublicSubnet` to point to the the `Walkthrough-InternetRouteTable`.

Now you should be able to telnet the machine and check the port.  `telnet {EC2 endpoint} 22`.  Success.

### Setting up the machine

Once this machine spins up.  Grab the public IP address and try to SSH into the machine.

```
$ ssh -i /path/to/your/pem ubuntu@{ip address}
```

Run a telnet command to check if the server connection is open:
`telnet {RDS IP Address} 5432`

Open a port on the database by adding a rule to the RDS Security group that allows traffic from the `Walkthrough-PublicSubnet`.

On the RDS Security Group, open up the port 5432 from the servers 10.0.1.0/24.  Then grab the internal IP address from the RDS detail page of our database.


That passes.  Great.  Now try and get in using psql. Switch to the simpleApp user with `su simpleApp`. Run the command:
```
psql -U simpleApp_user -h {RDS IP address} simpleApp_db
```

SCP a copy of the jar onto the server.
```
scp -i ~/.ssh/mikes-keys.pem build/libs/app-0.0.1-SNAPSHOT.jar ubuntu@{EC2 IP}:/opt/simpleApp/app.jar
```

Run `systemctl restart simpleApp.service` to see if it works.

Let's check and see if it is running locally:
```
$ telnet localhost 8080
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
^CConnection closed by foreign host.

$ curl localhost:8080/index.html
....
```

Yep!  It looks good!  Now let's try and hit it from the browser.  Grab the public url of the EC2.

Spinning.  Try to telnet to it from the outside.
```
$ telnet {EC2 IP} 8080
Trying 34.238.190.181...
```

... Must be a firewall.  Let's take a look at the security groups. Add an inbound TCP rule on port 8080 for the WebServerSecurity group.

Telnet again. Success!

### Setting up a Load Balancer

Stand up another EC2 just the same way that we did right here. Be sure to make it assign a public IP and have a the same `Walkthrough-WebServerSecurityGroup` as the other instance.

SSH into the machine.  Send up a jar.  Start the server.  Make sure that you can hit the URL.

Next create another Subnet.  The ELB needs two subnets each in a different availability zone.  Call the subnet `Walkthrough-AltPublicSubnet` CIDR block `10.0.5.0/24`. Remember to edit the route table to point to the internet gateway.

Create a new ELB.  During the process create a new `Walkthrough-LoadBalancerSG`.  Grab the DNS name of the LoadBalancer.

Telnet to the load balancer to see if it is up.
```
telnet {elb dns} 80
```

It connects via telnet, but the page doesn't load.  Let's look at our target group.  Oh!  There are no registered targets.  Let's add a few of those to our ELB.  Wait until the target's status turns healthy.

Wait a second... They're coming up unhealthy.  Oh!  We need to target port 8080. Remove those targets and re-add them with the port 8080.

Stand up an ELB server. Demonstrate how it load balances between multiple healthy hosts. Demonstrate what happens when a host becomes unhealthy. 
