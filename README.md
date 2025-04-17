# Java-application-hosted-on-elasticbeanstalk-rds-pipeline-
🚀 VProfile AWS CI/CD Pipeline | ⚡ Full automation from Bitbucket to Elastic Beanstalk | 🗄️ MySQL RDS backend | ☁️ S3 artifact storage  🔧 Tech Stack: 🛠️ CodeBuild &amp; CodePipeline  ☕ Java 17 (Corretto)  📦 Maven builds  🔒 Secure config management  📊 CloudWatch monitoring  💡 Demonstrates DevOps best practices for cloud-native deployments on AWS


DevOps Project: VProfile CI/CD Pipeline on AWS
Project Overview
This project implements a complete CI/CD pipeline for the VProfile application on AWS infrastructure, featuring:

AWS Elastic Beanstalk for application hosting

Amazon RDS MySQL for database services

AWS CodePipeline for CI/CD orchestration

Bitbucket as source code repository

AWS CodeBuild for build automation

Architecture Diagram
System Architecture

Key Components:
Bitbucket Repository: Source code management

AWS CodePipeline: CI/CD workflow orchestration

AWS CodeBuild: Automated build and package

Elastic Beanstalk: Application deployment environment

Amazon RDS: MySQL database service

Database Configuration
RDS Instance Details
RDS Configuration

Endpoint: vprords.c1ikcm2m84ek.us-east-1.rds.amazonaws.com:3306

Engine: MySQL Community 8.0.35

Availability Zone: us-east-1d

Security: Not publicly accessible

VPC: vpc-0c15a5f6da8e4e21

Database Schema
The application uses a MySQL database with three main tables:

user: Stores user profiles and credentials

role: Defines user roles (currently only ROLE_USER)

user_role: Junction table for user-role relationships

Database Initialization
The database is initialized using a SQL dump file (db_backup.sql) containing:

sql
Copy
-- Sample initialization commands
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(255) DEFAULT NULL,
  `password` varchar(255) DEFAULT NULL,
  -- ... additional fields ...
  PRIMARY KEY (`id`)
);

CREATE TABLE `role` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(45) DEFAULT NULL,
  PRIMARY KEY (`id`)
);

CREATE TABLE `user_role` (
  `user_id` int(11) NOT NULL,
  `role_id` int(11) NOT NULL,
  PRIMARY KEY (`user_id`,`role_id`)
);
Database Restoration
Database Restoration

The database is restored using:

bash
Copy
mysql -h vprords.c1ikcm2m84ek.us-east-1.rds.amazonaws.com \
      -u admin -pQrYp94rptWwz3ltf1CPo accounts < db_backup.sql
Build Process
Build Specification (buildspec.yml)
yaml
Copy
version: 0.2

phases:
  install:
    runtime-versions:
      java: corretto17
  pre_build:
    commands:
      - apt-get update
      - apt-get install -y jq 
      - wget https://archive.apache.org/dist/maven/maven-3/3.9.8/binaries/apache-maven-3.9.8-bin.tar.gz
      - tar xzf apache-maven-3.9.8-bin.tar.gz
      - ln -s apache-maven-3.9.8 maven
      - sed -i 's/jdbc.password=admin123/jdbc.password=QrYp94rptWwz31tflCPo' src/main/resources/application.properties
      - sed -i 's/jdbc.username=admin/jdbc.username=admin/' src/main/resources/application.properties
      - sed -i 's/db01:3306/vprords.c1ikcm2m84ek.us-east-1.rds.amazonaws.com:3306/' src/main/resources/application.properties
  build:
    commands:
      - mvn install
  post_build:
    commands:
       - mvn package
artifacts:
  files:
     - '**/*'
  base-directory: 'target/vprofile-v2'
Key build steps:

Installs Java Corretto 17 and Maven 3.9.8

Updates database connection properties in application.properties

Executes Maven build and package

Deployment Pipeline
CodePipeline

Pipeline Stages:
Source: Connects to Bitbucket repository

Build: Executes CodeBuild with the specified buildspec

Deploy: Deploys to Elastic Beanstalk environment

Application Access
Login Screen

Default Credentials:
Username: admin_vp

Password: Secured (hashed in database)

Sample Users:
The database includes several pre-configured users with different profiles and roles.

Security Considerations
Database Security:

RDS instance is not publicly accessible

Credentials stored securely in AWS Systems Manager Parameter Store

Passwords are hashed using BCrypt

Application Security:

Role-based access control

Secure password storage

Infrastructure Security:

Least privilege IAM roles

Encrypted S3 artifacts

VPC security groups restricting access

Monitoring and Maintenance
AWS CloudWatch for application metrics and logs

Elastic Beanstalk environment health monitoring

RDS performance monitoring

Pipeline execution history for debugging

Getting Started
Prerequisites:
AWS account with appropriate permissions

Bitbucket repository with application code

MySQL client for database administration

Setup Steps:
Create RDS MySQL instance

Initialize database using provided SQL script

Set up CodeBuild project with buildspec.yml

Configure CodePipeline to connect to source repository

Deploy to Elastic Beanstalk

Troubleshooting Guide
Issue	Solution
Build failures	Check CodeBuild logs for specific errors
Database connection issues	Verify RDS security groups and credentials
Deployment failures	Check Elastic Beanstalk environment health
Pipeline stalls	Look for approval actions or resource limits
Future Enhancements
Implement blue-green deployments

Add automated testing stages

Integrate security scanning tools

Set up comprehensive monitoring and alerts

Implement infrastructure as code using AWS CDK

Project Management
HKH Infotech

This project was developed as part of HKH Infotech's DevOps training program, demonstrating:

Continuous Integration best practices

Infrastructure as Code principles

Automated deployment strategies

Cloud-native application development