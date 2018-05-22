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

1. Create an RDS instance
2. Use the RDS instance in your airwaze app running on EC2
3. Use environment variables to connect to the RDS (if you haven't already)
