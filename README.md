# project-GitOps-Workflow-using-ArgoCD-on-Kubernetes
# Objective: 
- Implement GitOps to automate Kubernetes deployments by syncing with a GitHub repository using ArgoCD.
# Tools:
- EC2 (Ubuntu)------pick t2.medium instance
- K3s (Lightweight Kubernetes)
- ArgoCD
- GitHub
- Docker (for building and pushing images)
- MetalLB for K3s or Ingress controller (optional)
# Architecture

![image](https://github.com/user-attachments/assets/0493b0f3-c27a-4b86-95c5-1f17bfc59b68)


# Step-by-Step Guide
- Set Up K3s (Kubernetes) on EC2 -----> curl -sfL https://get.k3s.io | sh –
- Check node status: sudo kubectl get nodes
- Set up kubectl for current user:
```sh   
mkdir -p $HOME/.kube
sudo cp /etc/rancher/k3s/k3s.yaml $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
# Install ArgoCD in K3s
- kubectl create namespace argocd
- kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
# Expose the ArgoCD UI:

- nohup kubectl port-forward svc/argocd-server -n argocd 8080:443 > portforward.log 2>&1 &
- ssh -i "C:\Path\To\YourKey.pem" -L 8080:localhost:8080 ubuntu@your-remote-ip  # # We don’t want to expose of ec2 server NodePort address of server (optional)
- kubectl get nodes

![image](https://github.com/user-attachments/assets/37f23e39-af2c-4f02-823c-9e340f96ac01)

- kubectl edit svc argocd-server -n argocd  #change the clusterIP to nodeport
- Access vi : http://ec2-user-ip:8080
- Get ArgoCD Admin Password: kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d

# Create Your GitHub Repo
- Push the following sample YAML files to a public GitHub repo:
      1.	deployment.yaml
      2.	service.yaml
- Configure ArgoCD to Sync from Git: application.yml
- Apply it: kubectl apply -f .
- Verify Deployment: kubectl get all
- Then push the files into the GitHub repo, check the argocd UI

![image](https://github.com/user-attachments/assets/7524bea7-182c-4420-8417-a1b209581fc5)


- Click refresh


![image](https://github.com/user-attachments/assets/1fbaeec4-eac0-425f-ba4e-fa2218c1fa6d)

- After change the deployment file. Observer the changes in Argocd UI

![image](https://github.com/user-attachments/assets/6d32b715-2368-41ad-975a-ef3df21b092c)
  

- Check the svc of ngnix server NodePort ----> kubectl get svc -n argocd
- Then access it from your local browser: http://your-ec2-ip:NodePort or http://localhost:NodePort

![image](https://github.com/user-attachments/assets/f92b2136-05a4-4b54-abcf-b0bf320e6f22)



- Check the EC2 port in security Group ----> if application is not access means check the server sg
