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

    $ tk8 addon install argocd
    Search local for argocd
    check if provided a url
    Search addon on kubernauts space.
    Cloning into 'argocd'...
    Install argocd
    execute main.sh
    Creating main.yaml
    add  ./argocd-config/00-namespace.yaml
    add  ./argocd-config/01-argocd-crds.yaml
    add  ./argocd-config/02-argocd-sa.yaml
    add  ./argocd-config/03-argocd-role.yaml
    add  ./argocd-config/04-argocd-clusterrole.yaml
    add  ./argocd-config/05-argocd-rolebinding.yaml
    add  ./argocd-config/06-argocd-clusterrolebinding.yaml
    add  ./argocd-config/07-argocd-configmap.yaml
    add  ./argocd-config/08-argocd-secret.yaml
    add  ./argocd-config/09-argocd-service.yaml
    add  ./argocd-config/10-argocd-deploy.yaml
    apply argocd/main.yml
    namespace/argocd created
    customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io created
    customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io created
    serviceaccount/argocd-application-controller created
    serviceaccount/argocd-server created
    serviceaccount/dex-server created
    role.rbac.authorization.k8s.io/argocd-application-controller created
    role.rbac.authorization.k8s.io/argocd-server created
    role.rbac.authorization.k8s.io/dex-server created
    clusterrole.rbac.authorization.k8s.io/argocd-application-controller created
    clusterrole.rbac.authorization.k8s.io/argocd-server created
    rolebinding.rbac.authorization.k8s.io/argocd-application-controller created
    rolebinding.rbac.authorization.k8s.io/argocd-server created
    rolebinding.rbac.authorization.k8s.io/dex-server created
    clusterrolebinding.rbac.authorization.k8s.io/argocd-application-controller created
    clusterrolebinding.rbac.authorization.k8s.io/argocd-server created
    configmap/argocd-cm created
    configmap/argocd-rbac-cm created
    secret/argocd-secret created
    service/argocd-application-controller created
    service/argocd-metrics created
    service/argocd-repo-server created
    service/argocd-server created
    service/dex-server created
    deployment.apps/argocd-application-controller created
    deployment.apps/argocd-repo-server created
    deployment.apps/argocd-server created
    deployment.apps/dex-server created
    argocd installation complete

This command clones the https://github.com/kubernauts/tk8-addon-argocd repository locally and setups the necessary resources to run Argo CD.

This command also creates:

- CRD's required for Argo CD
- Create a separate namespace argocd
- RBAC for giving necessary permissions for Argo CD
- Deploys Argo CD service as the type ClusterIP

Verify if everything is deployed correctly is running with **kubectl get all -n argocd**

    $ kubectl get all -n argocd
    NAME                                                READY   STATUS    RESTARTS   AGE
    pod/argocd-application-controller-7b7d86c45-k4xvk   1/1     Running   2          58m
    pod/argocd-repo-server-6779bf9664-s9n86             1/1     Running   0          58m
    pod/argocd-server-7df95d4864-xzpld                  1/1     Running   0          58m
    pod/dex-server-86bd756dd4-bglcn                     1/1     Running   0          58m
     
    NAME                                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
    service/argocd-application-controller   ClusterIP   10.43.207.93    <none>        8083/TCP            58m
    service/argocd-metrics                  ClusterIP   10.43.29.254    <none>        8082/TCP            58m
    service/argocd-repo-server              ClusterIP   10.43.227.27    <none>        8081/TCP            58m
    service/argocd-server                   ClusterIP   10.43.242.27    <none>        80/TCP,443/TCP      58m
    service/dex-server                      ClusterIP   10.43.210.206   <none>        5556/TCP,5557/TCP   58m
     
    NAME                                            READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/argocd-application-controller   1/1     1            1           58m
    deployment.apps/argocd-repo-server              1/1     1            1           58m
    deployment.apps/argocd-server                   1/1     1            1           58m
    deployment.apps/dex-server                      1/1     1            1           58m
     
    NAME                                                      DESIRED   CURRENT   READY   AGE
    replicaset.apps/argocd-application-controller-7b7d86c45   1         1         1       58m
    replicaset.apps/argocd-repo-server-6779bf9664             1         1         1       58m
    replicaset.apps/argocd-server-7df95d4864                  1         1         1       58m
    replicaset.apps/dex-server-86bd756dd4                     1         1         1       58m

## Access the Argo CD API Server

By default, the Argo CD API server is not exposed with an external IP. To access the API server, we'll change the service type of Argo CD server from ClusterIP to LoadBalancer. For that, run **kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'**

    $ kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
    service/argocd-server patched

Now, check the service type for Argo CD server and note the EXTERNAL-IP with **kubectl get svc argocd-server -n argocd**

    $ kubectl get svc argocd-server -n argocd
    NAME            TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
    argocd-server   LoadBalancer   10.43.242.27   84.200.100.228   80:31665/TCP,443:30727/TCP   60m


Next, get the default password for logging in to Argo CD by running **kubectl get pods -n argocd -l app=argocd-server -o name | cut -d'/' -f 2**

    $ kubectl get pods -n argocd -l app=argocd-server -o name | cut -d'/' -f 2
    argocd-server-7df95d4864-xzpld

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

    $ tk8 addon destroy argocd
    Search local for argocd
    Addon argocd already exist
    Found argocd local.
    Destroying argocd
    execute main.sh
    Creating main.yaml
    add  ./argocd-config/00-namespace.yaml
    add  ./argocd-config/01-argocd-crds.yaml
    add  ./argocd-config/02-argocd-sa.yaml
    add  ./argocd-config/03-argocd-role.yaml
    add  ./argocd-config/04-argocd-clusterrole.yaml
    add  ./argocd-config/05-argocd-rolebinding.yaml
    add  ./argocd-config/06-argocd-clusterrolebinding.yaml
    add  ./argocd-config/07-argocd-configmap.yaml
    add  ./argocd-config/08-argocd-secret.yaml
    add  ./argocd-config/09-argocd-service.yaml
    add  ./argocd-config/10-argocd-deploy.yaml
    delete argocd from cluster
    namespace "argocd" deleted
    customresourcedefinition.apiextensions.k8s.io "applications.argoproj.io" deleted
    customresourcedefinition.apiextensions.k8s.io "appprojects.argoproj.io" deleted
    serviceaccount "argocd-application-controller" deleted
    serviceaccount "argocd-server" deleted
    serviceaccount "dex-server" deleted
    role.rbac.authorization.k8s.io "argocd-application-controller" deleted
    role.rbac.authorization.k8s.io "argocd-server" deleted
    role.rbac.authorization.k8s.io "dex-server" deleted
    clusterrole.rbac.authorization.k8s.io "argocd-application-controller" deleted
    clusterrole.rbac.authorization.k8s.io "argocd-server" deleted
    rolebinding.rbac.authorization.k8s.io "argocd-application-controller" deleted
    rolebinding.rbac.authorization.k8s.io "argocd-server" deleted
    rolebinding.rbac.authorization.k8s.io "dex-server" deleted
    clusterrolebinding.rbac.authorization.k8s.io "argocd-application-controller" deleted
    clusterrolebinding.rbac.authorization.k8s.io "argocd-server" deleted
    configmap "argocd-cm" deleted
    configmap "argocd-rbac-cm" deleted
    secret "argocd-secret" deleted
    service "argocd-application-controller" deleted
    service "argocd-metrics" deleted
    service "argocd-repo-server" deleted
    service "argocd-server" deleted
    service "dex-server" deleted
    deployment.apps "argocd-application-controller" deleted
    deployment.apps "argocd-repo-server" deleted
    deployment.apps "argocd-server" deleted
    deployment.apps "dex-server" deleted
    argocd destroy complete
