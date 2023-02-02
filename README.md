# stress-tracking-api
The goal of this project is to assess development skills suitable for a backend engineer position. It tests knowledge and proficiency with software design, unit testing and web technologies like HTTP and REST.

```
Project Name: OOCA Stress Tracking API
Project Goal: Create a small web-app for tracking user's stress level.
Technology: Nodejs + TypeScript with any framework and technologies you like.
Deliverables: The solution can be deployed to anywhere like Heroku, Netify,GCP or even Firebase.
```
## Description
Users can use the mobile/web app to upload their stress level (0-5) and image (optional). The mobile/web app uses REST
API to upload it. Every uploaded images should be resized for more suitable to display on mobile/web. The mobile/web app
also has a feed that shows all stress level tracked by the user with associated image.

**Task**: Build the API described above. Write unit/e2e tests for each components.

- [1. Infrastructure](#1-infrastructure)
  * [AWS Diagrams](#aws-diagrams)
  * [Jenkins Server](#jenkins-server)
    + [Jenkins pipeline for Prerequisite resources](#jenkins-pipeline-for-prerequisite-resources)
    + [Jenkins pipeline for Application](#jenkins-pipeline-for-application)
  * [Database Diagram](#database-diagram)
- [2. Development](#2-development)
  * [API Endpoint](#api-endpoint)
    - [Upload a stress level record](#upload-a-stress-level-record)
    - [Get all stress level records](#get-all-stress-level-records)
  * [Repository Folder Structure](#repository-folder-structure)
    + [Infrastructure Repo](#infrastructure-repo)
    + [API Stress Tracking Repo](#api-stress-tracking-repo)

# 1. Infrastructure

## AWS Diagrams
This requirements to create and deploy new application to tracking user's stress level. So I'm decided to use AWS Elastic Beantalk to deploy and deliveriles our application.AWS Elastic Beanstalk enables you to manage all of the resources that run your application also including Load Balancer, Autoscaling and Database connection support.

Use an Amazon Relational Database Service (Amazon RDS) DB instance to store `tracking` and `user` data gathered and modified by application. The database can be attached to your environment, created and managed externally. Elastic Beanstalk provides connection information for attached DB instances in environment properties

We will save image to S3 Bucket, and save image metadata in `Tracking` table following `imageBucket` and `imagePath`.

![aws_infras.png](Image/aws_infras.png)

## Jenkins Server

`On-premise or Virtural machine on Cloud`
### Jenkins pipeline for Prerequisite resources
Before start deploy and deliveries our application to Cloud environment, we need to deploy some prerequisite resources including: VPC, S3, RDS - AuroraDB, ECR

![prerequisite_resources_cicd.png](Image/prerequisite_resources_cicd.png)

### Jenkins pipeline for Application
The application will deploy to Cloud Providers following stages on this pipeline:
![jenkins_service_cicd.png](Image/jenkins_service_cicd.png)

## Database Tables

- User

| Parameter | Type     | Description |
|:----------|:---------|:------------|
| `userID`  | `string` | User ID     |
| `email`   | `string` | User email  |


- Tracking

| Parameter     | Type       | Description                                 |
|:--------------|:-----------|:--------------------------------------------|
| `iD`          | `string`   | Stress Tracking Action ID - Auto Increments |
| `userID`      | `string`   | User ID                                     |
| `level`       | `number`   | Stress level (0-5)                          |
| `imageBucket` | `string`   | Image S3 Bucket Name                        |
| `imagePath`   | `string`   | Image S3 Bucket Path                        |
| `createdAt`   | `datetime` | Time created action                         |
# 2. Development

## API Endpoint

#### Upload a stress level record

`
  POST /tracking
`

| Parameter | Type     | Description        | Required |
|:----------|:---------|:-------------------|:---------|
| `userID`  | `string` | User ID            | Yes      |
| `level`   | `number` | Stress level (0-5) | Yes      |
| `image`   | `File`   | Stress image       | Yes      |

#### Get all stress level records

`
  GET /tracking
`

| Parameter   | Type        | Description | Required |
|:------------|:------------|:------------|:---------|
| `userID`    | `string`    | User ID     | No       |

## Repository Folder Structure

### Infrastructure Repo

| Name                         | Description                                                    |
|------------------------------|----------------------------------------------------------------|
| jenkinsFile                  | Jenkins definition for CI/CD stages                            |
| properties/                  | Includes properties configuration for infrastructure resources |
| properties/dev_params.json   | dev environment infrastructure  properties                     |
| properties/uat_params.json   | uat environment  infrastructure properties                     |
| properties/prod_params.json  | prod environment infrastructure  properties                    |
| CloudFormation/              | CloudFormation IaC folder                                      |
| CloudFormation/vpc.yaml      | VPC CFT template for Tracking API                              |
| CloudFormation/s3.yaml       | S3 CFT template for Tracking API                               |
| CloudFormation/database.yaml | Database CFT template for Tracking API                         |

### API Stress Tracking Repo

| Name                               | Description                                                                                                                                           |
|------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| controllers/                       | `controller` class handles incoming requests and sends the response data back to the client. Use the `repository` class to interact with the database |
| controllers/tracking.controller.ts | `tracking` controller                                                                                                                                 |
| models/                            | Includes model represents the database model for its component                                                                                        |
| models/tracking.model.ts           | `tracking` model                                                                                                                                      |
| models/user.model.ts               | `user` model                                                                                                                                          |
| repositories/                      | Includes repositories wrapper for the database - Read and write data to the database.                                                                 |
| repositories/tracking.repo.ts      | `tracking` repository                                                                                                                                 |
| repositories/user.repo.ts          | `user` repository                                                                                                                                     |
| config/                            | Includes configuration files: global variables, logger config                                                                                         | 
| routes/                            | Register all component and middleware routes.                                                                                                         |
| routes/tracking.routes.ts          | API endpoints for stress tracking (GET /tracking, POST /tracking).                                                                                    |
| server.ts                          | Declare everything required for express server: import component route, error handling, ...                                                           |
| app.ts                             | Register all component and middleware routes.                                                                                                         |
| package.json                       | npm package metadata                                                                                                                                  |
| tests/                             | Test folder                                                                                                                                           | 
| tests/tracking.test.ts             | Test file for testing `tracking` component and its endpoints                                                                                          | 
| DockerFile                         | Dockerfile                                                                                                                                            |
| jenkinsFile                        | Jenkins definition for CI/CD stages                                                                                                                   |
| CloudFormation/                    | CloudFormation IaC folder                                                                                                                             |
| CloudFormation/service.yaml        | Service CFT template for Tracking API (Elastic Beanstalk, Role, Instance Profile, CloudWatch, ...)                                                    |
| properties/                        | Includes properties configuration for infrastructure resources                                                                                        |
| properties/dev_params.json         | dev environment infrastructure  properties                                                                                                            |
| properties/uat_params.json         | uat environment  infrastructure properties                                                                                                            |
| properties/prod_params.json        | prod environment infrastructure  properties                                                                                                           |


`Diagrams URL: https://drive.google.com/file/d/1nSHpk3z1ZR68G46OJ4MI6v_UZKJPmzg4/view?usp=sharing`