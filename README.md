# azure-bootcamp-aks-selenium-grid

# Create AKS Cluster using Azure-CLI
Open powershell or whatever terminal you prefer, login to Azure: 

$ az login

# Create Resource Group with location

$ az group create -g Selenium -l "eastus"

# Create cluster

$ az aks create -g Selenium -n k8s-grid

# Now connect to the cluster specifying the Resource Group and Cluster name we gave in a previous step:

$ az aks get-credentials --resource-group Selenium --name k8s-grid

# You should see something like 
Merged "k8s-grid" as current context in C:\Users\xxx\.kube\config
That’s it, you are connected!

# Create the Hub
Save selenium-hub-deployment.yaml in local folder, then run :

$ kubectl create -f selenium-hub-deployment.yaml

# Now see the pod initializing/ running

$ kubectl get pods 

# Create a service to allow us to get to the Hub web interface, note we need to make sure the type is set to ‘LoadBalancer‘ and create selenium-hub-svc.yaml:
To deploy:  

$ kubectl create -f selenium-hub-svc.yaml

Now run:

$ kubectl get services

# Create the Nodes

Chrome
kubectl create -f .\selenium-node-chrome-deployment.yaml 

Firefox
kubectl create -f .\selenium-node-firefox-deployment.yaml 

Edge
kubectl create -f .\selenium-node-edge-deployment.yaml 

Safari
kubectl create -f .\selenium-node-safari-deployment.yaml 

# Scaling the Grid
We can scale by replicating nodes and have them sit idle:

$ kubectl get deployments

$ kubectl scale deployment selenium-node-firefox --replicas=10
$ kubectl scale deployment selenium-node-chrome --replicas=10

# To AutoScale :

$ kubectl autoscale deployment selenium-node-chrome --cpu-percent=50 --min=1 --max=10

# Delete AutoScale
$ kubectl delete hpa selenium-node-chrome



kubectl create ns selenium
kubectl apply -f selenium-grid-node-chrome-hpa.yaml

kubectl get hpa


kubectl port-forward services/selenium-hub 4444:4444
http://localhost:4444/grid/console




# Kubernetes Dashboard :

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
kubectl apply -f dashboard-adminuser.yml
kubectl get sa admin-user -n kube-system
kubectl apply -f admin-role-binding.yml
kubectl -n kubernetes-dashboard create token admin-user

eyJhbGciOiJSUzI1NiIsImtpZCI6IjJET3NGY1Z3Tl93dTAtcjYyMlFoWkEwTFRsWmJFVGlReGR3YjdtRjYxX3MifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNjc2NDM4NjgxLCJpYXQiOjE2NzY0MzUwODEsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJhZG1pbi11c2VyIiwidWlkIjoiMDg4ZDU2ZmMtYmVkMS00ZTVlLWIxODUtNjU2ZGFmMDA1OTc5In19LCJuYmYiOjE2NzY0MzUwODEsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDphZG1pbi11c2VyIn0.oZwX1ckdWFv2R4XcZwiaI9KB5Qb0KzJBqVkBA3jGPY5mgPeS2sVKJld1XLHQxO87aIWCdQ_EOxa_HZnoUP8_7VPSlV-DFthew0tTPwjgKbqEpr7ClY9aJwP3YMLRRtc7JRGLFCUuSE7KtguFKL9t7qtBFRKiWXh0yWfrE9no6cUAkpTikJRM7mLMaA7moAHdeCaT_idpmzNC4qHFkeLI8rzoaXuA4PMYdrGFetiin_vySpvI_mhZMbLzYD21OFOs4XBszVIKB1F42Sm7I9KY6lx_nliSu6Ys1w4e34YwnlLePhUstuqPt55NpARPvqjzl-GaZP6X6M9yTs6C--2KQw

kubectl proxy
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/.

kubectl -n kubernetes-dashboard delete serviceaccount admin-user
kubectl -n kubernetes-dashboard delete clusterrolebinding admin-user
