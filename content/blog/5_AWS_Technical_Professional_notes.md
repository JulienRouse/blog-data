---
title: 'AWS Technical Professional'
url: '/blog/aws-technical-professional'
date: Sat, 25 Nov 2019 19:00:00 +0000
draft: true
tags: [AWS,Cloud,Learning Path]
featured: false
toc: true
summary: "Notes about the AWS Cloud practitionner and Technical Professional training"

---

# AWS Technical Professional

## Module 2: Core Services

### Compute

#### EC2

#### ECS

#### Lambda (i.e. Serverless)

Event-driven task compute service.

Zero administration.

Pay for compute time consumed

#### Summary

EC2 vs ECS vs Lambda 

### Storage

#### EBS: Elastic Block Storage

Similar to a *hard drive*, you can attach it to EC2 instances. 

One EBS can only extend one EC2, but one EC2 can have many EBS attached.

1GB to 16TB per volume.

Suited for apps that require:

-  Database
- File system
- Block-level storage

Automatic replIcation inside its AZ

Snapshot back-up to Amazon S3

You can provision a specific level of I/O performance, or select a burstable performance model.

#### S3

Bucket of static files

- Static website content
- Backups
- Storage (data store for analytics : Hadoop for instance)

#### Glacier

Archiving and backup. Eleven 9 of durability (99.99999999999% durability)

Starting from $0.004 per GB/month

One-way transition from S3 to Glacier

#### Snowball

Petabyte-scale data transport solution

- Cloud Migration
- Disaster Recovery
- Datacenter Decommission
- Content Distribution

#### CloudFront

Global CDN (Content Delivery Network)

Delivers content using global network of edge locations

### Database

#### RDS (Relational Database Service)

Can use:

- SQL
- MySQL
- Oracle
- PostgreSQL

Key benefits:

- Elastic
- Flexible
- Secure

#### Dynamo DB

NoSQL, fully managed, support document and key-value store

#### Database Migration Service

Heterogenous and heterogeneous migrations

Stream data to redshift from supported sources

#### Redshift

Data Warehouse and Analytics

BI

### Networking

#### VPC

virtual network on the cloud.

Public and private subnets, network topologies, route tabes and network gateway configuration, control access and security groups

- Elastic (200 subnets per VPC)
- Flexible
- Secure

#### Direct Connect

Establish a dedicated connection from your on-premises to AWS

Private connectivity between AWS and your datacenter

Use the same connection to access public and private ressources

- Reduce bandwith cost
- Consistent network performance
- Elastic

#### Route 53

Scalable DNS

- Latency Based Routing
- Weighted Round Robin
- DNS Fallover

Integrated Elastic Load Balancer

### Management Tools

#### CloudWatch

#### CloudFormation

#### Trusted Advisor

### Security

### IoT

