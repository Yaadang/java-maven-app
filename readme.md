Project Description


Pre-requisites

- A Jenkins server and Kubernetes Cluster using eksctl

Setup Steps

Install kubectl inside Jenkins Container:
    SSH into your Jenkins server.
    Access the Jenkins container:

bash
    docker exec -it [jenkins_container_id] /bin/bash

Install kubectl:

bash

    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    chmod +x kubectl
    mv kubectl /usr/local/bin/

Install aws-iam-authenticator inside Jenkins Container:

    Still inside the Jenkins container, install aws-iam-authenticator:

    bash

    curl -o aws-iam-authenticator https://amazon-eks.s3.us-west-2.amazonaws.com/1.16.8/2020-04-16/bin/linux/amd64/aws-iam-authenticator
    chmod +x ./aws-iam-authenticator
    mv ./aws-iam-authenticator /usr/local/bin/

Set up Kubernetes Configuration:

    Outside of the Jenkins container, create your .kube/config.
    Copy the config file into the Jenkins container:

    bash

    docker cp .kube/config [jenkins_container_id]:/var/jenkins_home/.kube/config

Create an ECR Repository:

    Navigate to the AWS ECR console.
    Click on "Create Repository" and follow the on-screen instructions.

Create Jenkins Credentials:
    Navigate to your Jenkins dashboard.
    Go to 'Manage Jenkins' > 'Manage Credentials'.
    Add a new global credential with the ECR repository details named ecr-credentials.

Set up AWS ECR Registry Secret in EKS:

    Create a Kubernetes secret for the AWS ECR registry. This helps in pulling images from private ECR repositories.

    bash

    kubectl create secret my-registry-key \
    --docker-server= [ECR_REPO_URL] \
    --docker-username=AWS \
    --docker-password= [AUTH_TOKEN]

    Adjust the secret reference in your deployment.yaml file to reflect the above-created secret.

Update Jenkinsfile:

    Modify Jenkinsfile to cater to the ECR setup and deployment.
    Use the Deployment and service files to connect, build, version increment and deploy to the EKS cluster that it created and connected to

Execute the Jenkins Pipeline:

    Navigate back to your Jenkins dashboard.
    Find your pipeline and execute it.