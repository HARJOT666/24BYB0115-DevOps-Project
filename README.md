# 24BYB0115-DevOps-Project

## NexaCorp Corporate Website — CI/CD Pipeline & Deployment

A complete DevOps project implementing a CI/CD pipeline for a responsive corporate website using Docker, Jenkins, Kubernetes, and monitoring tools (Nagios, Graphite, Grafana).

---

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Project Structure](#project-structure)
- [Website Features](#website-features)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
  - [Git Setup](#git-setup)
  - [Docker](#docker)
  - [Docker Compose](#docker-compose)
  - [Maven Build](#maven-build)
  - [Jenkins Pipeline](#jenkins-pipeline)
  - [Kubernetes Deployment](#kubernetes-deployment)
  - [Nagios Monitoring](#nagios-monitoring)
  - [Graphite Metrics](#graphite-metrics)
  - [Grafana Dashboard](#grafana-dashboard)
- [CI/CD Pipeline Flow](#cicd-pipeline-flow)
- [Screenshots](#screenshots)
- [Technologies Used](#technologies-used)
- [Author](#author)

---

## 📖 Project Overview

This project demonstrates a complete DevOps lifecycle for deploying a corporate website. It covers:

- **Source Code Management** — Git version control with proper branching
- **Build Automation** — Maven for packaging the application
- **Containerization** — Docker for creating portable, reproducible environments
- **Continuous Integration/Deployment** — Jenkins declarative pipeline
- **Container Orchestration** — Kubernetes for production-grade deployment
- **Monitoring & Alerting** — Nagios, Graphite, and Grafana for observability

---

## 📁 Project Structure

```
24BYB0115-DevOps-Project/
│
├── src/                          # Website source code
│   ├── index.html                # Home page
│   ├── about.html                # About Us page
│   ├── services.html             # Services page
│   ├── careers.html              # Careers page
│   ├── contact.html              # Contact Us page
│   ├── gallery.html              # Gallery page
│   ├── css/
│   │   └── style.css             # Main stylesheet
│   ├── js/
│   │   └── main.js               # Main JavaScript
│   └── images/                   # Image assets
│
├── k8s/                          # Kubernetes manifests
│   ├── deployment.yaml           # Deployment with 2 replicas
│   └── service.yaml              # NodePort service (30080)
│
├── monitoring/                   # Monitoring configurations
│   ├── nagios/
│   │   └── corporate-website.cfg # Nagios host & service checks
│   ├── graphite/
│   │   └── graphite-config.conf  # Storage schemas & metric commands
│   └── grafana/
│       └── dashboard.json        # Grafana dashboard definition
│
├── Dockerfile                    # Docker image definition (nginx:alpine)
├── Jenkinsfile                   # CI/CD pipeline definition
├── docker-compose.yml            # Docker Compose configuration
├── pom.xml                       # Maven build configuration
├── README.md                     # Project documentation
└── .gitignore                    # Git ignore rules
```

---

## 🌐 Website Features

- **6 Pages**: Home, About, Services, Careers, Contact, Gallery
- **Responsive Design**: Mobile-first, works on all screen sizes
- **Modern UI**: Gradient backgrounds, smooth animations, glassmorphism effects
- **Interactive Elements**: Mobile hamburger menu, lightbox gallery, scroll animations
- **Contact Form**: Client-side validation with toast notifications
- **Counter Animation**: Animated statistics with intersection observer
- **Gallery Filter**: Category-based image filtering
- **Smooth Scrolling**: Anchor link smooth scrolling
- **Scroll-to-Top**: Floating button with scroll detection

---

## ⚙️ Prerequisites

Ensure the following tools are installed on your system:

| Tool       | Version  | Purpose                    |
|------------|----------|----------------------------|
| Git        | 2.30+    | Version control            |
| Docker     | 20.10+   | Containerization           |
| Docker Compose | 2.0+ | Multi-container orchestration |
| Maven      | 3.8+     | Build automation           |
| Java JDK   | 11+      | Maven runtime              |
| Jenkins    | 2.300+   | CI/CD pipeline             |
| kubectl    | 1.24+    | Kubernetes CLI             |
| Minikube/K8s | 1.24+  | Kubernetes cluster         |

---

## 🚀 Getting Started

### Git Setup

```bash
# Clone the repository
git clone https://github.com/your-username/24BYB0115-DevOps-Project.git
cd 24BYB0115-DevOps-Project

# Initialize Git (if starting fresh)
git init
git add .
git commit -m "Initial commit: NexaCorp Corporate Website with CI/CD pipeline"

# Create and push to remote
git remote add origin https://github.com/your-username/24BYB0115-DevOps-Project.git
git branch -M main
git push -u origin main
```

---

### Docker

#### Build the Docker Image

```bash
# Build the image
docker build -t nexacorp/corporate-website:1.0.0 .

# Verify the image was created
docker images | grep nexacorp
```

#### Run the Container

```bash
# Run in detached mode
docker run -d --name nexacorp-website -p 8081:80 nexacorp/corporate-website:1.0.0

# Verify the container is running
docker ps

# Access the website
# Open browser: http://localhost:8081

# View container logs
docker logs nexacorp-website

# Stop and remove the container
docker stop nexacorp-website
docker rm nexacorp-website
```

---

### Docker Compose

```bash
# Start the service (build + run)
docker-compose up -d --build

# Verify the service
docker-compose ps

# Access the website
# Open browser: http://localhost:8081

# View logs
docker-compose logs -f corporate-website

# Stop the service
docker-compose down

# Stop and remove volumes
docker-compose down -v
```

---

### Maven Build

```bash
# Clean and package the project
mvn clean package

# The WAR file will be generated at:
# target/corporate-website.war

# Verify the build
ls -la target/corporate-website.war
```

---

### Jenkins Pipeline

#### Setup

1. **Install Jenkins** and required plugins:
   - Pipeline Plugin
   - Docker Pipeline Plugin
   - Kubernetes CLI Plugin
   - Git Plugin

2. **Configure Credentials** in Jenkins:
   - `docker-hub-credentials` — Docker Hub username/password
   - `kubeconfig-credentials` — Kubernetes config file

3. **Create a Pipeline Job**:
   - Go to Jenkins → New Item → Pipeline
   - Set Pipeline Definition: "Pipeline script from SCM"
   - Repository URL: your Git repo URL
   - Script Path: `Jenkinsfile`
   - Branch: `*/main`

#### Pipeline Stages

| Stage                 | Description                           |
|-----------------------|---------------------------------------|
| **Checkout**          | Clone source code from Git            |
| **Maven Build**       | Run `mvn clean package`               |
| **Docker Build**      | Build Docker image with build number  |
| **Docker Push**       | Push image to Docker Hub (main only)  |
| **Deploy to K8s**     | Apply K8s manifests & update image    |

#### Run the Pipeline

```bash
# Trigger build via Jenkins UI or CLI
java -jar jenkins-cli.jar -s http://localhost:8080/ build 24BYB0115-DevOps-Project
```

---

### Kubernetes Deployment

#### Setup

```bash
# Start Minikube (for local development)
minikube start

# Create the namespace
kubectl create namespace nexacorp
```

#### Deploy

```bash
# Apply the deployment manifest
kubectl apply -f k8s/deployment.yaml

# Apply the service manifest
kubectl apply -f k8s/service.yaml

# Verify deployment
kubectl get deployments -n nexacorp
kubectl get pods -n nexacorp
kubectl get services -n nexacorp
```

#### Access the Application

```bash
# For Minikube
minikube service corporate-website-service -n nexacorp

# For standard K8s clusters
# Access via: http://<node-ip>:30080

# Check pod logs
kubectl logs -f deployment/corporate-website -n nexacorp

# Scale the deployment
kubectl scale deployment corporate-website --replicas=4 -n nexacorp
```

#### Deployment Details

| Property            | Value                         |
|---------------------|-------------------------------|
| Replicas            | 2                             |
| Strategy            | RollingUpdate                 |
| Max Unavailable     | 1                             |
| Max Surge           | 1                             |
| CPU Request         | 100m                          |
| CPU Limit           | 250m                          |
| Memory Request      | 128Mi                         |
| Memory Limit        | 256Mi                         |
| Container Port      | 80                            |
| Service Type        | NodePort                      |
| Node Port           | 30080                         |

---

### Nagios Monitoring

#### Setup

1. Copy the Nagios configuration file:
   ```bash
   sudo cp monitoring/nagios/corporate-website.cfg /usr/local/nagios/etc/objects/
   ```

2. Add the configuration to `nagios.cfg`:
   ```bash
   echo "cfg_file=/usr/local/nagios/etc/objects/corporate-website.cfg" | sudo tee -a /usr/local/nagios/etc/nagios.cfg
   ```

3. Verify the configuration:
   ```bash
   sudo /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
   ```

4. Restart Nagios:
   ```bash
   sudo systemctl restart nagios
   ```

#### Configured Checks

| Service                | Check Interval | Warning | Critical |
|------------------------|----------------|---------|----------|
| HTTP Availability      | 5 min          | -       | -        |
| HTTPS (SSL)            | 5 min          | -       | -        |
| HTTP Response Time     | 5 min          | 2s      | 5s       |
| CPU Load               | 5 min          | 5.0     | 10.0     |
| Memory Usage           | 10 min         | 20%     | 10%      |
| Disk Usage             | 15 min         | 20%     | 10%      |
| SSH Access             | 10 min         | -       | -        |

---

### Graphite Metrics

#### Setup

1. Copy the configuration:
   ```bash
   sudo cp monitoring/graphite/graphite-config.conf /opt/graphite/conf/storage-schemas.conf
   ```

2. Install the metric collection script:
   ```bash
   sudo cp monitoring/graphite/collect-metrics.sh /opt/nexacorp/collect-metrics.sh
   sudo chmod +x /opt/nexacorp/collect-metrics.sh
   ```

3. Add to crontab for automated collection:
   ```bash
   # Collect metrics every minute
   echo "* * * * * /opt/nexacorp/collect-metrics.sh" | sudo crontab -
   ```

#### Manual Metric Publishing

```bash
# Send a single metric to Graphite
echo "nexacorp.website.http.status_code 200 $(date +%s)" | nc -q0 graphite-server 2003

# Send CPU usage
echo "nexacorp.system.cpu.usage $(mpstat 1 1 | awk '/Average/ {print 100-$NF}') $(date +%s)" | nc -q0 graphite-server 2003

# Send HTTP response time
echo "nexacorp.website.http.response_time $(curl -o /dev/null -s -w '%{time_total}' http://localhost:8081 | awk '{printf "%.0f", $1*1000}') $(date +%s)" | nc -q0 graphite-server 2003
```

#### Metric Paths

| Metric Path                              | Description              |
|------------------------------------------|--------------------------|
| `nexacorp.system.cpu.usage`              | CPU utilization (%)      |
| `nexacorp.system.memory.usage`           | Memory utilization (%)   |
| `nexacorp.system.disk.usage`             | Disk utilization (%)     |
| `nexacorp.system.network.bytes_in`       | Network bytes received   |
| `nexacorp.system.network.bytes_out`      | Network bytes sent       |
| `nexacorp.website.http.response_time`    | HTTP response time (ms)  |
| `nexacorp.website.http.status_code`      | HTTP status code         |
| `nexacorp.website.nginx.active_connections` | Active nginx connections |
| `nexacorp.system.uptime`                 | System uptime (seconds)  |

---

### Grafana Dashboard

#### Setup

1. **Access Grafana**: Open `http://localhost:3000` (default: admin/admin)

2. **Add Graphite Data Source**:
   - Go to Configuration → Data Sources → Add Data Source
   - Select "Graphite"
   - URL: `http://graphite-server:8080`
   - Click "Save & Test"

3. **Import the Dashboard**:
   - Go to Dashboards → Import
   - Click "Upload JSON file"
   - Select `monitoring/grafana/dashboard.json`
   - Select the Graphite data source
   - Click "Import"

#### Dashboard Panels

| Panel                  | Type        | Description                        |
|------------------------|-------------|------------------------------------|
| Website Status         | Stat        | Current HTTP status (UP/DOWN)      |
| Response Time          | Stat        | Current HTTP response time         |
| CPU Usage              | Stat        | Current CPU utilization            |
| Memory Usage           | Stat        | Current memory utilization         |
| Disk Usage             | Stat        | Current disk utilization           |
| Uptime                 | Stat        | System uptime                      |
| CPU Usage Over Time    | Time Series | Historical CPU usage graph         |
| Memory Usage Over Time | Time Series | Historical memory usage graph      |
| Network Traffic        | Time Series | Inbound/Outbound network traffic   |
| Active Connections     | Time Series | Nginx active connections           |
| HTTP Response Time     | Time Series | Historical response time with SLA  |
| HTTP Status Codes      | Time Series | Status code history                |
| Website Availability   | Gauge       | SLA availability percentage        |
| System Uptime          | Time Series | Uptime history                     |

---

## 🔄 CI/CD Pipeline Flow

```
┌──────────┐    ┌──────────┐    ┌──────────────┐    ┌─────────────┐    ┌──────────────────┐
│   Git    │───▶│  Maven   │───▶│    Docker    │───▶│   Docker    │───▶│   Kubernetes     │
│ Checkout │    │  Build   │    │    Build     │    │    Push     │    │    Deploy        │
└──────────┘    └──────────┘    └──────────────┘    └─────────────┘    └──────────────────┘
                                                         │                      │
                                                    (main branch           (main branch
                                                      only)                  only)
```

1. **Developer** pushes code to Git repository
2. **Jenkins** automatically triggers the pipeline
3. **Maven** cleans and packages the application
4. **Docker** builds a production-ready container image
5. **Docker Hub** stores the versioned image (main branch only)
6. **Kubernetes** deploys the new version with rolling updates
7. **Monitoring** (Nagios/Graphite/Grafana) tracks health & performance

---

## 🛠️ Technologies Used

| Category          | Technology                              |
|-------------------|-----------------------------------------|
| Frontend          | HTML5, CSS3, JavaScript (Vanilla)       |
| Web Server        | Nginx (Alpine)                          |
| Containerization  | Docker, Docker Compose                  |
| Build Tool        | Maven                                   |
| CI/CD             | Jenkins (Declarative Pipeline)          |
| Orchestration     | Kubernetes                              |
| Monitoring        | Nagios, Graphite, Grafana               |
| Version Control   | Git                                     |

---

## 👤 Author

**Student ID:** 24BYB0115

**Project:** DevOps Assignment — Corporate Company Website Deployment

---

## 📝 License

This project is created for educational purposes as part of a DevOps assignment.
