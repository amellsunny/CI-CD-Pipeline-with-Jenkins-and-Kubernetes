pipeline {
    agent any
    
    tools {
        maven 'maven'
        jdk 'jdk17'
        
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
        
    }
    
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/AmalSunny992/weather-app.git'
            }
        }
       
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        
        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=weather-app -Dsonar.projectName=weather-app \
                    -Dsonar.java.binaries=.'''
                    
                }
            }
        }
 
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
  
        stage('Build') {
            steps {
                sh 'mvn package -DskipTests=true'
            }
        }
    
        stage('Deploy To Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'global-maven', jdk: 'jdk17', maven: 'maven', mavenSettingsConfig: '', traceability: true) {
                    sh "mvn deploy -DskipTests=true"
                }
            }
        }
        
        stage('Docker Build and Tag Image') {
            steps {
                script {
                    // Fetch the Dockerfile from the external repository
                    sh "curl -o Dockerfile https://raw.githubusercontent.com/AmalSunny992/ci-cd/main/docker/Dockerfile"
                    
                    withDockerRegistry(credentialsId: 'dockertoken', toolName: 'docker') {
                        // Build the Docker image using the fetched Dockerfile
                        sh "docker build -t amalsunny992/weather-app:latest -f Dockerfile ."
                    }
                }
            }
        }
        
        stage('Trivy Scan') {
            steps {
                    sh "trivy image amalsunny992/weather-app:latest > trivy-report.txt"
            }
        }
        
        stage('Docker Push Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockertoken', toolName: 'docker') {
                        sh "docker push amalsunny992/weather-app:latest"
                    }
                }
            }
        }
        
    stage('Kubernetes Deploy') {
            steps {
                    // Fetch the Deployment from the external repository
                    sh "curl -o deploymentservice.yml https://raw.githubusercontent.com/AmalSunny992/ci-cd/main/kubernetes/deploymentservice.yml"
                    withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8-token', namespace: 'webapps1', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.47.48:6443') {
                    sh "kubectl apply -f deploymentservice.yml -n webapps1"
                    sh "kubectl get svc -n webapps1"
                    }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
