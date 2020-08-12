E:\Kubernetes\play\kubernetes\vagrant-provisioning\vagrant up
[ Kube 1 ] Setup Kubernetes Cluster using Kubeadm on CentOS 7
#[ Kube 5 ] Install Kubernetes Dashboard Web UI
    kubectl -n kubernetes-dashboard get all
    kubectl -n kubernetes-dashboard describe service kubernetes-dashboard
    kubectl -n kubernetes-dashboard port-forward kubernetes-dashboard-7b544877d5-stq9t 8080:8443
    kubectl -n kubernetes-dashboard edit service kubernetes-dashboard
    kubectl -n kubernetes-dashboard get sa
    kubectl -n kubernetes-dashboard describe sa kubernetes-dashboard
    kubectl -n kubernetes-dashboard describe secret kubernetes-dashboard-token-ppgjx
    kubectl -n kube-system get sa
#[ Kube 6 ] Running Docker Containers in Kubernetes Cluster
    kubectl run myshell -it --image busybox -- sh
    kubectl delete deploy myshell
    kubectl run myshell -it --rm --image busybox -- sh
    kubectl run nginx --image nginx
    kubectl port-forward nginx-76df748b9-f2nzl 8080:80
    kubectl scale deployments nginx --replicas 2
    kubectl describe pod nginx-76df748b9-znvcm
    kubectl expose deployment nginx --type NodePort --port 80
    kubectl get deploy nginx -o yaml >./nginx.yaml
    kubectl delete deploy nginx
    kubectl delete service nginx
    kubectl create -f .\nginx.yaml
[ Kube 7 ] Kubernetes Pods Replicasets & Deployments
    kubectl create -f .\1-nginx-pod.yaml
    kubectl get events
    kubectl describe pod nginx
    kubectl delete -f .\1-nginx-pod.yaml
    kubectl create -f .\1-nginx-replicaset.yaml
    kubectl delete replicaset nginx-replicaset
    kubectl create -f .\1-nginx-deployment.yaml
    kubectl scale deploy nginx-deploy --replicas=3
    kubectl delete deploy nginx-deploy
[ Kube 8 ] Kubernetes Namespaces & Contexts
    kubectl cluster-info
    kubectl get namespace
    kubectl -n kube-system get pods
    kubectl create namespace demo
    kubectl config view
    kubectl config get-contexts
    kubectl config set-context kubesys --namespace=kube-system --user=kubernetes-admin --cluster=kubernetes
    kubectl config current-context
    kubectl config use-context kubesys
    kubectl config set-context demo --namespace=demo --user=kubernetes-admin --cluster=kubernetes
    alias kcc='kubectl config current-context'
    alias kuc='kubectl config use-context'
    kubectl config use-context kubernetes-admin@kubernetes
    kubectl create -f .\1-nginx-pod.yaml
    kubectl config use-context demo
    kubectl create -f .\1-nginx-pod.yaml
    kubectl delete pod nginx
[ Kube 9 ] How to use Node Selector in Kubernetes
    kubectl label node kworker2.example.com demoserver=true
    kubectl get node kworker2.example.com --show-labels
    notepad .\1-nginx-deployment.yaml
    kubectl create -f .\1-nginx-deployment.yaml
    kubectl scale deploy nginx-deploy --replicas=2
[ Kube 9.1 ] PodNodeSelector Admission Control Plugin | Assigning pods to nodes
    kubectl label node kworker1.example.com env=prod
    kubectl label node kworker1.example.com env-
    kubectl label node kworker1.example.com env=dev
    kubectl label node kworker2.example.com env=prod
    kubectl edit ns dev
    kubectl edit ns prod
    kubectl -n dev get pods -o wide
    kubectl -n dev run nginx --image nginx --replicas 4
    kubectl -n dev get deploy nginx -o yaml
    kubectl -n dev delete deploy nginx
[ Kube 10 ] Kubernetes DaemonSets
    kubectl create -f .\1-nginx-daemonset.yaml
    kubectl describe daemonsets nginx-daemonset
    kubectl delete pod nginx-daemonset-6tzs9
    kubectl get all -o wide
    kubectl delete daemonset nginx-daemonset
    kubectl get daemonset -n kube-system
    kubectl get daemonset kube-proxy -n kube-system -o yaml
    kubectl get nodes -l  gpupresent=true
[ Kube 11 ] Jobs & Cronjobs in Kubernetes Cluster
    kubectl create -f .\2-job.yaml
    kubectl logs helloworld-nmvsj
    kubectl describe job helloworld
    kubectl delete job helloworld    
Jobs:
    Default run
    Killing pod restarts pod
    completions
    parallelism
    backofflimit
    activateDeadlineSeconds
Cronjobs:
    Cron wiki @hourly, @weekly, monthly
    deleting cronjobs
    successfulJobshistoryLimit
    failedJobshistoryLimit
    suspending cron jobs (kubectl, apply, patch)
    concurrencyPolicy (Allow, Forbid & Replicate)
    idempotency

Usecases:
    mysql backup
    sending emails
    anybackups
    checking out sources periodically
[ Kube 11 ] Jobs & Cronjobs in Kubernetes Cluster
kubectl get all -o wide
kubectl create -f 3-init-container.yaml
kubectl describe deploy nginx-deploy
kubectl expose deploy nginx-deploy --type NodePort --port 80
kubectl scale deploy nginx-deploy --replicas=2
kubectl delete deploy nginx-deploy
kubectl describe pod nginx-deploy-7c589896b8-77v5n    

