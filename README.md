kubectl get nodes  
kubectl create namespace argocd  
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml  
watch kubectl get pods -n argocd  
kubectl port-forward svc/argocd-server -n argocd 8080:443  
use get get secret to fetch secret of admin users  
or  
Modify the service to LoadBalancer and then add then take the lb ip and add in DNS.  



kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo  
user the password received from the output of above step to login to argocd using admin as username

argocd on command line - Install 
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64  
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd  
rm argocd-linux-amd64   


Create Application  
argocd app create helm-guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path helm-guestbook --dest-server https://kubernetes.default.svc --dest-namespace default  

argocd app get helm-guestbook  
argocd app sync helm-guestbook  



Other Commands During PoC  
argocd app create helm-guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path helm-guestbook --dest-server https://kubernetes.default.svc --dest-namespace default  

argocd app create helm-guestbook-veera --repo https://github.com/veerahemannthnag/argocd-example-apps.git --path helm-guestbook --dest-server https://kubernetes.default.svc --dest-namespace default-veera  

.\argocd-windows-amd64.exe app sync helm-guestbook  
.\argocd-windows-amd64.exe app get helm-guestbook-veera  

kubectl port-forward svc/helm-guestbook 9090:80  
 
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'  

Add New user  
kubectl get configmap argocd-cm -n argocd -o yaml > argocd-cm.yml  
add below CM and do kubectl apply  
data:  
  accounts.argouser: apiKey, login  
argocd account list  
.\argocd-windows-amd64.exe account update-password --account argouser --new-password <pwd>  


Adding policy to new user to access application which are created using admin user  
Add Below to configmap argocd-rbac-cm  
p, argouser, applications, action/apps/Deployment/restart, default/*, allow  


References:  
https://medium.com/google-cloud/configuring-argocd-on-gke-with-ingress-and-github-sso-bf7868942403  
https://github.com/GoogleCloudPlatform/gke-poc-toolkit-demos/tree/main/gke-fleets-with-argocd#fleet-cluster-setup  
https://www.digitalocean.com/community/tutorials/how-to-deploy-to-kubernetes-using-argo-cd-and-gitops  
https://medium.com/geekculture/create-a-new-user-in-argocd-using-the-cli-and-configmap-8cbb27cf5904  
https://github.com/argoproj/argo-cd/releases  
https://www.opsmx.com/blog/getting-started-with-multi-cluster-k8s-deployments-using-argo-cd  
https://cloud.google.com/blog/products/containers-kubernetes/building-a-fleet-with-argocd-and-gke  
