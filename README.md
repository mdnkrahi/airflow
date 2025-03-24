sudo apt update  -y

sudo apt install -y curl wget apt-transport-https ca-certificates gnupg

sudo apt install -y docker.io

sudo systemctl start docker

sudo systemctl enable docker


curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

minikube version


curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/


kubectl version --client

sudo usermod -aG docker $USER && newgrp docker

minikube start --driver=docker 

minikube status




# Create a kubernetes cluster of 1 control plane and 3 worker nodes
kind create cluster --name airflow-cluster --config kind-cluster.yaml

# Check the cluster info
kubectl cluster-info

# Check the nodes with kubectl
kubectl get nodes -o wide

# Add the official repository of the Airflow Helm Chart
helm repo add apache-airflow https://airflow.apache.org

# Update the repo
helm repo update

# Create namespace airflow
kubectl create namespace airflow

# Check the namespace 
kubectl get namespaces

# Install the Airflow Helm Chart
helm install airflow apache-airflow/airflow --namespace airflow --debug

# Get pods
kubectl get pods -n airflow

# Check release
helm ls -n airflow

# If for some reasons the release is stuck in pending-install or timed out
# Resinstall the chart
helm delete airflow --namespace airflow
helm install airflow apache-airflow/airflow --namespace airflow --debug —timeout 10m0s

# Port forward 8080:8080
kubectl port-forward svc/airflow-webserver 8080:8080 -n airflow --context kind-airflow-cluster
