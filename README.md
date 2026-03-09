# Ogofi DevOps CI/CD Pipeline

## Project Overview

This project demonstrates a complete CI/CD pipeline that automates building, pushing, and deploying a containerized web application using Jenkins, Docker, Docker Hub, Kubernetes, and Minikube.

The pipeline automatically builds a Docker image, pushes it to Docker Hub, updates a Kubernetes deployment, and exposes the application through a NodePort service.

---

## Architecture

Developer → GitHub → Jenkins → Docker Build → Docker Hub → Kubernetes Deployment → NodePort Service → Browser

---

## Technologies Used

- Jenkins
- Docker
- Docker Hub
- Kubernetes
- Minikube
- Nginx
- GitHub

---

## CI/CD Pipeline Stages

1. Checkout source code from GitHub
2. Build Docker image
3. Tag Docker image
4. Push image to Docker Hub
5. Apply Kubernetes manifests
6. Update deployment image
7. Verify rollout status
8. Display application URL

---

## Kubernetes Resources

Deployment:
	Ogofi-web
Service:
	Ogofi-web-svc


---

## Application Access

The application is exposed using a NodePort service.


---

## Key DevOps Concepts Demonstrated

- CI/CD automation
- Docker containerization
- Image registry usage
- Kubernetes deployment updates
- Infrastructure automation using Jenkins pipelines

---

## Author

Yinka Fadoju
