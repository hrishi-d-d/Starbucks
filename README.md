![Starbucks Clone Deployment](https://github.com/hrishi-d-d/Starbucks/blob/main/Starbucks%20Project.png)
# Deploy Starbucks Application Clone using DevSecOps


## Overview
This project is a Starbucks web application that uses Jenkins for Continuous Integration and Continuous Deployment (CI/CD). The deployment is containerized using Docker and orchestrated with Docker Swarm to maintain a desired state of **5 replicas**.

## Features
- Automated CI/CD pipeline with Jenkins.
- Static code analysis using **SonarQube**.
- Security vulnerability scanning with **Trivy**.
- Docker containerization and deployment using **Docker Swarm**.
- Ensures high availability with **5 running replicas**.


## Prerequisites
### Jenkins Setup
1. Install Jenkins and required plugins:
   - **Pipeline Plugin**
   - **Git Plugin**
   - **SonarQube Scanner Plugin**
   - **Docker Pipeline Plugin**
2. Configure Jenkins tools in `Manage Jenkins` → `Global Tool Configuration`:
   - JDK 17 (`jdk17`)
   - Node.js 16 (`node16`)
   - Sonar Scanner (`sonar-scanner`)
3. Add **Jenkins credentials** in `Manage Credentials`:
   - `docker` → DockerHub credentials
   - `Sonar-token` → SonarQube authentication token
   - `mail-cred` → Email credentials


### Docker Setup
- Install **Docker** and **Docker Swarm** on the server.
- Run `docker swarm init` to initialize Swarm mode.
- Ensure Docker daemon is running with `systemctl enable --now docker`.


## Project Structure
```
Starbucks/
│── Jenkinsfile            # Jenkins pipeline script
│── Dockerfile             # Docker image build instructions
│── package.json           # Node.js dependencies
│── src/                   # Application source code
│── README.md              # Documentation (this file)
│── trivy.txt              # Trivy File scan report
│── Build.log              # Build logs 
│── Screenshots            # Screenshots of the project (optional)
```


## Jenkins Pipeline Workflow

1. **Pulls the latest code** from GitHub.
2. **Runs SonarQube analysis** to check code quality.
3. **Performs a security scan** using Trivy.
4. **Builds a Docker image** and pushes it to DockerHub.
5. **Deploys a Docker Swarm service** with **5 replicas**.
6. **Ensures containers restart automatically** if they fail.
7. **Sends an email** about the **succes/failure  of the jenkins job pipeline** along with **build.log and trivy scan report file.**

## SonarQube Source code analysis report :
![SonarQube Source code analysis report](https://github.com/hrishi-d-d/Starbucks/blob/main/Screenshot%20(444).png)

## Jenkins pipeline workflow :
![Jenkins pipeline workflow](https://github.com/hrishi-d-d/Starbucks/blob/main/Screenshot%20(445).png)

## Deployment
"To manually deploy the application using Docker Swarm:"
```sh
docker service create --name starbucks --replicas 5 \
  --publish 3000:3000 \
  --restart-condition any \
  hrishi0706/starbucks:latest
```

## Accessing the Application :
- After deployment, access the application at: `http://<server-ip>:3000`

## Starbucks website clone :
![Starbucks website clone](https://github.com/hrishi-d-d/Starbucks/blob/main/image.png)


- Check if all replicas are running:
  ```sh
  docker service ls
  docker container ls #Check whether the worker nodes are running
  ```
## Working nodes of the application :
![Working nodes of the application](https://github.com/hrishi-d-d/Starbucks/blob/main/Screenshot%202025-02-10%20110202.png)



## Extended e-Mail Service for the jenkins job notification :
![Email notification](https://github.com/hrishi-d-d/Starbucks/blob/main/Screenshot%20from%202025-02-07%2019-38-40.png)

