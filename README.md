# AWS CI/CD Workshop
Overview

This project demonstrates a complete CI/CD workflow for deploying a containerized application on AWS.

I built the infrastructure using **AWS CDK with Python**, connected the project to **GitHub**, and created an automated pipeline that tests the application, builds a Docker image, pushes it to Amazon ECR, and deploys it to Amazon ECS using Fargate.

The goal of this project was to understand how application code can move from a GitHub repository to a running application on AWS with minimal manual intervention.

## Architecture
text
                         ┌─────────────────────┐
                         │       GitHub        │
                         │   cicd-workshop     │
                         └──────────┬──────────┘
                                    │
                                    │ Push
                                    ▼
                         ┌─────────────────────┐
                         │    AWS CodePipeline │
                         └──────────┬──────────┘
                                    │
                         ┌──────────┴──────────┐
                         │                     │
                         ▼                     ▼
                ┌─────────────────┐   ┌─────────────────┐
                │    CodeBuild    │   │    CodeBuild    │
                │  Unit Testing   │   │  Docker Build   │
                └─────────────────┘   └────────┬────────┘
                                               │
                                               │ Push Image
                                               ▼
                                      ┌─────────────────┐
                                      │   Amazon ECR    │
                                      │  Docker Image   │
                                      └────────┬────────┘
                                               │
                                               │ Deploy
                                               ▼
                                      ┌─────────────────┐
                                      │   ECS Fargate   │
                                      │     Service     │
                                      └────────┬────────┘
                                               │
                                               ▼
                                      ┌─────────────────┐
                                      │  Load Balancer  │
                                      └────────┬────────┘
                                               │
                                               ▼
                                             🌐
                                        Application
## What I Built

The project includes the following AWS components:

* **AWS CodePipeline** – orchestrates the CI/CD workflow.
* **AWS CodeBuild** – runs automated tests and builds the Docker image.
* **Amazon ECR** – stores the Docker container image.
* **Amazon ECS with Fargate** – runs the container without managing servers.
* **Application Load Balancer** – provides access to the deployed application.
* **AWS CloudFormation/CDK** – provisions and manages the infrastructure.
* **GitHub** – stores the application and infrastructure source code.

## CI/CD Workflow

The pipeline is designed to follow this flow:

Developer
   │
   │ git push
   ▼
GitHub
   │
   ▼
CodePipeline
   │
   ├── Source
   │
   ├── Code Quality Testing
   │
   ├── Docker Build & Push
   │
   └── Deploy to ECS/Fargate
           │
           ▼
       Live Application

When changes are pushed to the `main` branch, CodePipeline retrieves the latest source code and moves it through the pipeline stages.

### 1. Source

The source code is maintained in GitHub:

**Repository:** `Dhilli-prasad/cicd-workshop`

The pipeline monitors the `main` branch.

 2. Code Quality Testing

AWS CodeBuild runs the project's tests using the configured build specification.

This gives the pipeline an opportunity to catch problems before creating and deploying a new container image.

 3. Docker Build and ECR Push

The application is packaged into a Docker image.

The image is then pushed to the Amazon ECR repository created using AWS CDK.

4. ECS/Fargate Deployment

After the Docker image is available in ECR, the pipeline deploys the application to ECS using Fargate.

This allows the application to run as a container without requiring me to manage the underlying servers.

 Infrastructure as Code

Instead of creating the AWS resources manually through the AWS Console, I used **AWS CDK with Python**.

The infrastructure is defined as code and deployed using commands such as:

cdk deploy

This makes the infrastructure repeatable and easier to maintain.

Some of the infrastructure components are separated into different CDK stacks, including:

text
infrastructure/
├── app.py
├── infrastructure/
│   ├── __init__.py
│   ├── ecr_stack.py
│   ├── infrastructure_stack.py
│   ├── pipeline_stack.py
│   └── repo_connection.py
├── buildspec_test.yml
└── buildspec_docker.yml

## Technologies Used

| Technology       | Purpose                                 |
| ---------------- | --------------------------------------- |
| Python           | Application and CDK infrastructure code |
| AWS CDK          | Infrastructure as Code                  |
| GitHub           | Source code repository                  |
| AWS CodePipeline | CI/CD orchestration                     |
| AWS CodeBuild    | Testing and Docker builds               |
| Amazon ECR       | Container image storage                 |
| Amazon ECS       | Container orchestration                 |
| AWS Fargate      | Serverless container execution          |
| Docker           | Containerization                        |
| CloudFormation   | AWS infrastructure provisioning         |

## Deployment

The infrastructure can be deployed using AWS CDK.

For example:
bash
cd infrastructure
cdk deploy EcrStack

and:

bash
cdk deploy PipelineStack --require-approval never

The exact commands may vary depending on which part of the infrastructure is being deployed.

## What I Learned

This project helped me understand how the different parts of a modern DevOps workflow fit together.

Some of the main things I learned were:

* How to provision AWS resources using CDK.
* How to connect GitHub with AWS CodePipeline.
* How CodeBuild can be used for automated testing.
* How to build Docker images as part of a CI/CD pipeline.
* How to store container images in Amazon ECR.
* How ECS Fargate can run containerized applications.
* How a Git push can trigger an automated deployment.
* How to troubleshoot issues involving GitHub connections, branches, CDK stacks, and AWS pipeline stages.

## Challenges and Troubleshooting

One of the useful parts of this project was troubleshooting the pipeline rather than only following the deployment steps.

For example, I encountered a GitHub source configuration issue where CodePipeline was looking for the wrong repository:

```text
my-aws-workshop/cicd-workshop
```

while my actual repository was:

```text
Dhilli-prasad/cicd-workshop
```

After correcting the repository configuration and ensuring the `main` branch was available, the pipeline successfully completed all stages.

The final pipeline execution showed successful stages for:

```text
Source                 ✅
Code-Quality-Testing   ✅
Docker-Push-ECR        ✅
Deploy-Test            ✅
```

This was an important part of the project because it showed me how to identify where a CI/CD pipeline has failed and trace the problem back to its source.

## Project Status

The CI/CD pipeline is successfully configured to:

**GitHub → CodePipeline → CodeBuild → Docker → ECR → ECS/Fargate**

The application can therefore be updated through the source repository and deployed through the automated pipeline.

## Cleanup

AWS resources created for this project can generate charges if they are left running.

When the project is no longer needed, the infrastructure can be removed using:

```bash
cdk destroy --all
```

ECR images may need to be removed before the ECR repository can be deleted.

> **Note:** I would only run the cleanup commands when I am finished using the deployed application and no longer need its live URL.

## Author

**Dhilli Prasad**

GitHub: `Dhilli-prasad`

---

### One thing I'd add to make your GitHub README look much better

At the very top, add badges such as:

```text
AWS CDK | Python | Docker | CodePipeline | ECS | ECR
```

and put your **architecture diagram immediately below the project description**.

That will make the repository look much more like a real **DevOps/MLOps portfolio project** rather than a workshop submission.
