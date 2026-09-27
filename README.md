# Microservices Deployment on Kubernetes

A Java-based microservices application deployed on Kubernetes using Docker, Maven, Kubernetes Services, Deployments, health checks, and Minikube.

The project consists of three independent services:

* **Shopfront** – User-facing application
* **Product Catalogue** – Provides product information
* **Stock Manager** – Provides stock information

The Shopfront communicates with the Product Catalogue and Stock Manager services to retrieve and combine application data.

## Architecture

```text
                         User / Browser
                              |
                              v
                    +-------------------+
                    |     Shopfront     |
                    |      :8010        |
                    +---------+---------+
                              |
                    +---------+---------+
                    |                   |
                    v                   v
          +------------------+   +------------------+
          | Product Catalogue|   |  Stock Manager   |
          |      :8020       |   |      :8030       |
          +------------------+   +------------------+
```

Each service is containerized using Docker and deployed as a Kubernetes Deployment with a corresponding Kubernetes Service.

## Technologies

* Java 8
* Apache Maven
* Docker
* Kubernetes
* Minikube
* Kubernetes Services
* Kubernetes Deployments
* Kubernetes YAML
* Spring Boot
* Dropwizard
* Kubernetes health checks

## Project Structure

```text
Microservices-Deployment-on-kubernetes/
│
├── shopfront/
│   ├── src/
│   ├── pom.xml
│   └── Dockerfile
│
├── productcatalogue/
│   ├── src/
│   ├── pom.xml
│   ├── Dockerfile
│   └── product-catalogue.yml
│
├── stockmanager/
│   ├── src/
│   ├── pom.xml
│   └── Dockerfile
│
├── Kubernetes/
│   ├── shopfront-service.yaml
│   ├── productcatalogue-service.yaml
│   └── stockmanager-service.yaml
│
└── README.md
```

## Service Details

| Service           | Technology  | Port | Purpose                      |
| ----------------- | ----------- | ---: | ---------------------------- |
| Shopfront         | Spring Boot | 8010 | User-facing application      |
| Product Catalogue | Dropwizard  | 8020 | Provides product information |
| Stock Manager     | Spring Boot | 8030 | Provides stock information   |

The Product Catalogue also exposes a Dropwizard administration/health endpoint on port `8025`.

## Application Flow

The Shopfront acts as the user-facing service.

When the application retrieves product information:

```text
Shopfront
    |
    +----> Product Catalogue
    |          |
    |          +----> Product information
    |
    +----> Stock Manager
               |
               +----> Stock information
```

The Shopfront combines the product and stock information before presenting it to the user.

The Stock Manager integration also uses a Hystrix fallback mechanism to handle unavailable stock data.

## 1. Build the Applications

Build each service using Maven.

### Shopfront

```bash
cd shopfront
mvn clean install -DskipTests
```

### Product Catalogue

```bash
cd productcatalogue
mvn clean install -DskipTests
```

### Stock Manager

```bash
cd stockmanager
mvn clean install -DskipTests
```

## 2. Build Docker Images

Build the Docker images for each service:

```bash
docker build -t shyamsunder01/shopfront:latest ./shopfront
```

```bash
docker build -t shyamsunder01/productcatalogue:latest ./productcatalogue
```

```bash
docker build -t shyamsunder01/stockmanager:latest ./stockmanager
```

Verify the images:

```bash
docker image ls
```

## 3. Push Images to Docker Hub

Login to Docker Hub:

```bash
docker login
```

Push the images:

```bash
docker push shyamsunder01/shopfront:latest
docker push shyamsunder01/productcatalogue:latest
docker push shyamsunder01/stockmanager:latest
```

## 4. Start Minikube

Start Minikube using the Docker driver:

```bash
minikube start --force --driver=docker
```

Verify the cluster:

```bash
kubectl get nodes
```

## 5. Deploy to Kubernetes

Navigate to the Kubernetes directory:

```bash
cd Kubernetes
```

Apply the Kubernetes manifests:

```bash
kubectl apply -f shopfront-service.yaml
kubectl apply -f productcatalogue-service.yaml
kubectl apply -f stockmanager-service.yaml
```

Verify the deployments:

```bash
kubectl get deployments
```

Check the pods:

```bash
kubectl get pods
```

Check the services:

```bash
kubectl get svc
```

## 6. Verify Application Health

The Kubernetes deployments include HTTP liveness probes.

Shopfront:

```text
/health
```

Product Catalogue:

```text
/healthcheck
```

Stock Manager:

```text
/health
```

The probes allow Kubernetes to monitor whether the application containers are responding correctly.

## 7. Access the Application

The Shopfront is exposed through a Kubernetes `NodePort` service.

You can inspect the assigned service port with:

```bash
kubectl get svc shopfront
```

With Minikube, the service can also be opened using:

```bash
minikube service shopfront --url
```

Open the resulting URL in a browser to access the Shopfront application.

## 8. Kubernetes Dashboard

Start the Kubernetes Dashboard:

```bash
minikube dashboard
```

The dashboard can be used to inspect:

* Deployments
* Pods
* Services
* Container status
* Application workloads

## Docker Images

The project uses the following Docker images:

```text
shyamsunder01/shopfront:latest
shyamsunder01/productcatalogue:latest
shyamsunder01/stockmanager:latest
```

## Kubernetes Resources

The project demonstrates the following Kubernetes concepts:

* Deployments
* Pods
* Services
* NodePort
* Labels and selectors
* Container ports
* Liveness probes
* YAML-based configuration
* Service discovery
* Containerized microservices
* Kubernetes Dashboard

## Key Learning Outcomes

Through this project, I practiced:

* Building Java applications with Maven
* Containerizing multiple services with Docker
* Publishing container images to Docker Hub
* Deploying independent services on Kubernetes
* Configuring Kubernetes Deployments and Services
* Managing application health using liveness probes
* Understanding service-to-service communication
* Monitoring workloads using Kubernetes Dashboard
* Working with Minikube for local Kubernetes orchestration

## Future Improvements

Potential improvements include:

* Kubernetes ConfigMaps and Secrets
* Resource requests and limits
* Horizontal Pod Autoscaling
* Rolling update strategies
* Ingress-based application exposure
* Persistent storage
* CI/CD automation for Kubernetes deployments
* Improved container image security and versioning

## Author

**Shyam Sunder**

SRE / DevOps Engineer

GitHub: https://github.com/shyam-sunder01
LinkedIn: https://www.linkedin.com/in/shyam-sunder01/
