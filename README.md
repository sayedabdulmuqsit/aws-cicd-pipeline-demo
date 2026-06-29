<div align="center">

# ⚙️ AWS CI/CD Pipeline — GitHub Actions

### Push-to-deploy automation for a static site on Amazon S3

[![GitHub Actions](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-2088FF?style=flat&logo=github-actions&logoColor=white)](https://github.com/features/actions)
[![AWS](https://img.shields.io/badge/AWS-S3-FF9900?style=flat&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/s3/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

**[→ View Live Console](http://muqsit-cicd-demo.s3-website.eu-north-1.amazonaws.com)**

</div>

---

## 📌 Overview

A fully automated deployment pipeline — every push to `main` triggers GitHub Actions to sync the site directly to a dedicated S3 bucket. No manual upload, no manual script execution. This closes the loop on the manual deployment workflows used in earlier projects (`aws-s3-website-boto3`, `aws-portfolio-docker-deployment`).

The page itself is a live **deployment console**, styled after a mission-control instrument panel, listing all four cloud projects as "modules."

> **Note on bucket isolation:** This pipeline deploys to its own bucket (`muqsit-cicd-demo`), kept separate from the main portfolio bucket. Early in development, this workflow briefly pointed at the portfolio bucket and overwrote it on deploy — a real reminder that CI/CD targets need their own isolated destination, never a shared production resource.

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
Syncs index.html → dedicated S3 bucket
│
▼
Live site updated — zero manual steps after push
**What's automatic vs. manual:** committing and pushing code is still a manual, human action — that's how the change reaches GitHub in the first place. Everything *after* the push (build, authenticate, deploy) runs without any further intervention.

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
        run: aws s3 cp index.html s3://muqsit-cicd-demo/index.html --content-type text/html
```

---

## 💡 Key Concepts Demonstrated

- Writing and structuring GitHub Actions workflows (YAML)
- Securing credentials with encrypted repository secrets
- Event-driven automation (`on: push`)
- Resolving unrelated Git histories during merge
- Isolating deployment targets to avoid overwriting production resources
- End-to-end deployment automation with a single manual step (`git push`)

---

## 🔮 Future Improvements

- [ ] Replace public bucket access with CloudFront + Origin Access Control
- [ ] Add a build/test step before deployment
- [ ] Deploy to multiple environments (staging/production)
- [ ] Slack/email notification on deployment success or failure
- [ ] Extend pipeline to deploy Docker images to ECR/ECS

---

## 👤 Author

**Abdul Muqsit Sayed**
CSE (AI/ML) Student | Cloud Computing Enthusiast
[GitHub](https://github.com/sayedabdulmuqsit) • [Portfolio](http://muqsit-portfolio-site.s3-website.eu-north-1.amazonaws.com)
