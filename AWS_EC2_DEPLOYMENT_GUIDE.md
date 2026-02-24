# AWS EC2 Deployment Guide for MEAN Stack App

This guide walks you through deploying your containerized MEAN stack app onto an AWS EC2 instance.

## 1. Launching an EC2 Instance
1. Log in to your **AWS Management Console** and navigate to **EC2**.
2. Click **Launch Instance**.
3. **Name**: `MEAN-App-Server`
4. **AMI**: Select **Ubuntu Server 22.04 LTS** (or 24.04).
5. **Instance Type**: `t2.micro` (Free Tier eligible) or `t3.micro`.
6. **Key Pair**: Create a new key pair (e.g., `mean-app-key.pem`) and download it. Keep this file safe.
7. **Network Settings**: 
   - Allow SSH traffic from anywhere.
   - Allow HTTP traffic from the internet (port 80).
   - Allow HTTPS traffic from the internet (port 443).
8. Click **Launch Instance**.

## 2. Connecting to the Instance
Wait for the instance state to become **Running**, then connect to it using SSH:

```bash
chmod 400 mean-app-key.pem
ssh -i "mean-app-key.pem" ubuntu@<YOUR_EC2_PUBLIC_IP>
```

## 3. Installing Docker & Docker Compose
Once connected, run the following commands to install Docker:

```bash
# Update packages
sudo apt-get update -y

# Install Docker
sudo apt-get install docker.io -y

# Install Docker Compose
sudo apt-get install docker-compose -y

# Start and enable Docker
sudo systemctl start docker
sudo systemctl enable docker

# Add your user to the docker group (to run docker without sudo)
sudo usermod -aG docker ubuntu
```
*(You may need to log out and log back in for the group change to take effect).*

## 4. Deploying the Application manually
1. Clone your GitHub repository inside the EC2 instance:
   ```bash
   git clone <YOUR_GITHUB_REPO_URL>
   cd crud-dd-task-mean-app
   ```
2. Build and run the containers using Docker Compose:
   ```bash
   docker-compose up -d --build
   ```
3. Open your browser and navigate to `http://<YOUR_EC2_PUBLIC_IP>`. You should see your Angular application working properly mapping to your Node.js/MongoDB backend!

## 5. Setting Up GitHub Actions CI/CD Secrets
To enable the automated deployment we configured in `.github/workflows/main.yml`, go to your GitHub repository -> **Settings** -> **Secrets and variables** -> **Actions** -> **New repository secret**, and add the following:

- `VM_HOST`: The Public IP address of your EC2 instance.
- `VM_USER`: `ubuntu`
- `SSH_PRIVATE_KEY`: The entire contents of your `mean-app-key.pem` file.
- `DOCKER_USERNAME`: Your DockerHub username.
- `DOCKER_PASSWORD`: Your DockerHub password (or access token).

Now, every time you push code to GitHub, it will automatically build your Docker images, push them to DockerHub, SSH into your EC2 instance, and deploy the new code!
