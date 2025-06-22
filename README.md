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

- **Tool:** kubeadm

- **Installation steps & config:** Please check the following [README.md](kubeadm/README.md)

- **Short description** This repo showcases the Web application deployment in k8s together with 
DevOps tools such as K8s, Jenkins, ArgoCD, Prometheus, ...

- **System validation logs:**
    ````shell
    kubectl get nodes -o wide
    kubectl get pods -A -o wide
    ````
- **Screenshots:**

- ![Nodes list](asset/1.0.init.png)
- ![Pods list](asset/1.1.init-2.png)

---

## 2. ArgoCD & Jenkins Setup

### ArgoCD
  * Manifest: [ArgoCD Helm Chart](charts/web)
  * Installing Manifest
  ````shell
  kubectl create namespace argocd
  kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
  ````
  - Check deployment
  ````shell
  k get all -n argocd
  ````
  ![alt text](image_argo_ns.png)
  ![ArgoCD UI](asset/1.2.argo-startup.png)
### Jenkin
  * Manifest: [Jenkins Helm Chart](charts/api)

  * Installing Manifest
  ````shell
  cd jenkins
  kubectl apply -f jenkins-ns.yaml
  kubectl apply -f jenkins-pv.yaml
  kubectl apply -f jenkins-sa.yaml
  kubectl apply -f jenkins-deployment.yaml
  kubectl apply -f jenkins-service.yaml
  ````
  - Check deployment
  ````shell
  k get all -n jenkins
  ````

  ![alt text](image_jenkins_ns.png)
  ![ArgoCD UI](asset/1.3.jenkins-startup.png)

## Application on ArgoCD
### Description

  * Backend Helm Chart and values file: [click](https://github.com/Fat1512/VDT-Backend-Config)

  * Frontend Helm Chart and values file: [click](https://github.com/Fat1512/VDT-Frontend-Config)

  * I have 1 frontend and 2 service for backend as below

  ![alt text](asset/1.4.argo-startup-overview.png)
  ![alt text](asset/1.5.argo-startup-auth.png)
  ![alt text](asset/1.6.argo-startup-crud.png)
  ![alt text](asset/1.7.argo-startup-frontend.png)
### App Demo
  ![alt text](asset/1.9.frontend-demo.png)
  ![alt text](asset/1.8.backend-demo.png)



## 3. CI/CD
### Jenkinsfile
* Backend Jenkinsfile: [click](https://github.com/Fat1512/VDT-Backend-Config)

* Frontend Jenkinsfile: [click](https://github.com/Fat1512/VDT-Frontend-Config)
### Build log

* Make changes to title on frontend

![web build log](asset/2.0.source-code-change.png)

* Pipeline triggered on commit changes

![api build log](asset/2.2.pipeline-output-1.png)
![api build log](asset/2.3.pipeline-output-2.png)
![api build log](asset/2.4.pipeline-output-3.png)

* Pipeline triggered on commit changes
### Stage view

![stage](asset/2.5.jenkins-frontend.png)


![stage](asset/2.6.jenkins-backend.png)

### Changes in CD
* CD repo and Dockerhub get updated

![change](asset/2.7.updated-cd.png)
![change](asset/2.8.updated-dockerhub.png)

* ArgoCD noticed the change

![change](asset/2.9.updated-argo.png)

### Before and After
![change](asset/2.10.updated-before.png)
![change](asset/2.11.updated-after.png)



## 4. Monitoring
### Prometheus:  

  ````shell
    ansible-playbook -i inventory.ini deploy-prometheus.yml
  ````
![Prometheus UI](asset/3.1.setup.png)  
- [prometheus setup](prometheus)
- **UI & Target list:**  
    ![Prometheus Targets](images/prometheus_targets.png)

---

## 5. Security

<!-- ### HAProxy Load Balancer & Ingress

- **HAProxy config:** [haproxy.cfg](haproxy/haproxy.cfg)
- **Ingress config:** [ingress.yaml](k8s/ingress.yaml)
- **HTTPS Access:**  
    - Web: `https://<LB-IP>:3001/`
    - API: `https://<LB-IP>:8081/`
    - ![HAProxy Web](images/haproxy_web.png)
    - ![HAProxy API](images/haproxy_api.png) -->

### Rate Limiting

- **Rate limiting config:** See [rateLimitation.md](docs/rateLimitation.md)
- **Test result:**  
    ![Rate Limit](images/rate_limit.png)


### Authentication & Authorization

- **API protected endpoints:** See [api repo](https://github.com/[your_api_repo])
- **Role-based access screenshots:**  
    ![Admin Access](images/admin_access.png)
    ![User Access](images/user_access.png)
