# three-tier-app Helm chart

Install locally:




Render and validate:


helm template three-tier-app ./three-tier-app
helm lint ./three-tier-app


helm upgrade --install three-tier-app ./three-tier-app -n three-tier --create-namespace


curl -L https://istio.io/downloadIstio | sh -
cd istio-*
export PATH=$PWD/bin:$PATH
istioctl install --set profile=demo -y
