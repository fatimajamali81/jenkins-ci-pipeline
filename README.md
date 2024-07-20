# Jenkins IIS Deployment Pipeline

This repository contains a Jenkins pipeline for deploying a web application to an IIS server on Windows. The pipeline automates the process of checking out the code, restoring packages, building the application, and deploying it to IIS with maintenance mode handling.

## Features

- **Automated Checkout**: Pulls the latest code from the GitHub repository.
- **Package Restoration**: Restores .NET packages.
- **Build Automation**: Builds the application in Release mode.
- **Deployment**: Publishes the build to a specified directory.
- **IIS Management**: Stops and starts IIS sites and application pools.
- **Maintenance Mode**: Switches to a maintenance site during deployment.
- **Email Notifications**: Sends email notifications on build completion.

## Prerequisites

- **Jenkins**: Installed on a Windows server.
- **.NET SDK**: Installed on the Jenkins server.
- **IIS**: Installed and configured with the "working" and "maintenance" sites.
- **Credentials**: Configured in Jenkins for GitHub, IIS server, and email notifications.

## Pipeline Overview

### Stages

1. **Checkout Stage**: Checks out the code from the GitHub repository.
2. **Restore Packages**: Restores the .NET packages required for the project.
3. **Build Stage**: Builds the project in Release configuration.
4. **Deploy Stage**: Publishes the build to a specified directory.
5. **Stop Site**: Stops the IIS site and application pool for the working site.
6. **Start Maintenance Site**: Starts the maintenance site and its application pool.
7. **Publish Stage**: Copies the published build to the IIS server.
8. **Stop Maintenance Site**: Stops the maintenance site and its application pool.
9. **Start Site**: Starts the working site and its application pool.

## Jenkinsfile Configuration

### Environment Variables

- **`GIT_ACCESS_TOKEN`**: GitHub access token for cloning the repository.
- **`INSTANCE_CREDS`**: Jenkins credentials ID for IIS server access (username and password).
- **`INSTANCE_HOST`**: Jenkins credentials ID for the IIS server hostname.
- **`EMAIL_RECIPIENT`**: Email address to receive build notifications.

### Pipeline Stages Explained

1. **Checkout Stage**: 
   - Uses `GIT_ACCESS_TOKEN` to clone the repository.
   - Replace `your-username` and `your-repo` with your actual GitHub username and repository name.

2. **Restore Packages**:
   - Restores .NET packages from the project directory.

3. **Build Stage**:
   - Builds the .NET project in Release configuration.

4. **Deploy Stage**:
   - Publishes the .NET project to a specified directory.

5. **Stop Site**:
   - Stops the IIS site and application pool for the "working" site to prepare for deployment.

6. **Start Maintenance Site**:
   - Starts the "maintenance" site and its application pool to display maintenance information.

7. **Publish Stage**:
   - Copies the published build files to the IIS server using network share credentials.

8. **Stop Maintenance Site**:
   - Stops the "maintenance" site and its application pool after deployment is complete.

9. **Start Site**:
   - Restarts the "working" site and its application pool to go live with the new deployment.

## Setup Instructions

1. **Clone the Repository**:
    ```bash
    git clone https://github.com/your-username/jenkins-iis-cicd-pipeline.git
    cd jenkins-iis-cicd-pipeline
    ```

2. **Create a Jenkins Job**:
    - Open Jenkins and create a new Pipeline job.
    - In the Pipeline section, configure it to use this repository.
    - Set the script path to `Jenkinsfile`.

3. **Configure Jenkins Credentials**:
    - Add credentials in Jenkins for `GIT_ACCESS_TOKEN`, `INSTANCE_CREDS`, `INSTANCE_HOST`, and email notifications.

4. **Verify Configuration**:
    - Ensure that all environment variables and credentials are properly set.
    - Test the pipeline by triggering a build to ensure that each stage executes as expected.

## License

This project is licensed under the MIT License.
