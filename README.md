<div align="center">

# ⚙️ AWS CI/CD Pipeline — GitHub Actions

### Push-to-deploy automation for a static site on Amazon S3

[![GitHub Actions](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-2088FF?style=flat&logo=github-actions&logoColor=white)](https://github.com/features/actions)
[![AWS](https://img.shields.io/badge/AWS-S3-FF9900?style=flat&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/s3/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

</div>

---

## 📌 Overview

A fully automated deployment pipeline — every push to `main` triggers GitHub Actions to build and sync the site directly to an S3 bucket. No manual upload, no manual script execution. This closes the loop on the manual deployment workflows used in earlier projects (`aws-s3-website-boto3`, `aws-portfolio-docker-deployment`).

The page itself is a live **deployment console** displaying all four cloud projects as "modules," styled after a mission-control instrument panel.

---

## ⚙️ How the Pipeline Works

git push (main)
│
▼
GitHub Actions triggered
│
▼
Spins up a temporary Ubuntu runner
│
▼
Authenticates with AWS using encrypted secrets
│
▼
Syncs index.html → S3 bucket
│
▼
Live site updated — zero manual steps
---

## 🔐 Security Model

AWS credentials are never hardcoded. They're stored as **encrypted GitHub Secrets**, injected into the workflow only at runtime:

| Secret | Purpose |
|---|---|
| `AWS_ACCESS_KEY_ID` | IAM authentication |
| `AWS_SECRET_ACCESS_KEY` | IAM authentication |
| `AWS_REGION` | Target AWS region (`eu-north-1`) |

---

## 📄 Workflow File

```yaml
name: Deploy to S3

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}

      - name: Sync to S3
        run: aws s3 cp index.html s3://muqsit-portfolio-site/index.html --content-type text/html
```

---

## 💡 Key Concepts Demonstrated

- Writing and structuring GitHub Actions workflows (YAML)
- Securing credentials with encrypted repository secrets
- Event-driven automation (`on: push`)
- Resolving unrelated Git histories during merge conflicts
- End-to-end deployment automation — no manual intervention

---

## 🔮 Future Improvements

- [ ] Add a build/test step before deployment
- [ ] Deploy to multiple environments (staging/production)
- [ ] Slack/email notification on deployment success or failure
- [ ] Extend pipeline to deploy Docker images to ECR/ECS

---

## 👤 Author

**Abdul Muqsit Sayed**
CSE (AI/ML) Student | Cloud Computing Enthusiast
[GitHub](https://github.com/sayedabdulmuqsit) • [Portfolio](http://muqsit-portfolio-site.s3-website.eu-north-1.amazonaws.com)
