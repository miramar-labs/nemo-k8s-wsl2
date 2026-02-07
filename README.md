# nemo-k8s-wsl2

## INSTALL

### Minikube

    minikube start --driver=docker --cpus=max --memory=max
    #minikube start --driver=docker --cpus=max --memory=12000mb
    minikube addons enable ingress
    minikube addons enable dashboard
    minikube addons enable storage-provisioner
    minikube addons enable default-storageclass

### volcano

    helm repo add volcano-sh https://volcano-sh.github.io/helm-charts
    helm repo update

    helm install volcano volcano-sh/volcano \
    -n volcano-system --create-namespace

### NeMo
        
    export NVIDIA_API_KEY="$NGC_API_KEY"  

    kubectl create secret generic ngc-api --from-literal=NGC_API_KEY="$NGC_API_KEY"
    kubectl create secret generic nvidia-api --from-literal=NVIDIA_API_KEY="$NVIDIA_API_KEY"

    helm repo add nmp https://helm.ngc.nvidia.com/nvidia/nemo-microservices \
    --username='$oauthtoken' \
    --password="$NGC_API_KEY"
    helm repo update

    helm install nemo nmp/nemo-microservices-helm-chart \
    --namespace default \
    --set nim.enabled=false \
    --set guardrails.guardrails.nvcfAPIKeySecretName="nvidia-api" \
    --timeout 30m \
    --wait
  
### MLflow

    ./install-mlflow.sh

## RESTART NeMo operator

    kubectl rollout restart deployment/nemo-nemo-operator-controller-manager
    kubectl rollout status deployment/nemo-nemo-operator-controller-manager


## UNINSTALL

### MLflow

    ./uninstall-mlflow.sh

### NeMo

    helm uninstall nemo -n default