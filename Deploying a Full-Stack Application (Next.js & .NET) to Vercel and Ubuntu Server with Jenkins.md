

This document outlines the complete process of deploying a full-stack application consisting of a Next.js frontend and a .NET backend, using Jenkins for Continuous Integration and Continuous Deployment (CI/CD). The frontend will be deployed to Vercel, and the backend to an Ubuntu server.

**Architecture:**

* **Frontend:** Next.js application, deployed to Vercel.
* **Backend:** .NET Web API, containerized with Docker, deployed to an Ubuntu server.
* **Database (Optional):** Deployed to the same Ubuntu server or a separate database server.
* **CI/CD:** Jenkins pipeline for automated builds and deployments.

**Prerequisites:**

1.  **Vercel Account:**
    * A Vercel account with a project created for your Next.js application.
2.  **Ubuntu Server:**
    * An Ubuntu server with Docker installed.
    * SSH access to the Ubuntu server.
3.  **Jenkins Server:**
    * A Jenkins server with the following plugins installed:
        * Git
        * Docker Pipeline
        * SSH Agent
        * Credentials Binding
4.  **Git Repository:**
    * A Git repository containing your Next.js frontend and .NET backend code.
5.  **.NET SDK and Node.js:**
    * The .NET SDK and Node.js installed on your Jenkins server or within your Docker agent.

**Step 1: Vercel Setup**

1.  **Vercel API Token:**
    * Go to your Vercel account settings and generate an API token.
    * In Jenkins, go to Credentials -> System -> Global credentials (unrestricted).
    * Add a "Secret text" credential and paste your Vercel API token into the "Secret" field.
    * Give it an ID (e.g., `vercel-api-token`).
2.  **Vercel Project and Org IDs:**
    * Find your Vercel project ID and organization ID in your Vercel project settings.
    * Note these IDs for use in the Jenkins pipeline.

**Step 2: Ubuntu Server Setup**

1.  **Install Docker:**
    * Ensure Docker is installed on your Ubuntu server.
2.  **SSH Key Pair:**
    * Generate an SSH key pair on your local machine.
    * Copy the public key to your Ubuntu server's `~/.ssh/authorized_keys` file.
    * In Jenkins, go to Credentials -> System -> Global credentials (unrestricted).
    * Add an "SSH Username with private key" credential and paste your private key into the "Private Key" field.
    * Give it an ID (e.g., `ubuntu-server-ssh`).
3.  **Firewall:**
    * Configure your Ubuntu server's firewall to allow traffic on the port your .NET backend will use (e.g., port 5000).

**Step 3: Jenkins Pipeline (Jenkinsfile)**

Create a `Jenkinsfile` in the root of your Git repository with the following content:

```groovy
pipeline {
    agent any

    environment {
        VERCEL_TOKEN = credentials('vercel-api-token')
        VERCEL_PROJECT_ID = 'your-vercel-project-id'
        VERCEL_ORG_ID = 'your-vercel-org-id'
        BACKEND_DOCKER_IMAGE = 'your-backend-image:latest' // Replace
        SERVER_USER = 'your-server-user' // Replace
        SERVER_HOST = 'your-server-ip-or-hostname' // Replace
        SERVER_SSH_CREDENTIALS = credentials('ubuntu-server-ssh')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master', credentialsId: 'github-pat', url: '[https://github.com/PrathmeshnPawar/aimsr-website.git](https://github.com/PrathmeshnPawar/aimsr-website.git)' //Replace
            }
        }

        stage('Build Next.js Frontend') {
            steps {
                sh """
                    cd frontend
                    npm install
                    npm run build
                """
            }
        }

        stage('Deploy Frontend to Vercel') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'vercel-api-token', variable: 'VERCEL_TOKEN')]) {
                        sh """
                            npm install -g vercel
                            vercel deploy --prebuilt --token=\<span class="math-inline">VERCEL\_TOKEN \-\-projectId\=\\$VERCEL\_PROJECT\_ID \-\-orgId\=\\$VERCEL\_ORG\_ID
"""
\}
\}
\}
\}
stage\('Build \.NET Backend Docker Image'\) \{
steps \{
script \{
docker\.build\(env\.BACKEND\_DOCKER\_IMAGE, "\./backend"\)
\}
\}
\}
stage\('Deploy Backend to Ubuntu Server'\) \{
steps \{
script \{
withCredentials\(\[sshUserPrivateKey\(credentialsId\: 'ubuntu\-server\-ssh', keyFileVariable\: 'SSH\_PRIVATE\_KEY', usernameVariable\: 'SSH\_USER'\)\]\) \{
sh """
ssh \-o StrictHostKeyChecking\=no \-i \\$SSH\_PRIVATE\_KEY \\$SSH\_USER@\\$SERVER\_HOST <<EOF
docker pull \\$\{env\.BACKEND\_DOCKER\_IMAGE\}
docker stop backend\-container \|\| true
docker rm backend\-container \|\| true
docker run \-d \-\-name backend\-container \-p 5000\:5000 \\</span>{env.BACKEND_DOCKER_IMAGE}
                            EOF
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo '🎉 Deployment Successful!'
        }
        failure {
            echo '❌ Deployment Failed!'
        }
    }
}