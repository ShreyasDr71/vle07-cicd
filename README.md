# vle07-cicd

CI/CD + monitoring lab: GitHub → Jenkins → Docker Hub → Kubernetes (minikube) → Prometheus/Grafana/Alertmanager.

## Flow
1. Push to `main` → GitHub webhook triggers the Jenkins job `myapp`.
2. Jenkins builds the Docker image (`Dockerfile`) and pushes it to Docker Hub as `<user>/myapp:<build>`.
3. Jenkins applies `deployment.yaml` (Deployment + NodePort Service) to the cluster and waits for the rollout.
4. Prometheus scrapes the pods, Grafana shows them, Alertmanager sends alerts.

## Jenkins requirements
- Credential `dockerhub` (Username with password; password = Docker Hub access token).
- `jenkins` user in the `docker` group and a kubeconfig at `/var/lib/jenkins/.kube/config`.
