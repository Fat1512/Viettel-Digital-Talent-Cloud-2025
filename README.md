# Viettel Digital Talent 2025 - Capstone Project

## Table of Contents

- [1. Kubernetes Deployment](#1-kubernetes-deployment)
- [2. ArgoCD & Jenkins Setup](#2-argocd--jenkins-setup)
- [3. Application Deployment via ArgoCD](#3-application-deployment-via-argocd)
- [4. CI/CD](#4-cicd)
- [5. Monitoring](#5-monitoring)
- [6. Logging](#6-logging)
- [7. Security](#7-security)

## 1. Kubernetes Deployment

**Tool:** kubeadm

**Installation Steps & Configuration**  
  Please refer to the detailed [kubeadm/README.md](kubeadm/README.md) for comprehensive setup instructions.

**Server Address:**
- Master: __192.168.111.111__  
- Worker 1: __192.168.111.112__  
- Worker 2: __192.168.111.113__
- Monitor: __192.168.111.114__

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
  ![ArgoCD Namespace](asset/image_argo_ns.png)
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
  ![Jenkins Namespace](asset/image_jenkins_ns.png)
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

  ![ArgoCD Overview](asset/image_web_service.png)

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
  
  * Deploy Prometheus using Ansible:

  ```shell
  ansible-playbook -i inventory.ini deploy-prometheus.yml
  ```

  ![Prometheus UI](asset/3.1.setup.png)

  * SSH into monitoring server and check status to ensure that container is up:

  ```shell
    docker ps
  ```
  ![Prometheus UI](asset/prometheus_monitoring.png)

  * API service monitoring:

  ![Prometheus UI](asset/3.2.show-metrics.png)
  ![Prometheus UI](asset/3.3.metric-1.png)
  ![Prometheus UI](asset/3.4.metric-2.png)

## 6. Logging 
  - To be continued

## 7. Security

### HAProxy
  - To be continued

### Authentication & Authorization

- **Solution Reference:**[Log file](log/pipeline_log.txt)
- **Security App Config:**

![Admin Access](asset/4.0-security-config.png)

- **User role is allowed to get**

![Admin Access](asset/4.0-user-get.png)

- **User role is forbidden to perform post**

![Admin Access](asset/4.0-user-post.png)

- **User role is forbidden to perform delete**

![Admin Access](asset/4.0-user-delete.png)


- **Admin role is allowed to get**

![Admin Access](asset/4.0-user-get.png)

- **User role is allowed to perform post**

![Admin Access](asset/4.0-user-post.png)

- **User role is allowed to perform post**

![Admin Access](asset/4.0-user-delete.png)


### Rate Limiting

- **Rate Limiting Configuration:** See [rateLimitation.md](docs/rateLimitation.md)

**Overview:**  
  The API endpoint /api/v1/students allows clients to retrieve student records. To prevent abuse and overuse, a token bucket algorithm is used to limit the number of requests.

![Admin Access](asset/7.0.png)

- __Capacity__: 10 tokens

- __Refill Rate__: 10 tokens per minute

- This means each client can send up to 10 requests per minute.

![Admin Access](asset/7.1.png)

![Admin Access](asset/7.2.png)

![Admin Access](asset/7.3.png)

- Once all tokens are used, further requests are blocked until the bucket refills after 1 minute.

*Note*: There may be slight overlaps in token consumption due to concurrency (multiple threads accessing the bucket simultaneously), so the effective limit might occasionally exceed 10 requests per minute.