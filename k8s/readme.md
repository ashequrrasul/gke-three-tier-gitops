gcloud container clusters create gke-three-tier \
  --zone us-east5-a \
  --network myvpc \
  --subnetwork gke-subnet \
  --cluster-secondary-range-name gke-pods \
  --services-secondary-range-name gke-services \
  --enable-private-nodes \
  --enable-private-endpoint \
  --master-ipv4-cidr 172.16.0.0/28 \
  --enable-master-authorized-networks \
  --master-authorized-networks 192.168.0.0/24 \
  --num-nodes 1 \
  --enable-autoscaling \
  --min-nodes 1 \
  --max-nodes 4 \
  --machine-type e2-standard-2 \
  --disk-type pd-balanced \
  --disk-size 30 \
  --enable-ip-alias \
  --workload-pool=project-40d105bf-bb2b-4bf6-b9f.svc.id.goog \
  --enable-secret-manager \
  --release-channel regular


install argo cd

kubectl create namespace argocd
kubectl apply -n argocd --server-side --force-conflicts \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

get argocd password
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d
echo


to access argo cd from local pc. if you managing cluster from vm.
kubectl port-forward svc/argocd-server -n argocd 8080:443 --address 0.0.0.0


cat <<EOF | kubectl apply -f -
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: gke-three-tier-helm-app
  namespace: argocd
spec:
  project: default

  source:
    repoURL: https://github.com/ashequrrasul/gke-three-tier-gitops.git
    targetRevision: main
    path: charts/three-tier-app

  destination:
    server: https://kubernetes.default.svc
    namespace: three-tier-helm

  syncPolicy:
    automated:
      prune: true
      selfHeal: true

    syncOptions:
      - CreateNamespace=true
EOF


###################################
install istio

curl -L https://istio.io/downloadIstio | sh -
cd istio-*
export PATH=$PWD/bin:$PATH

istioctl install --set profile=demo -y

