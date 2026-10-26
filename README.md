# Automated Docker Image Publishing to Amazon ECR

CI/CD pipeline that builds versioned Docker images, publishes them to
Amazon ECR, and deploys a selected version to EC2.

## Architecture
(paste the mermaid block here)

## Tech stack
Docker, Amazon ECR, GitHub Actions, AWS IAM, EC2, Python (Flask)

## How it works
1. Push to `main` triggers the build workflow
2. Image is tagged with the short commit SHA and pushed to ECR
3. Deploy workflow takes an image tag as input, pulls it on EC2, and runs it
4. A /health check fails the deploy if the new version is unhealthy

## Security decisions
- Least-privilege IAM: CI user can push to one repo only
- EC2 uses an IAM role (read-only ECR), with no keys stored on the server
- Immutable tags and scan-on-push enabled on ECR
- Secrets stored in GitHub Secrets, never in code
- ECR lifecycle policy keeps the last 10 images (cost control)

## Screenshots
(add your 6 images with 

![alt](docs/file.png)

)

## Challenges I solved
- Disk full on EC2 (8 GB): resized the EBS volume online with growpart and resize2fs
- Pipeline failures: missing region variable and credentials, debugged from Actions logs

## Future improvements
- Replace access keys with GitHub OIDC
- Blue/green or rolling deploys (ECS/EKS)
- Provision infrastructure with Terraform
- Use SSM instead of open SSH

## Run locally
docker build -t ecr-demo . && docker run -p 5000:5000 ecr-demo
