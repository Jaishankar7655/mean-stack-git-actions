# MEAN Stack Application - Deployment and CI/CD Setup

## Project Overview
This repository contains the containerized setup and deployment configuration for a full-stack MEAN (MongoDB, Express.js, Angular, Node.js) application. The project demonstrates hands-on implementation of Docker containerization, cloud deployment on AWS EC2, Nginx reverse proxy routing, and an automated CI/CD pipeline using GitHub Actions.

**Live Application URL:** http://3.110.142.35
**GitHub Repository:** https://github.com/Jaishankar7655/mean-stack-git-actions.git

## Architecture and Infrastructure
* **Frontend:** Angular 15 application (Containerized using Node 18, served via Nginx on port 80).
* **Backend:** Node.js API with Express.js (Containerized using Node 18 Alpine, running on port 8080).
* **Database:** MongoDB (Running as a Docker container).
* **Cloud Provider:** AWS EC2 (Ubuntu 22.04 LTS).
* **CI/CD:** GitHub Actions.
* **Orchestration:** Docker Compose.

## 1. Containerization
The application is fully containerized using Docker.
* **Frontend Dockerfile:** Uses a multi-stage build. It first compiles the Angular application using the Angular CLI and then serves the compiled static files using an Nginx Alpine container.
* **Backend Dockerfile:** Copies the Node.js source code, installs dependencies, and exposes port 8080.
* **Docker Compose (`docker-compose.yml`):** Orchestrates three services: `mongo`, `backend`, and `frontend`. They communicate over a custom bridge network (`mean-network`).

## 2. Nginx Reverse Proxy
Nginx is utilized as a reverse proxy to route traffic seamlessly on port 80 without requiring the user to specify backend ports.
* Requests to the root `/` are served by the compiled Angular frontend.
* Requests starting with `/api/` are intercepted by Nginx and proxy-passed to the underlying `backend` container on port 8080 (`proxy_pass http://backend:8080/api/;`).

## 3. Automated CI/CD Pipeline (GitHub Actions)
The repository uses a GitHub Actions workflow (`.github/workflows/main.yml`) to achieve continuous integration and continuous deployment.

### Pipeline Workflow:
1. **Trigger:** The pipeline is triggered automatically on every push to the `main` branch.
2. **Build and Push:** It uses Docker Buildx to build fresh Docker images for the frontend and backend. It then authenticates with Docker Hub and pushes the newly tagged images.
3. **Continuous Deployment (SSH):** The pipeline uses `appleboy/ssh-action` to securely connect to the AWS EC2 instance (IP: 3.110.142.35).
4. **Restart Containers:** Once connected to the EC2 server, it navigates to the project directory, pulls the latest Docker images from Docker Hub, and runs `docker-compose up -d --build` to restart the architecture with zero manual intervention.

## 4. Manual Deployment Instructions
If deploying to a fresh server manually, follow these steps:

1. Clone the repository on the server:
   ```bash
   git clone https://github.com/Jaishankar7655/mean-stack-git-actions.git
   cd mean-stack-git-actions
   ```
2. Ensure Docker and Docker Compose are installed on the machine.
3. Start the application:
   ```bash
   docker-compose up -d --build
   ```
4. Access the application on port 80 via the server's public IP address.

## Evidence & Screenshots

### 1. CI/CD Configuration and Execution
*(Please attach a screenshot of your successful GitHub Actions pipeline run here)*
![CI/CD Pipeline](screenshots/cicd_pipeline.png)

### 2. Docker Image Build and Push Process
*(Please attach a screenshot of your Docker Hub repository displaying the pushed images here)*
![Docker Hub](screenshots/docker_hub.png)

### 3. Application Deployment and Working UI
*(Please attach a screenshot of the Angular frontend working in the browser at http://3.110.142.35 here)*
![Working UI](screenshots/working_ui.png)

### 4. Nginx Setup and Infrastructure Details
*(Please attach a screenshot of your EC2 instance details showing the IP or the Nginx configuration here)*
![Infrastructure](screenshots/infrastructure.png)
