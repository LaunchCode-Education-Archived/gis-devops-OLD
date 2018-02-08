# Load Balanced Cloud

## Project

## Overview

This project will expand on what was learned in the previous unit. Your goal is create a load-balanced cloud-based application using good AWS Dev Ops practices.

## Make a Custom Snapshot

Build a new instance
Use AWS to make an snapshot of of that instance

## Provision a VPC

Spin up one EC2 machine based on the server snapshot.
Configure firewall to allow SSH and HTTP access.

## Set Up Load Balancing

Spin up a second EC2 machine based on the server snapshot.
Provision an ElasticLoadBalancer in front of the two EC2 machines.
Test traffic balancing.  Test one app going down.

## Connect a Database

Provision an RDS database and connect to application.

## Bonus Mission

Bonus: Provision an SSH bastion and make the application servers only accessible via http.
