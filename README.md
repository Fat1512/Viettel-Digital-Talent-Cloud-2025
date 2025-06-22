# Viettel Digital Talent 2025 - Capstone Project

## Table of Contents
- [0. Requirements](#0-requirements)
- [1. Kubernetes Deployment](#1-kubernetes-deployment)
- [2. Web Application & DevOps Practices](#2-web-application--devops-practices)
- [3. Containerization](#3-containerization)
- [4. Continuous Integration & Delivery](#4-continuous-integration--delivery)
- [5. Automation](#5-automation)
- [6. Monitoring](#6-monitoring)
- [7. Logging](#7-logging)
- [8. Security](#8-security)

## 1. Kubernetes Deployment

**Tool Used:** kubeadm

**Installation Steps & Configuration:**  
  Please refer to the detailed [kubeadm/README.md](kubeadm/README.md) for comprehensive setup instructions.

**Overview:**  
  This repository demonstrates the deployment of a complete web application ecosystem within a Kubernetes cluster. It integrates various DevOps tools, including Kubernetes itself, Jenkins for CI/CD, ArgoCD for GitOps-based delivery, and Prometheus for monitoring. The aim is to provide an end-to-end platform for scalable and robust cloud-native applications.

- **System Validation Logs:**
    ```shell
    kubectl get nodes -o wide
    kubectl get pods -A -o wide
    ```

- **Screenshots:**

- ![Nodes list](asset/1.0.init.png)
- ![Pods list](asset/1.1.init-2.png)


## 2. ArgoCD & Jenkins Setup

### ArgoCD
  * **Manifest:** [ArgoCD Helm Chart](charts/web)
  * **ArgoCD Service Address:**   192.168.113.111:32489
  * **Install ArgoCD:**
  ```shell
  kubectl create namespace argocd
  kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
  ```
  * **Check Deployment:**
  ```shell
  # Patch the argocd-server service to change its type from ClusterIP to NodePort.
  kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
  
  # Retrieve password
  kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
  
  kubectl get all -n argocd
  ```
  ![ArgoCD Namespace](image_argo_ns.png)
  ![ArgoCD UI](asset/1.2.argo-startup.png)

### Jenkins
  * **Manifest:** [Jenkins Helm Chart](charts/api)
  * **Jenkins Service Address:**  192.168.113.111:32474
  * **Install Jenkins:**
  ```shell
  cd jenkins
  kubectl apply -f jenkins-ns.yaml
  kubectl apply -f jenkins-pv.yaml
  kubectl apply -f jenkins-sa.yaml
  kubectl apply -f jenkins-deployment.yaml
  kubectl apply -f jenkins-service.yaml
  ```
  - **Check Deployment:**
  ```shell
  kubectl get all -n jenkins
  ```
  ![Jenkins Namespace](image_jenkins_ns.png)
  ![Jenkins Startup](asset/1.3.jenkins-startup.png)


## 3. Application Deployment via ArgoCD

### Description
  - Backend Helm Chart and values file: [Backend Config](https://github.com/Fat1512/VDT-Backend-Config)
  
  - Frontend Helm Chart and values file: [Frontend Config](https://github.com/Fat1512/VDT-Frontend-Config)
  
  - The application consists of one frontend service and two backend services.

### Application  

  ```shell
    kubectl get svc -n vdt
  ```

  ![ArgoCD Overview](image_web_service.png)

  - Frontend Service: 192.168.113.111:32647
   
  - Auth Service: 192.168.113.111:30101
  
  - Crud Service: 192.168.113.111:30102

  ![ArgoCD Overview](asset/1.4.argo-startup-overview.png)
  ![Auth Service](asset/1.5.argo-startup-auth.png)
  ![CRUD Service](asset/1.6.argo-startup-crud.png)
  ![Frontend Service](asset/1.7.argo-startup-frontend.png)

### Demo
  ![Frontend Demo](asset/1.9.frontend-demo.png)
  ![Backend Demo](asset/1.8.backend-demo.png)

---

## 4. CI/CD

### Jenkinsfile
* Backend Jenkinsfile: [Backend Jenkinsfile](https://github.com/Fat1512/VDT-Backend-Config)
* Frontend Jenkinsfile: [Frontend Jenkinsfile](https://github.com/Fat1512/VDT-Frontend-Config)

### Build Logs
* Reference: [Log file](log/pipeline_log.txt)
* Example: Update frontend title and observe pipeline triggers.

![Web Build Log](asset/2.0.source-code-change.png)

* Pipeline is automatically triggered on commit changes

![Pipeline Output 1](asset/2.2.pipeline-output-1.png)
![Pipeline Output 2](asset/2.3.pipeline-output-2.png)
![Pipeline Output 3](asset/2.4.pipeline-output-3.png)

### Stage View

![Frontend Stage](asset/2.5.jenkins-frontend.png)
![Backend Stage](asset/2.6.jenkins-backend.png)

### Continuous Delivery Changes

* CD repository and DockerHub images get updated automatically after successful builds.

![CD Updated](asset/2.7.updated-cd.png)
![DockerHub Updated](asset/2.8.updated-dockerhub.png)

* ArgoCD automatically detects and synchronizes changes.

![ArgoCD Updated](asset/2.9.updated-argo.png)

### Before and After

![Before Update](asset/2.10.updated-before.png)
![After Update](asset/2.11.updated-after.png)


## 5. Monitoring

### Prometheus

  * **Prometheus Address:**  
    _Insert your Prometheus UI address here:_  
    `Prometheus UI Address: _____________________________`

  * Deploy Prometheus using Ansible:
    ```shell
    ansible-playbook -i inventory.ini deploy-prometheus.yml
    ```

  ![Prometheus UI](asset/3.1.setup.png)  
  - [Prometheus Setup Details](prometheus)

  - **UI & Target List:**  
    ![Prometheus Targets](images/prometheus_targets.png)

  * **Additional Kubernetes Monitoring Commands:**
    ```shell
    # Get all services in monitoring namespace
    kubectl get svc -n monitoring
    # Port-forward Prometheus
    kubectl port-forward svc/prometheus-server -n monitoring 9090:9090
    ```

---

## 6. Security

### Rate Limiting

- **Rate Limiting Configuration:** See [rateLimitation.md](docs/rateLimitation.md)
- **API Gateway or Rate Limiter Address:**  
  _Insert rate limiter endpoint here:_  
  `Rate Limiter Address: _____________________________`
- **Test Result:**  
    ![Rate Limit](images/rate_limit.png)

### Authentication & Authorization

- **API Protected Endpoints:** See [API repo](https://github.com/[your_api_repo])
- **Section for API Endpoint Address:**  
  _Insert your API base URL here:_  
  `API Base URL: _____________________________`
- **Role-based Access Screenshots:**  
    ![Admin Access](images/admin_access.png)
    ![User Access](images/user_access.png)
