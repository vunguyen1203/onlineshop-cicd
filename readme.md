# OnlineShop CI/CD Pipeline

Hi there, this project demonstrates how to implement a Continuous Integration and Continuous Deployment (CI/CD) pipeline for an OnlineShop application. The pipeline automates the process of building, testing, analyzing code quality, creating Docker images, and deploying the application. This helps reduce manual work, improve code quality, and make deployments faster and more reliable.

<p align="center">
  <a href="https://postimg.cc/SY22X29b">
    <img src="https://i.postimg.cc/Vv4jVXLv/cicd-pipeline-drawio.png" alt="CI/CD Pipeline">
  </a>
</p>

## Tech Stack

- GitHub
- Jenkins
- SonarQube
- Docker
- Dockerhub

## CI/CD Workflow

1. The developer writes code and pushes it to the GitHub repository.

2. Jenkins starts the CI pipeline automatically when it detects a new commit.

3. Jenkins builds the Oneline Shop application and checks that the project can be built successfully.

4. SonarQube analyzes the source code to find bugs, code smells, and security issues.

5. Jenkins builds a new Docker image and pushes it to the Dockerhub.

6. Jenkins connects to the deployment server through SSH and deploys the latest Docker image.

7. Send an email notification when the pipeline completes.
