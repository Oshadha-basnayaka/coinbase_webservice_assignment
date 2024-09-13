Coinbase Crypto Exchange - Runbook
Overview
This runbook provides step-by-step instructions for managing and monitoring services for the Coinbase crypto exchange deployed using Kubernetes on AWS EKS. It includes deployment, testing, rollback procedures, and CI/CD pipeline monitoring.

Table of Contents
Environment Setup
Deployment
Monitoring and Validation
Automated and Manual Testing
Rollback Procedure
CI/CD Pipeline Management
Troubleshooting
Environment Setup
Prerequisites
AWS EKS Cluster: Ensure the EKS cluster is up and running, with appropriate permissions.
Docker Hub Account: Docker images are stored and pulled from your Docker Hub registry (e.g., docker.io/bazza69/your-images).
GitHub Repository: Code and deployment files are managed here. CI/CD actions trigger on commits to the main branch.
Secrets: Ensure Docker credentials and kubeconfig are stored in GitHub Secrets for secure pipeline execution.
Tools & Access
kubectl: Command-line tool for Kubernetes interaction.
AWS CLI: Required for AWS resource management.
GitHub Actions: Pipeline for CI/CD tasks.
Deployment
Build and Push Docker Images
Trigger Build:
Commit changes to the main branch or open a pull request, which will trigger the CI/CD pipeline.
Pipeline Steps:
Code is checked out.
Docker images for each service (e.g., crypto-price-service, transaction-service, account-service) are built.
Images are pushed to your Docker Hub repository (e.g., bazza69).
Deploying Services to AWS EKS
kubeconfig Setup:

Ensure kubectl is configured for the correct Kubernetes context and AWS EKS cluster.
Example command to configure kubeconfig:
bash
Copy code
aws eks --region <your-region> update-kubeconfig --name <eks-cluster-name>
Blue-Green Deployment:

Deploy Blue Environment:
bash
Copy code
kubectl apply -f kubernetes/blue-green/crypto-price-service-blue-deployment.yaml --namespace=default
kubectl apply -f kubernetes/blue-green/transaction-service-blue-deployment.yaml --namespace=default
kubectl apply -f kubernetes/blue-green/account-service-blue-deployment.yaml --namespace=default
Deploy Green Environment:
bash
Copy code
kubectl apply -f kubernetes/blue-green/crypto-price-service-green-deployment.yaml --namespace=default
kubectl apply -f kubernetes/blue-green/transaction-service-green-deployment.yaml --namespace=default
kubectl apply -f kubernetes/blue-green/account-service-green-deployment.yaml --namespace=default
Switch Traffic to Green:
bash
Copy code
kubectl patch service crypto-price-service -p '{"spec":{"selector":{"version":"green"}}}' --namespace=default
kubectl patch service transaction-service -p '{"spec":{"selector":{"version":"green"}}}' --namespace=default
kubectl patch service account-service -p '{"spec":{"selector":{"version":"green"}}}' --namespace=default
Monitoring and Validation
Service Monitoring
Check Services:

Ensure all services are up and running:
bash
Copy code
kubectl get services --namespace=default
Check Pods:

Monitor the status of the deployed pods:
bash
Copy code
kubectl get pods --namespace=default
Validate Endpoints:

Confirm service functionality by hitting the service endpoints:
bash
Copy code
curl -f http://<service-ip>:<port>/crypto-prices
curl -f http://<service-ip>:<port>/transactions
curl -f http://<service-ip>:<port>/accounts
Automated and Manual Testing
Automated Tests
CI/CD Pipeline Tests: Integration tests will be automatically triggered upon deployment completion. These tests will check service accessibility, response times, and correct data handling.
Manual Testing
Perform manual testing by accessing the deployed services using the respective service IPs and ports. The commands below will help with the verification:
bash
Copy code
curl -f http://<service-ip>:<port>/crypto-prices
curl -f http://<service-ip>:<port>/transactions
curl -f http://<service-ip>:<port>/accounts
Rollback Procedure
Switch Traffic Back to Blue: If the Green deployment fails, you can revert the traffic to the Blue environment:

bash
Copy code
kubectl patch service crypto-price-service -p '{"spec":{"selector":{"version":"blue"}}}' --namespace=default
kubectl patch service transaction-service -p '{"spec":{"selector":{"version":"blue"}}}' --namespace=default
kubectl patch service account-service -p '{"spec":{"selector":{"version":"blue"}}}' --namespace=default
Monitor Rollback: Check that the services are now routing traffic to the Blue pods:

bash
Copy code
kubectl get services --namespace=default
kubectl get pods --namespace=default
CI/CD Pipeline Management
GitHub Actions Monitoring
Triggering Pipeline: The CI/CD pipeline is automatically triggered by any changes to the main branch. Ensure that Docker images are successfully built and pushed to Docker Hub.

Checking Pipeline Status: Review the status of the pipeline in the GitHub Actions tab. Logs for each step (checkout, build, push, deploy) can be checked here.

Pipeline Troubleshooting:

If any stage of the CI/CD process fails, check the logs to identify the issue (e.g., authentication failure, failed tests).
Retry or fix issues in the pipeline as necessary.
Troubleshooting
Common Issues
Pods Not Starting:

Check logs for the affected pod:
bash
Copy code
kubectl logs <pod-name> --namespace=default
Check for issues with image pulls, resource allocation, or incorrect environment variables.
Service Not Accessible:

Verify if the service IP is reachable:
bash
Copy code
kubectl get services --namespace=default
Check security group rules in AWS, ensuring that the necessary ports are open.
Failed Tests:

Investigate failed integration or unit tests by reviewing the logs in the GitHub Actions tab.
Useful Commands
Restarting Pods:

bash
Copy code
kubectl rollout restart deployment/<service-name> --namespace=default
Viewing Logs:

bash
Copy code
kubectl logs <pod-name> --namespace=default