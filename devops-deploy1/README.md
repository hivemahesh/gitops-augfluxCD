Gitops-FluxCD::
=======================
THis is used in the CD part***

CD --> done by the Flux *****

. We will keep the files in the Github, and it gets trigger from k8s clsuter***

. GitOps is a way to do k8s cluster management and application deliver. It works using GIt as a single source of truth for declarative infrastructure and application.

Step 1: Intsall fluxctl *** and fluxCD or Flux operator

Install fluxctl:
wget https://github.com/fluxcd/flux/releases/download/1.21.0/fluxctl_linux_amd64
mv fluxctl_linux_amd64 fluxctl
chmod +x fluxctl
mv fluxctl /usr/bin/
fluxctl version

https://github.com/fluxcd/flux/releases/download/1.25.4/fluxctl_linux_arm64

[root@ip-172-31-11-47 ~]# wget https://github.com/fluxcd/flux/releases/download/1.25.4/fluxctl_linux_amd64

[root@ip-172-31-11-47 ~]# mv fluxctl_linux_amd64 fluxctl
[root@ip-172-31-11-47 ~]# chmod +x fluxctl
[root@ip-172-31-11-47 ~]# mv fluxctl /usr/bin/
[root@ip-172-31-11-47 ~]# fluxctl version
1.25.4


Install FluxCD or Flux Operator
--------------------------------------------------------------------

In order to run FluxCD, we need to run flux operator...

Create the flux namespace:

[root@ip-172-31-11-47 ~]# kubectl create ns flux    +++++++++++
namespace/flux created

[root@ip-172-31-11-47 ~]# kubectl get ns
NAME              STATUS   AGE
cert-manager      Active   5h9m
default           Active   11h
flux              Active   4s

Then, install Flux in your cluster (replace YOURUSER with your GitHub username): Syntax:

export GHUSER="YOURUSER"
    fluxctl install \
    --git-user=${GHUSER} \
    --git-email=${GHUSER}@users.noreply.github.com \
    --git-url=git@github.com:${GHUSER}/flux-get-started \		--> Replace URL with our Git Repo URL
    --git-path=namespaces,workloads \				            --> Replace with folder inside Git Repo
    --namespace=flux | kubectl apply -f -

Make changes according to our requriement --->

export GHUSER="hivemahesh"
fluxctl install \
--git-user=${GHUSER} \
--git-email=${GHUSER}@users.noreply.github.com \
--git-url=git@github.com:${GHUSER}/gitops-new \
--git-branch=main \
--git-path=devops-deploy \
--namespace=flux | kubectl apply -f -

[root@ip-172-31-11-47 ~]#  export GHUSER="hivemahesh"     +++++++++ give our github username***


Create a new repo in github and clone it****** gitops-augfluxCD and create a branch "devops

Inside gitops-augfluxCD, created a directory "devops-deploy1" from the github console only ..........

I need to monitor "devops-deploy1" directory +++++++++++++++++++++++++++++++++++

(only yml manifest will be monitored)

fluxctl install \
--git-user=${GHUSER} \
--git-email=${GHUSER}@users.noreply.github.com \
--git-url=git@github.com:${GHUSER}/gitops-augfluxCD \
--git-branch=main \
--git-path=devops-deploy1 \
--namespace=flux | kubectl apply -f -


[root@ip-172-31-11-47 ~]# fluxctl install \
--git-user=${GHUSER} \
--git-email=${GHUSER}@users.noreply.github.com \
--git-url=git@github.com:${GHUSER}/gitops-augfluxCD \
--git-branch=main \
--git-path=devops-deploy1 \
--namespace=flux | kubectl apply -f -

secret/flux-git-deploy unchanged
Warning: spec.template.spec.nodeSelector[beta.kubernetes.io/os]: deprecated since v1.14; use "kubernetes.io/os" instead
deployment.apps/memcached configured
service/memcached unchanged
serviceaccount/flux unchanged
clusterrole.rbac.authorization.k8s.io/flux created
clusterrolebinding.rbac.authorization.k8s.io/flux created
deployment.apps/flux configured

[root@ip-172-31-11-47 ~]# kubectl get all -n flux
NAME                             READY   STATUS    RESTARTS   AGE
pod/flux-6869cc6bbc-gflxv        1/1     Running   0          2m37s
pod/memcached-577c754f55-2t2wt   1/1     Running   0          2m37s

NAME                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
service/memcached   ClusterIP   10.100.49.173   <none>        11211/TCP   7m53s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/flux        1/1     1            1           7m54s
deployment.apps/memcached   1/1     1            1           7m53s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/flux-5d4cd54495        0         0         0       7m53s
replicaset.apps/flux-6869cc6bbc        1         1         1       2m37s
replicaset.apps/memcached-577c754f55   1         1         1       2m37s
replicaset.apps/memcached-7b786d999c   0         0         0       7m53s


[root@ip-172-31-11-47 ~]# kubectl get pods -n flux
NAME                         READY   STATUS    RESTARTS   AGE
flux-6869cc6bbc-gflxv        1/1     Running   0          5m9s			this operator will sync with my github  ************
memcached-577c754f55-2t2wt   1/1     Running   0          5m9s


Clone the github repositing and keep our deployment and svc file under "devops-deploy1" directory ****************

To pull the github repo to our local m/c, we need to create the PAT personal access token and add it at the starting of the link while pulling

On the right side top ---> settings ---> developer settings ---> PAT

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



[root@ip-172-31-11-47 ~]# git clone https://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@github.com/hivemahesh/gitops-augfluxCD.git

[root@ip-172-31-11-47 devops-deploy1]# git branch
* main

[root@ip-172-31-11-47 devops-deploy1]# cat deployment.yml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: spring-boot-hello
  namespace: springboot
  labels:
    app: spring-boot-hello
spec:
  replicas: 1
  selector:
    matchLabels:
      app: spring-boot-hello
  template:
    metadata:
      labels:
        app: spring-boot-hello
    spec:
      containers:
      - name: springboot-container
        image: naresh240/springboothello:v9
        imagePullPolicy: Always
        ports:
        - containerPort: 8080

[root@ip-172-31-11-47 devops-deploy1]# vi service.yml
[root@ip-172-31-11-47 devops-deploy1]# cat service.yml
---
kind: Service
apiVersion: v1
metadata:
  name: spring-boot-hello
spec:
  type: LoadBalancer
  selector:
    app: spring-boot-hello
  ports:
    - name: http
      protocol: TCP
      port: 8080
      targetPort: 8080

[root@ip-172-31-11-47 devops-deploy1]# git config --global user.email "mahesh.haruvaiah@gmail.com"
[root@ip-172-31-11-47 devops-deploy1]# git config --global user.name "hivemahesh"
[root@ip-172-31-11-47 devops-deploy1]# git add .
[root@ip-172-31-11-47 devops-deploy1]# git commit -m "created devops-deploy1 dir on main branch and 2 yml files"

[root@ip-172-31-11-47 devops-deploy1]# git push origin main		++++++++++++++++


-----------------------------------

Authorize the FluxCD to connect to our github repository

We now need to allow the Flux CD operator to interact with the git repository, and therefore we need to add its public SSH key to the repo...


Get the public SSH key using fluxctl:   -- so that the communication is establised b/w github and flux ****

 fluxctl identity --k8s-fwd-ns <namespace>   ********			+++++++++++++++++++++++++

[root@ip-172-31-11-47 devops-deploy1]# fluxctl identity --k8s-fwd-ns flux
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCewyTK1OugYBtrp+HzQErAocLl+zdFMzVvlpdBv/vGnKGRWhcqmLVo16hSH1UUKZzTk1DAxFo3Plv9NlNHjJXBxb/QCtouAfuobar1O3p190Ok3pPJDGVCzGB6THL5IyjSDSAgtfieqE8+sK0GSUC+xHvNpo7pdT91lNum6TouTem4Fu7thgaAE0xUAahMwqsvw8qpWRrunUq8yg/6K6NYXGqP0+qdI2FhaojtE4FRGAnvURjelYNNAyJhIzXwIFOEYh6IB1YvU6ZmS7eVXInAkObMXW4STxcZ8ekguJyhM7umxVmbl09MmUUt7/mEckP1W6mnve2hRCfvrafl4AcZ3HCO9/vaLr+4+wI83sCfWss1B+Dbzc5ewOD7sB7wGp4ntB+jNgmzvTZs9aYi2W37anXZuYhMnxlTNCascokMXcE0tKZUKcaG3MEFgREXkz9k9fXpkLe6nYzQxhK7uXC8BXi5jtC5oOKxm7aGNSC3Xccu88MgNG+iO+Nf3KiaWEE= root@flux-6869cc6bbc-gflxv


Go to github -> gitops-augfluxCD--> settigns ---> deploy keys --> Add deploy keys 

title -> fluxcd  --> anyname we can keep
key:  paste the above key ***
click on "write access" and "Add key"" 


THIS GITHUB REPOSITORY WILL BE MONITORED BY """FLUX""" OPERATOR +++++++++++++++++++ CALLED AS "GITOPS""''


Here we have already kept 2 yml files --> deploy and svc ...  and this willbe deployed automatically.


By default, Flux git pull frequency is set to 5 minutes. You can tell Flux to sync the changes immediately with:  +++++++++++++

fluxctl sync --k8s-fwd-ns flux

[root@ip-172-31-11-47 devops-deploy1]# kubectl get deploy
No resources found in default namespace.

[root@ip-172-31-11-47 devops-deploy1]# fluxctl sync --k8s-fwd-ns flux
Synchronizing with ssh://git@github.com/hivemahesh/gitops-augfluxCD
Revision of main to apply is be783fc
Waiting for be783fc to be applied ...
Done.


Now if i wan to delete the resources created by "flux" means we need to add this below value***   ( mean to say that, i made the change to deploy file, so it should delte existing deploy and craete a new one so using it)

 - --sync-garbage-collection=true
 
 
[root@ip-172-31-11-47 devops-deploy1]# kubectl edit deploy flux -n flux


    spec:
      containers:
      - args:
        - --memcached-service=
        --sync-garbage-collection=true		+++++++++++++++++++++
        - --ssh-keygen-dir=/var/fluxd/keygen
        - --git-url=git@github.com:hivemahesh/gitops-augfluxCD


[root@ip-172-31-11-47 devops-deploy1]# fluxctl sync --k8s-fwd-ns flux


[root@ip-172-31-11-47 devops-deploy1]# kubectl get deploy
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
spring-boot-hello   1/1     1            1           5s

IN REAL-TIME, ALWAYS MAKE THE CHANGES IN "DEVOPS" BRANCH, ONCE DONE CREATE A 'PULL REQUEST' AND ONCE THE DATA IS AVAILABLE IN ""MAIN"" BRANCH, IT IS MOVED TO PRODUCTION WHEN WE EXECUTE  ABOVE """SYNC""" COMMAND *****
