pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        DOCKER_REGISTRY = 'hrishi0706'
        IMAGE_NAME = 'starbucks'
    }
    stages {
        stage ("Clean Workspace") {
            steps {
                cleanWs()
            }
        }
        stage ("Git Checkout") {
            steps {
                git branch: 'main', url: 'https://github.com/hrishi-d-d/Starbucks.git'
            }
        }
        stage ("SonarQube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=starbucks \
                        -Dsonar.projectKey=starbucks'''
                }
            }
        }
        stage ("Quality Gate Check") {
            steps {
                script {
                    waitForQualityGate abortPipeline: true, credentialsId: 'Sonar-token'
                }
            }
        }
        stage ("Install NPM Dependencies") {
            steps {
                sh "npm install"
            }
        }
        stage ("Trivy Security Scan") {
            steps {
                sh "trivy fs . > trivy.txt"   // Run Trivy scan and output to file
                sh "cat trivy.txt"           // Display the results in Jenkins console
            }
        }
        stage ("Build Docker Image") {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }
        stage ("Push to DockerHub") {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker') {
                        sh "docker tag ${IMAGE_NAME}:latest ${DOCKER_REGISTRY}/${IMAGE_NAME}:latest"
                        sh "docker push ${DOCKER_REGISTRY}/${IMAGE_NAME}:latest"
                    }
                }
            }
        }
        stage ("Deploy to Docker Swarm") {
            steps {
                sh '''
                docker swarm init || true  # Initialize Swarm (if not already)
                docker service rm starbucks || true  # Remove old service
                docker service create --name starbucks --replicas 5 \
                    --publish 3000:3000 \
                    --restart-condition any \
                    hrishi0706/starbucks:latest
                '''
            }
        }
    }
    post {
        always {
            emailext attachLog: true,
                subject: "'${currentBuild.result}' - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <html>
                    <body>
                        <h2 style="color: #4CAF50;">Jenkins Build Report</h2>
                        <p><strong>Project:</strong> ${env.JOB_NAME}</p>
                        <p><strong>Build Number:</strong> ${env.BUILD_NUMBER}</p>
                        <p><strong>URL:</strong> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    </body>
                    </html>
                """,
                to: 'hd.darbastwar0706@gmail.com',
                mimeType: 'text/html',
                attachmentsPattern: 'trivy.txt'
        }
    }
}

