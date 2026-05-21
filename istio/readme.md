curl -L https://istio.io/downloadIstio | sh -
cd istio-*
export PATH=$PWD/bin:$PATH
istioctl install --set profile=demo -y

disable ingress in values.yaml 

kubectl apply -f argocd/gke-three-tier-istio-app.yaml

kubectl label namespace three-tier-helm istio-injection=enabled --overwrite

kubectl rollout restart deployment/backend -n three-tier-helm
kubectl rollout restart deployment/frontend -n three-tier-helm

