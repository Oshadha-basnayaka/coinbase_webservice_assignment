# RunBook for Deploying and Testing Flask Microservices

## Overview

This RunBook outlines the steps to deploy and test a Python Flask microservices application using Docker, Kubernetes (AWS EKS), and GitHub Actions. It includes setting up the environment, configuring CI/CD pipelines, and executing tests.

## Prerequisites

- **Python Version:** 3.12 or higher
- **Flask Version:** 2.0.3
- **Werkzeug Version:** 2.0.3
- **pytest Version:** 7.4.2
- **Flask-JWT-Extended Version:** 4.3.1
- **Docker**
- **Kubernetes (AWS EKS)**
- **GitHub Actions**

## Project Structure

- `services/`
   - `crypto-price-service/`
    - `app.py`
    - `Dockerfile`
    - `requirements.txt`
    - `__init__.py`
    - `test/`
      - `__init__.py`
      - `test_app.py`
  - `transaction-service/`
    - `app.py`
    - `Dockerfile`
    - `requirements.txt`
    - `__init__.py`
    - `test/`
      - `__init__.py`
      - `test_app.py`
  - `account-service/`
    - `app.py`
    - `Dockerfile`
    - `requirements.txt`
    - `__init__.py`
    - `test/`
      - `__init__.py`
      - `test_app.py`
- `kubernetes/`
  - `blue-green/`
    - `crypto-price-service-blue-deployment.yaml`
    - `crypto-price-service-green-deployment.yaml`
    - `transaction-service-blue-deployment.yaml`
    - `transaction-service-green-deployment.yaml`
    - `account-service-blue-deployment.yaml`
    - `account-service-green-deployment.yaml`
    - `crypto-price-service-service.yaml`
    - `transaction-service-service.yaml`
    - `account-service-blue-service.yaml`
  - `namespace.yaml`
- `.github/`
  - `workflows/`
    - `ci-cd-pipeline.yml`

## Environment Setup

1. **Clone the Repository**

   ```bash
   git clone https://github.com/NethmiKaumudi/coinbase-exchange-solution-NK.git
   cd coinbase-exchange-solution-NK
