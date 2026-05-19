# three-tier-app Helm chart

Install locally:




Render and validate:


helm template three-tier-app ./three-tier-app
helm lint ./three-tier-app


helm upgrade --install three-tier-app ./three-tier-app -n three-tier --create-namespace