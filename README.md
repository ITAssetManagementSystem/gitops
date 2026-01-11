Help me in creating the document about our application, tech stack, Its high level architecture and how we deploy it in the multiple phases. I will share all the details, This document should show our learning journey and help eveyone in understanding about application or how they can setup the same. We wil alo include setup steps like commands and scrresnhots.

Apllication: 
ITAssetManagementSystem

Repos:
AssetService : Java
EmployeeService: .Net
AssignmentService: NodeJS
ReportingService: NodeJS 
UIService : React
CommonWorkflows: For Resuable workflow and composite action for CI and CD 
gitops: For Argo CD ApplicationSet and helm values for all microservices 
helm-charts: For generating the generic single chart for all microservces
InfraService: For Creating the Infra using Terraform 
Utils: Have docker compose file to deploy the all microservice locally 

I wil explain you our juorney and how we implement in phases and commands:

1. We start with creating the Infra using Terraform and we created the resuable workflow for its CI and CD.
2. Then we proced with creating the Dockerfile for all application as per the instruction of each microservices
3. Then we created the docker compose file for testing all app locally.
4. Then we created the manifests fiel for AKS cluster deployment under each microservices.
5. Created Pipeline for CI and CD for deployument to AKS using Rsuable wodkflow and composire acrtion using Github Action. We have also created the AGIC and implented the SSL Certificate.
6. We have setup the ODIC for Azure Login and to saetup the environment variables for subscription, tent id and client id, we have used organization secrets and Have the workflow to update this secrest at organization level uysiing the Github App.
7. Then we further enhance it using helm where we basically created the separate repo for helm chart and passing different values from each moicrservice. We have deployed the Dockerimage as we ll as the OCI helm chart into ACR and then depoying it to AKS using helm comamnds upgrade.
8. After that we implement the Argo CD where we created the gitops repo for Applicationset and put all application values.yml in it so that developer and platform team have separatee rpeo to handle.

You can give the commands and screenshot plave also and I will update.



## Argo CD Setup
 1822  kubectl create namespace argocd
 1823  k get ns
 1824  kubectl apply -n argocd \\n  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml\n
 1825  kubectl port-forward svc/argocd-server -n argocd 8080:443
 1826  kubectl -n argocd get secret argocd-initial-admin-secret \\n  -o jsonpath="{.data.password}" | base64 -d | cat -A
 1827  argocd repo add oci://devacr301225.azurecr.io/helm/service \\n  --type oci \\n  --username devacr301225 \\n  --password <pwd>
 Install argocdcli
 1828  argocd login localhost:8080 \\n  --username admin \\n  --password aG-SqlNMX65DMgLt \\n  --insecure
 1829  argocd repo add oci://devacr301225.azurecr.io/helm/service \\n  --type oci \\n  --username devacr301225 \\n  --password <pwd>
 1830  cd ../../../gitops
 1831  cd gitops
 1832  ls
 1833  cd applicationset
 1834  ls
 1835  kubectl apply -f service-appset.yaml -n argocd
 1836  kubectl get applications -n argocd
 1844  kubectl delete applicationset it-asset-services -n argocd\nkubectl apply -f service-appset.yaml -n argocd\n
 1845  kubectl get applicationsets -n argocd
 1846  kubectl get applicationsets -n argocd\nkubectl get applications -n argocd
 1847  kubectl logs -n argocd deploy/argocd-applicationset-controller
 Enable teh ingress controller and uploaded the certificate
 1852  kubectl rollout restart deployment ingress-appgw-deployment -n kube-system\n

argocd login localhost:8080 \
  --username admin \
  --password <argocd_pwd> \
  --insecure

argocd repo add oci://devacr301225.azurecr.io/helm/service \
  --type oci \
  --username devacr301225 \
  --password <password>

For Creating the PR in gitops repo, we are using the Github App and have it credentials added as a secrets at organization Level.
Three Environment values:
    GH_APP_ID
    GH_APP_PRIVATE_KEY
    GITOPS_REPO=