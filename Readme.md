# TK8 addon - ArgoCD

## What are TK8 addons?

- TK8 add-ons provide freedom of choice for the user to deploy tools and applications without being tied to any customized formats of deployment.
- Simplified deployment process via CLI (will also be available via TK8 web in future).
- With the TK8 add-ons platform, you can also build your own add-ons.

### Prerequisites

This addon was created for the tk8 cli you could find it here: https://github.com/kubernauts/tk8
Addon integration is supported on Version 0.5.0 and greater

Alternative you can apply the main.yml directly with kubectl

RBAC must be enabled on the Kubernetes Cluster.

## What is Argo CD?

Argo CD is a declarative, GitOps continuous delivery tool for Kubernetes. Argo CD follows the GitOps pattern of using git repositories as the source of truth for defining the desired application state. 

## Get Started

You can install ArgoCD on the Kubernetes cluster via TK8 addons functionality.

What do you need:
- tk8 binary
- A Kubernetes cluster that supports Service objects of type: LoadBalancer

## Deploy ArgoCD on your Kubernetes Cluster

Run **tk8 addon install argocd** 

This command clones the https://github.com/kubernauts/tk8-addon-argocd repository locally and setups the necessary resources to run Argo CD.

This command also creates:

- CRD's required for Argo CD
- Create a separate namespace argocd
- RBAC for giving necessary permissions for Argo CD
- Deploys Argo CD service as the type ClusterIP

Verify if everything is deployed correctly is running with **kubectl get all -n argocd**

## Access the Argo CD API Server

By default, the Argo CD API server is not exposed with an external IP. To access the API server, we'll change the service type of Argo CD server from ClusterIP to LoadBalancer. For that, run **kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'**

Now, check the service type for Argo CD server and note the EXTERNAL-IP with **kubectl get svc argocd-server -n argocd**

Next, get the default password for logging in to Argo CD by running **kubectl get pods -n argocd -l app=argocd-server -o name | cut -d'/' -f 2**

Yes, you guessed it right. The default password for admin user is the Argo CD server's pod name. Now, head over to the browser and open http://EXTERNAL-IP and that should open the login page for Argo CD. Login with the credentials we just obtained.

## Log in using the CLI

You can also Log in to the Argo CD server via argocd CLI. Download the latest Argo CD version from https://github.com/argoproj/argo-cd/releases/latest.

Also available in Mac Homebrew:
- brew tap argoproj/tap
- brew install argoproj/tap/argocd

Now, using the above password and the hostname/IP address log in to the argocd server **argocd login <ARGOCD_SERVER>**

Change the password using the command **argocd account update-password**

This project is licensed under the Apache License - see the [LICENSE.md](LICENSE.md) file for details

## Uninstall Argo CD

For removing Argo CD from your cluster, we can use TK8 addon's destroy functionality. Run **tk8 addon destroy argocd**
