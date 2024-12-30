# Complete CI/CD Pipeline with Terraform and Jenkins

## Overview
This project demonstrates a complete CI/CD pipeline using Jenkins and Terraform. The pipeline automates the provisioning of an EC2 instance and the deployment of a Java Maven application with Docker Compose. This comprehensive guide is tailored for DevOps practitioners looking to implement robust and scalable CI/CD pipelines.

## Technologies Used
- **Terraform**: Automates infrastructure provisioning.
- **Jenkins**: Orchestrates the CI/CD pipeline.
- **Docker**: Containerizes the application.
- **AWS**: Provides cloud infrastructure.
- **Git**: Version control for application and pipeline code.
- **Java & Maven**: Builds and packages the Java application.
- **Docker Hub**: Stores the container image.
- **Linux**: Runs the Jenkins server and EC2 instances.

---

## Project Description
This CI/CD pipeline integrates the following stages:

1. **CI Stage**: Builds the Java Maven application and creates a Docker image.
2. **CI Stage**: Pushes the Docker image to Docker Hub.
3. **CD Stage**: Provisions an EC2 instance using Terraform.
4. **CD Stage**: Deploys the new application version to the provisioned EC2 instance using Docker Compose.

The pipeline introduces automation at every step to ensure consistency, scalability, and reduced deployment time.

---

## Key Files and Configurations
### 1. **Jenkinsfile**
Defines the pipeline stages, including build, provision, and deployment steps.

### 2. **Dockerfile**
Defines the containerization process for the Java application.

### 3. **docker-compose.yaml**
Configures the deployment of the Java application and PostgreSQL database.

### 4. **Terraform Files**
- **`main.tf`**: Provisions the EC2 instance and supporting infrastructure.
- **`variables.tf`**: Defines configurable variables.
- **`entry-script.sh`**: Installs Docker and Docker Compose on the EC2 instance.

---

## Step-by-Step Guide

### 1. Prerequisites
- **AWS CLI** installed and configured with appropriate credentials.
- **Terraform CLI** installed.
- **Jenkins** installed and running.
- **Docker** installed.
- **Git** installed.

### 2. Create SSH Key Pair
1. Generate an SSH key pair:
   ```bash
   ssh-keygen -t rsa -b 2048 -f ~/.ssh/myapp-key-pair
   ```
2. Add the public key to AWS:
   ```bash
   aws ec2 import-key-pair --key-name "myapp-key-pair" --public-key-material file://~/.ssh/myapp-key-pair.pub
   ```

### 3. Set Up Terraform in Jenkins
1. Add Terraform to the Jenkins Docker container:
   ```bash
   docker exec -it <jenkins-container-id> bash
   apk add terraform
   ```
2. Install the Terraform plugin in Jenkins.

### 4. Configure the Application Repository
- Add Terraform configuration, Dockerfile, and `Jenkinsfile` to the application's Git repository.

### 5. Configure Jenkins Pipeline
1. Create a Jenkins pipeline job.
2. Use the following `Jenkinsfile`:
   ```groovy
   pipeline {
       agent any
       stages {
           stage("build") {
               steps {
                   sh "mvn clean package"
               }
           }
           stage("docker") {
               steps {
                   sh "docker build -t syleniainc/demo-app ."
                   sh "docker push syleniainc/demo-app"
               }
           }
           stage("provision") {
               steps {
                   sh "terraform init"
                   sh "terraform apply -auto-approve"
               }
           }
           stage("deploy") {
               steps {
                   sh "bash server-cmds.sh"
               }
           }
       }
   }
   ```

### 6. Deploy the Pipeline
1. Run the Jenkins pipeline.
2. Monitor the logs for each stage.

### 7. Verify Deployment
- Check the running application:
   ```bash
   curl http://<EC2_PUBLIC_IP>:8080
   ```

---

## Best Practices

### Jenkins
- Use credentials securely with Jenkins credential store.
- Configure Jenkins agents to scale builds dynamically.

### Terraform
- State Management: Use a remote backend (e.g., S3 with encryption) to manage Terraform state securely. By storing the Terraform state in a remote backend such as S3, you ensure that the  state is centralized, secure, and accessible for team collaboration. This practice also prevents issues caused by local state file corruption or accidental deletion. Remote state enables locking mechanisms to prevent simultaneous writes, ensuring consistency during updates. Additionally, it facilitates versioning and backups, providing a safety net for restoring previous states in case of errors.
- Define clear variable structures for modularity.
- Lock Terraform provider versions.

### Security
- Restrict inbound SSH access to specific IPs.
- Use IAM roles for AWS resource access.
- Enable encryption for sensitive data.

---

## Final Thoughts
This project illustrates a fully automated CI/CD pipeline, showcasing the power of Terraform and Jenkins in modern DevOps workflows. By following this guide, you can replicate and extend the setup for diverse applications.

Happy Deploying! 🚀
