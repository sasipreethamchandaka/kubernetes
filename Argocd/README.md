**What is ArgoCD**
Argo CD is a Kubernetes-native continuous deployment (CD) tool. Unlike external CD tools that only enable push-based deployments, Argo CD can pull updated code from Git repositories and deploy it directly to Kubernetes resources.
How to install ArgoCD?
**Create the namespace for argoCD**
kubectl create namespace argocd
**Install ArgoCD using the below command**
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
**After installing the ArgoCD, you can run the below command to check what resources it has created.**
ubuntu@ip-172-31-7-106:~$ kubectl get all -n argocd

**Now in order to access the UI of ArgoCD, you need to run the below command**
kubectl edit svc argocd-server -n argocd  #enable Node Port 

Now, in order to log into the UI you need the credentials. So, for a username, you can write _admin_ and the password is stored in the secret called _argocd-initial-admin-secret_ in the cluster.
You need to run the below command to get the value of the secret.

kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
**The secret base64 encoded so, you have to decode the secret by running the below command.**
echo "secret value" | base64 --decode


So, now I have an application running on my kubernetes cluster. Below are the manifest files for that.

Deployment file
apiVersion: apps/v1
kind: Deployment
metadata:
  name: swiggy-app
  labels:
    app: swiggy-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: swiggy-app
  template:
    metadata:
      labels:
        app: swiggy-app
    spec:
      terminationGracePeriodSeconds: 30
      containers:
      - name: swiggy-app
        image: veeranarni/hotstar:latest
        imagePullPolicy: "Always"
        ports:
        - containerPort: 3000
2. Service file

apiVersion: v1
kind: Service
metadata:
  name: swiggy-app
  labels:
    app: swiggy-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 3000
  selector:
    app: swiggy-app
Press enter or click to view image in full size

Repository

So, now in order for argoCD to sync with this repository we need to write some manifest file for that. Here is the manifest file for that.

apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp-argo-application
  namespace: argocd
spec:
  project: default

  source:
    repoURL: https://github.com/sasipreethamchandaka/Kubernetes.git
    targetRevision: HEAD
    path: day-14-argocd
  destination: 
    server: https://kubernetes.default.svc
    namespace: myapp

  syncPolicy:
    syncOptions:
    - CreateNamespace=true

    automated:
      selfHeal: true
      prune: true
argoproj.io/v1alpha1 is an API version of argoCD. The API Version might get changed once argoCD has some new release. Always refer to the documentation for the latest information.
I am defining my repository URL in repoURL section.
targetRevision is set to HEAD so that it will always fetch the latest commit.
path is set to ‘day-14-argocd’ because I have my application’s manifest files in ‘day-14-argocd’ folder.
In the destination section we have server section and it is set to https://kubernetes.default.svc which is the internal service of the kubernetes API Server.
ubuntu@ip-172-31-7-106:~$ kubectl get svc 
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   137m
ubuntu@ip-172-31-7-106:~$
namespace is set to myapp , because we want to create our application in that namespace. Now, we actually don’t have the namespace already created because we want argoCD to create that automatically.
In order for argoCD to create the namespace automatically we need to define the below attributes.
syncPolicy:
    syncOptions:
    - CreateNamespace=true
We want argoCD to automatically sync any changes in the git repository but by default, it is turned off. So in order to enable that we need to define the below attributes.
automated:
  selfHeal: true
  prune: true
If you apply any changes from the backend using kubectl utility then we want to override that with whatever we have in our git repository in order to do that we have selfHeal: true for that.
If we rename any component or delete the entire component then we want argoCD to delete that component in the cluster as well and in order to do that we have prune: true for that.
argoCD will check the changes in the git repository every 3 minutes. If you want argoCD to check the changes as soon as it has done then you can implement the webhook for that.
Now, that we have our file ready we need to run the below command to apply that.
kubectl apply -f application.yaml
Once you apply the file you can check your application in the UI.
Press enter or click to view image in full size

Application

You can click on your application and check the various details.
Press enter or click to view image in full size

Workflow

Press enter or click to view image in full size

Manifest file

Press enter or click to view image in full size

Manifest file

Press enter or click to view image in full size

Events of pod creation

Now let’s say you want to increase the replica for your application. You just need to commit your changes in the git repository deployment.yml
Changes

As soon as you commit the changes in the repository, argoCD will look for the changes and apply the changes in the cluster.
Press enter or click to view image in full size

Changes in cluster



reference :- **https://medium.com/@veerababu.narni232/a-complete-overview-of-argocd-with-a-practical-example-f4a9a8488cf9**
