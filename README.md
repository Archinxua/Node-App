# CI/CD Pipeline for Web Application

A React + Vite web application with automated CI/CD pipeline using Jenkins and GitHub webhooks for deployment to AWS EC2.

## Overview

This project demonstrates a complete CI/CD pipeline implementation that automates the build and deployment process of a React application to an AWS EC2 instance. The pipeline uses Jenkins for orchestration, GitHub webhooks for trigger automation, and Nginx for serving the application.

## Features

- **Automated Build Process**: Automated dependency installation and build using npm
- **CI/CD Pipeline**: Jenkins pipeline with GitHub webhook integration
- **Automated Deployment**: Automated deployment to AWS EC2 via SSH
- **Nginx Integration**: Automatic Nginx restart and web root update
- **React + Vite**: Modern React application with Vite for fast development and optimized builds

## Tech Stack

- **Frontend**: React 19, Vite 7
- **CI/CD**: Jenkins, GitHub Webhooks
- **Deployment**: AWS EC2, Nginx
- **Build Tool**: Vite
- **Linting**: ESLint

## Prerequisites

- Jenkins server with SSH agent plugin installed
- AWS EC2 instance with:
  - Ubuntu/Debian-based OS
  - Nginx installed and configured
  - SSH access enabled
  - User with sudo privileges
- GitHub repository with webhook configured
- SSH credentials configured in Jenkins

## Project Structure

```
Node-App/
├── public/              # Public assets
├── src/                # React source code
├── dist/               # Build output (generated)
├── Jenkinsfile         # Jenkins pipeline configuration
├── package.json        # Node.js dependencies
├── vite.config.js      # Vite configuration
└── eslint.config.js    # ESLint configuration
```

## Jenkins Pipeline Configuration

The `Jenkinsfile` defines a declarative pipeline with two main stages:

### Build Stage
- Installs npm dependencies
- Builds the React application using Vite
- Generates optimized production build in `dist/` directory

### Deploy Stage
- Creates remote directory on EC2 instance
- Copies built files to EC2 via SCP
- Updates Nginx web root
- Restarts Nginx service

## Setup Instructions

### 1. Configure Jenkins

1. **Install Required Plugins**:
   - SSH Agent Plugin
   - GitHub Plugin (for webhook support)

2. **Configure SSH Credentials**:
   - Go to Jenkins → Credentials → Add Credentials
   - Add your EC2 private key (.pem file)
   - Note the credential ID (used in Jenkinsfile as `SSH_CREDENTIAL_ID`)

3. **Create Pipeline Job**:
   - New Item → Pipeline
   - Configure GitHub webhook trigger
   - Point to your repository

### 2. Configure EC2 Instance

```bash
# Install Nginx
sudo apt update
sudo apt install nginx -y

# Configure Nginx (optional - default config works)
sudo systemctl enable nginx
sudo systemctl start nginx
```

### 3. Update Jenkinsfile Variables

Edit the `Jenkinsfile` with your specific values:

```groovy
environment{
    EC2_HOST = "your-ec2-ip-address"
    SSH_CREDENTIAL_ID = "your-jenkins-credential-id"
    REMOTE_USER = "ubuntu"  # or ec2-user for Amazon Linux
    REMOTE_PATH = "/home/ubuntu/app"
    WEB_ROOT = "/var/www/html"
    SERVER = "nginx"
}
```

### 4. Configure GitHub Webhook

1. Go to your GitHub repository → Settings → Webhooks
2. Add webhook pointing to: `http://your-jenkins-url/github-webhook/`
3. Select "Just the push event"
4. Save webhook

## Local Development

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Run linter
npm run lint
```

## Deployment Flow

1. **Developer pushes code** to GitHub repository
2. **GitHub webhook triggers** Jenkins pipeline
3. **Jenkins checks out** code from repository
4. **Build stage** installs dependencies and builds the app
5. **Deploy stage**:
   - Connects to EC2 via SSH
   - Creates deployment directory
   - Copies built files
   - Updates Nginx web root
   - Restarts Nginx service
6. **Application is live** on EC2 instance

## Environment Variables

The pipeline uses the following environment variables (configured in Jenkinsfile):

- `EC2_HOST`: IP address or hostname of EC2 instance
- `SSH_CREDENTIAL_ID`: Jenkins credential ID for SSH key
- `REMOTE_USER`: SSH username for EC2 instance
- `REMOTE_PATH`: Temporary deployment path on EC2
- `WEB_ROOT`: Nginx web root directory
- `SERVER`: Web server service name

## Troubleshooting

### Pipeline fails at SSH connection
- Verify EC2 security group allows SSH (port 22) from Jenkins server IP
- Check SSH credentials are correctly configured in Jenkins
- Ensure EC2 instance is running and accessible

### Deployment fails at Nginx restart
- Verify user has sudo privileges on EC2
- Check Nginx configuration is valid: `sudo nginx -t`
- Ensure web root directory exists and has correct permissions

### Build fails
- Check Node.js version compatibility
- Verify all dependencies are listed in `package.json`
- Review build logs for specific errors

## Security Considerations

- Use SSH keys instead of passwords
- Restrict EC2 security group to Jenkins server IP only
- Use IAM roles for EC2 when possible
- Keep Jenkins and dependencies updated
- Review and validate all code before deployment

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test the pipeline locally
5. Submit a pull request

## License

MIT

## Author

Archin Joshi
