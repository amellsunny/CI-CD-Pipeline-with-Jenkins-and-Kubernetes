# Handson-ci-cd-jenkins-Deploy-to-K8S

This repository demonstrates a CI/CD pipeline using Jenkins to deploy a Java application to Kubernetes. 
The pipeline includes stages for code checkout, compilation, testing, static code analysis, dependency checking, packaging, deployment to Nexus, Docker image creation, security scanning, and deployment to Kubernetes.

![image](https://github.com/user-attachments/assets/47584e47-d336-4486-9cd6-f3ff04f451a8)


## Repository Structure

- **Jenkinsfile**: Defines the Jenkins pipeline stages.
- **Dockerfile**: Defines the Docker image configuration for the application.
- **deploymentservice.yml**: Defines the Kubernetes deployment and service configurations.

## Jenkins Pipeline

Here is the Jenkinsfile used in this repository: [Jenkins File](./Jenkinsfile)

The Jenkins pipeline defined in the `Jenkinsfile` includes the following stages:

### 1. Git Checkout
Checks out the code from the main branch of the repository.

### 2. Compile
Compiles the Java code using Maven.

### 3. Unit Test
Runs unit tests using Maven.

### 4. SonarQube Analysis
Performs static code analysis using SonarQube.

![image](https://github.com/user-attachments/assets/9ee7ebb2-61a0-4c0b-befd-96955a5da389)
![image](https://github.com/user-attachments/assets/f6741970-20b3-4229-8af5-c1b228cc4458)


### 5. OWASP Dependency Check
Checks for vulnerabilities in project dependencies.

### 6. Build
Packages the application into a JAR file.

### 7. Deploy To Nexus
Deploys the JAR file to a Nexus repository.

![image](https://github.com/user-attachments/assets/71e5f6f4-93d5-4552-9c37-56d2ffbf084c)
![image](https://github.com/user-attachments/assets/8a8a868f-64f1-4dc9-b41a-7f369f9ba18e)


### 8. Docker Build and Tag Image
Builds a Docker image for the application and tags it.

### 9. Trivy Scan
Scans the Docker image for vulnerabilities using Trivy.

### 10. Docker Push Image
Pushes the Docker image to Docker Hub.

![image](https://github.com/user-attachments/assets/53cdb2cd-f192-4e0d-b90d-4634a414e8fa)

### 11. Kubernetes Deploy
Deploys the application to a Kubernetes cluster using a deployment configuration file.

![image](https://github.com/user-attachments/assets/c787b438-0c81-478d-bbaa-8505b27d52a6)
![image](https://github.com/user-attachments/assets/758db047-2fa6-4013-9f83-fe2619a0b02e)
![image](https://github.com/user-attachments/assets/2c56fe7f-4249-4ab3-99ce-5fe34f7e906e)


## Dockerfile

Here is the Dockerfile used in this repository: [Docker File](./docker/Dockerfile)

The Dockerfile defines the Docker image configuration for the application:

FROM: Specifies the base image to use for the Docker image. In this case, it uses the official Tomcat image version 9.0.87 with JDK 11. This base image includes a Tomcat server and a Java Development Kit.

LABEL: Adds metadata to the image. Here, it specifies the maintainer of the Docker image, which is useful for contact information or documentation.

WORKDIR: Sets the working directory inside the container. Any subsequent COPY, ADD, CMD, RUN, or ENTRYPOINT instructions will be executed in this directory. Here, it's set to the Tomcat webapps directory where web applications are deployed.

EXPOSE: Informs Docker that the container listens on the specified network port at runtime. This does not actually publish the port; it serves as documentation and helps with container linking. Here, port 8080 is exposed, which is the default port for Tomcat.

COPY: Copies files or directories from the host machine to the container's filesystem. In this case, it copies the weather-app.war file from the target directory on the host to the webapps directory in the container. This WAR file is the packaged Java web application to be deployed on Tomcat.

CMD: Specifies the command to run within the container when it starts. Here, it runs the catalina.sh script with the run argument, which starts the Tomcat server in the foreground. This keeps the container running as long as the Tomcat server is running.



## Kubernetes Deployment File

Here is the Kubernetes file used in this repository: [Deployment File](./kubernetes/deploymentservice.yml)

The Kubernetes deployment file defines the deployment and service configurations for the application:

### Deployment Configuration
- apiVersion: apps/v1
Specifies the version of the Kubernetes API to use for this resource. In this case, apps/v1 is used for managing deployments.

- kind: Deployment
Defines the type of Kubernetes resource. Here, Deployment is used to manage the deployment of applications.

- metadata
Provides metadata about the resource, including the name field, which sets the name of the deployment as weather-app-deployment.

- spec
Describes the desired state of the deployment.

- selector
Specifies how to identify the Pods that belong to this deployment. The matchLabels field selects Pods with the label app: weather-app.

- replicas
Indicates the number of desired Pod replicas. In this case, 2 replicas are created and managed by the deployment.

- template
Defines the template for the Pods created by this deployment.

- metadata
Provides metadata for the Pods, including labels. Here, the Pods are labeled with app: weather-app.

- spec
Describes the desired state of the Pods.

- containers
Defines the container specifications for the Pods.

name: The name of the container.

image: The Docker image used for the container (amalsunny992/weather-app:latest).

imagePullPolicy: Specifies when the image should be pulled. IfNotPresent means the image will only be pulled if it's not already present on the node.

ports: Lists the ports exposed by the container. Here, port 8080 is exposed.

### Service Configuration

- apiVersion: v1
Specifies the version of the Kubernetes API to use for this resource. v1 is used for core Kubernetes API objects like services.

- kind: Service
Defines the type of Kubernetes resource. Here, Service is used to expose an application running on a set of Pods.

- metadata
Provides metadata about the resource, including the name field, which sets the name of the service as weather-app-ssvc.

- spec
Describes the desired state of the service.

- selector
Specifies how to identify the Pods that this service applies to. The selector field selects Pods with the label app: weather-app.

- ports
Defines the ports that the service will expose.

protocol: The protocol used by the service, which is TCP in this case.

port: The port that the service will listen on within the cluster (8080).

targetPort: The port on the container that the service will forward traffic to (8080).

type: NodePort

Specifies the type of service. NodePort exposes the service on each Node's IP at a static port (the NodePort). A NodePort service makes the application accessible from outside the cluster.


## Prerequisites
Jenkins server with the following plugins:
- Maven Integration
- Git
- SonarQube
- OWASP Dependency-Check
- Docker Pipeline
- Kubernetes CLI
- SonarQube server
- Nexus repository
- Docker Hub account
- Kubernetes cluster

## How to Use
- Clone this repository.
- Configure the Jenkins pipeline with the provided Jenkinsfile.
- Ensure the necessary credentials and tools are configured in Jenkins.
- Run the pipeline.

## License
This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details.
