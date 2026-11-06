# Voting App — Jenkins + SonarQube + Amazon ECR + EKS

Sample classroom CI/CD project:
- Jenkins on EC2 (`jenkins-ec2`)
- SonarQube on EC2 (`sonarqube-ec2`)
- Existing EKS cluster `k8s-session` in `ap-south-1`
- ECR repositories `votingapp-vote`, `votingapp-result`, `votingapp-worker`

## Reference

Requested reference: https://github.com/nimbuswiztech/Kubernetes/tree/main/Votingapp-Deployment

That folder could not be fetched during generation. These files are a self-consistent sample for the Vote/Redis/Worker/PostgreSQL/Result architecture. Compare resource names, labels, ports and environment variables with the linked source before deploying over an existing application.

## Layout

- `jenkins/Jenkinsfile`
- `kubernetes/eks/00-namespace.yaml` through `05-worker.yaml`
- `student/VotingApp-EKS-Student-Guide.html`

## Jenkins credentials

Create:
- `sonarqube-token` — Secret text
- `postgres-db-password` — Secret text

Edit `SONAR_HOST_URL` in Jenkinsfile to the SonarQube EC2 private DNS/IP.

## Jenkins EC2

Install Java, Jenkins, Git, Docker Engine + Buildx, AWS CLI v2, kubectl and SonarScanner. The sample multi-architecture build also uses a privileged binfmt registration container; review this requirement for your host security policy. Attach a least-privilege EC2 instance profile. Do not hardcode AWS access keys.

## IAM / EKS

Jenkins role needs ECR push permissions, `eks:DescribeCluster`, and EKS cluster access entry / RBAC granting the required namespace deployment permissions. EKS worker-node role needs ECR pull permission. `update-kubeconfig` does not grant Kubernetes authorization.

## First run

Create Jenkins Pipeline job with script path `jenkins/Jenkinsfile`. First run with `DEPLOY_TO_EKS=false` to validate scan/build/push. Then run with `true`.

## App access

```bash
kubectl port-forward -n votingapp svc/voting-service 8080:80
kubectl port-forward -n votingapp svc/result-service 8081:80
```

Vote: http://localhost:8080  
Result: http://localhost:8081

## Limitations

PostgreSQL uses ephemeral storage. No public LoadBalancer/Ingress is created. Worker SonarQube analysis is not configured. This is a training sample, not production-ready.
