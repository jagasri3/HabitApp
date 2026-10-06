# Habit Tracker — Java CI/CD and GitOps

## Project Overview

Habit Tracker is a Spring Boot application with a web dashboard and REST API for tracking daily and weekly habits and streaks.

This project demonstrates a DevOps workflow using Maven, Jenkins, SonarQube, Docker, Helm, Kubernetes, and Argo CD.

## Technology Stack

- Java 21
- Spring Boot 3.3.4
- Maven
- Jenkins — CI pipeline
- SonarQube — code quality analysis
- Docker — containerization
- Helm — Kubernetes application packaging
- Kubernetes — container orchestration
- Argo CD — GitOps continuous delivery
- Git and GitHub — source and deployment configuration

## Architecture and Workflow

1. Source code is stored in GitHub.
2. Jenkins checks out the repository.
3. Maven builds the application and runs unit tests.
4. SonarQube analyzes code quality.
5. Docker builds a container image.
6. Helm defines the Kubernetes Deployment and Service.
7. Argo CD monitors the Git repository and synchronizes the desired configuration to Kubernetes.
8. Kubernetes maintains the configured number of application replicas and performs rolling updates.

**CI:** GitHub → Jenkins → Maven → Tests → SonarQube → Docker build

**GitOps CD:** GitHub Helm configuration → Argo CD → Kubernetes

Jenkins performs the CI workflow, while Argo CD manages deployment synchronization from Git.

## Repository Structure

- `src/` — Java application source and tests
- `pom.xml` — Maven build and dependencies
- `Dockerfile` — multi-stage container build
- `.dockerignore` — files excluded from the Docker build context
- `Jenkinsfile` — Jenkins declarative pipeline
- `helm/` — Helm chart and Kubernetes resource templates
- `sonar-project.properties` — SonarQube project configuration
- `README.md` — project documentation

## Prerequisites

Install Java 21, Maven, Docker, Git, kubectl, and Helm. Jenkins and SonarQube must be running for the CI workflow. A Kubernetes cluster and Argo CD are required for GitOps deployment.

## Build and Run Locally

Build the application:

```bash
mvn clean package
```

Run the generated JAR:

```bash
java -jar target/habit-tracker.jar
```

Open the application at:

http://localhost:8080

The application provides a web dashboard and REST API. Its current data storage is in-memory, so application data is cleared when the process restarts.

## SonarQube Analysis

Start or use a running SonarQube server, then execute the Maven analysis with a token supplied securely:

```bash
mvn clean verify sonar:sonar \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.token=YOUR_GENERATED_TOKEN
```

Replace the placeholder with your own token. Do not commit tokens or passwords to Git.

## Docker

Build the image from the repository root:

```bash
docker build -t habit-tracker:v1 .
```

Run it locally:

```bash
docker run --rm -p 8080:8080 habit-tracker:v1
```

Open http://localhost:8080.

The Dockerfile uses separate build and runtime stages and runs the application as a non-root user.

## Helm and Kubernetes

Validate the Helm chart:

```bash
helm lint helm
helm template habit-tracker helm
```

Install or update the application:

```bash
helm upgrade --install habit-tracker helm
```

Check the deployment:

```bash
kubectl get deployment
kubectl get pods -o wide
kubectl get services
kubectl rollout status deployment/habit-tracker-habit-tracker
```

The local Kubernetes application is available at:

http://localhost:30080

The Helm configuration defines the replica count, container image, NodePort Service, resource requests and limits, security context, and health probes.

## Argo CD and GitOps

Argo CD monitors the Helm configuration in this repository and synchronizes changes to Kubernetes.

To inspect application status:

```bash
argocd app get habit-tracker
```

The application should report **Synced** and **Healthy** when synchronization and health checks succeed.

For a GitOps update, commit and push the intended Helm configuration change. Argo CD can then detect and apply the desired state from Git.

## Rollback

Deployment configuration changes should be reverted through Git so the repository remains the source of truth.

For example, revert a configuration commit and push the revert:

```bash
git revert COMMIT_ID
git push origin main
```

Argo CD can then synchronize the reverted Helm configuration. Verify the deployment and pod status after synchronization.

## REST API

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/habits` | List habits |
| GET | `/api/habits/{id}` | Get one habit |
| POST | `/api/habits` | Create a habit |
| PUT | `/api/habits/{id}` | Update a habit |
| DELETE | `/api/habits/{id}` | Delete a habit |
| PATCH | `/api/habits/{id}/active` | Activate or deactivate a habit |
| POST | `/api/habits/{id}/complete` | Mark a habit complete |
| DELETE | `/api/habits/{id}/complete` | Remove a completion |
| GET | `/api/habits/{id}/stats` | View streak and completion statistics |

## Security Notes

- Keep tokens and passwords out of source control.
- Store Jenkins credentials in Jenkins Credentials.
- Run the application container as a non-root user.
- Use appropriate Kubernetes security settings.

## Project Evidence

Project evidence includes Maven build and test results, SonarQube analysis and Quality Gate, Docker build and running container, Jenkins pipeline stages, Helm chart validation, Kubernetes deployment, Argo CD synchronization, application access, monitoring output, and rollback verification.

## Limitations

The current Jenkins pipeline builds the Docker image locally. Publishing to an external container registry and automatically updating Helm image versions are additional steps if required by the deployment environment.

The local Kubernetes environment does not currently expose pod resource metrics through the Metrics API.

## Application URL

Local Kubernetes application: http://localhost:30080

GitHub repository: https://github.com/jagasri3/HabitApp