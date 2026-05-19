gcloud container clusters create gke-three-tier \
  --zone us-east5-a \
  --network myvpc \
  --subnetwork gke-subnet \
  --cluster-secondary-range-name gke-pods \
  --services-secondary-range-name gke-services \
  --num-nodes 1 \
  --enable-autoscaling \
  --min-nodes 1 \
  --max-nodes 3 \
  --machine-type e2-medium \
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

