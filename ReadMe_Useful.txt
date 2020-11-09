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
[ Kube 11 ] Init Containers in Kubernetes Cluster
    kubectl get all -o wide
    kubectl create -f 3-init-container.yaml
    kubectl describe deploy nginx-deploy
    kubectl expose deploy nginx-deploy --type NodePort --port 80
    kubectl scale deploy nginx-deploy --replicas=2
    kubectl delete deploy nginx-deploy
    kubectl delete service nginx-deploy
    kubectl describe pod nginx-deploy-7c589896b8-77v5n    
[ Kube 13 ] Using Persistent Volumes and Claims in Kubernetes Cluster
1. Create a Presistent Volume
2. Create a Presistent Volume Claim
3. POD that uses the PVC -> PV
4. Static Provisioning and Dynamic Provisioning

ReclaimPolicy:
1. Retain
2. Recycle
3. delete

Access Mode:
1. RWO - Read write once
2. RWM - Read write many
3. RO - Read Only
    `Watch` Command in Windows
    cmd prompt>for /l %g in () do @(kubectl get all & timeout /t 2)
    while (1) {kubectl get all -o wide; sleep 5}
    login to kworker1 and create a folder /kube and chmod 777 /kube
1.  kubectl create -f 4-pv-hostpath.yaml
    kubectl get pv
2.  kubectl create -f 4-pvc-hostpath.yaml
    kubectl describe pvc pvc-hostpath
    kubectl get pvc
3.  kubectl create -f 4-busybox-pv-hostpath.yaml
    kubectl exec busybox ls
    kubectl exec busybox ls /mydata
    kubectl exec busybox touch /mydata/hello
    kubectl delete pod busybox
    kubectl delete pvc pvc-hostpath
    login to kworker1 and check a folder /kube to verify hello file
    kubectl create -f 4-pvc-hostpath.yaml
    kubectl describe pvc pvc-hostpath
    kubectl delete pvc pvc-hostpath
    kubectl delete pv pvc-hostpath
    kubectl get pv
    kubectl create -f 4-pvc-hostpath.yaml
    edit 4-busybox-pv-hostpath.yaml
    add nodeSelector
        testserver: "true"
    kubectl get nodes -l testserver=true
    kubectl label node kworker1.example.com demoserver=true
    kubectl get pv pv-hostpath
    kubectl delete pod busybox
--nfs---
make sure nfs utilis(server) is installed
sudo mkdir /sr/nfs/kube -p
sudo vi /etc/exports
sudo exportfs -r
edit 4-pv-hostpath.yaml
add nfs > server: "10.95.65.216" 
        >  /sr/nfs/kube
sudo showmount -e
sudo systemctl status nfs-server
sudo systemctl start nfs-server
mount 10.95.65.213:/srv/nfs/kube
--nfs---
yum install nfs-utils
mkdir /var/nfsshare
chmod -R 755 /var/nfsshare
chown nfsnobody:nfsnobody /var/nfsshare

systemctl enable rpcbind
systemctl enable nfs-server
systemctl enable nfs-lock
systemctl enable nfs-idmap
systemctl start rpcbind
systemctl start nfs-server
systemctl start nfs-lock
systemctl start nfs-idmap
ip addr
server: 172.16.16.101
client: 192.168.97.225 or 192.168.100.1 or 192.168.70.1 or 192.168.0.8
vi /etc/exports
/var/nfsshare    *(rw,sync,no_root_squash,no_all_squash)
/home            *(rw,sync,no_root_squash,no_all_squash)
systemctl restart nfs-server
firewall-cmd --permanent --zone=public --add-service=nfs
firewall-cmd --permanent --zone=public --add-service=mountd
firewall-cmd --permanent --zone=public --add-service=rpc-bind
firewall-cmd --reload

C:\Users\windows>mount -o anon \\172.16.16.101\var\nfsshare Z:
-----winNFSd setup--------------------------

Extract the downloaded WinNFSd.exe on C:/nfs (or where ever you like)
paths.txt and start_server.bat
paths.txt looks like this
C:\Users > /c
D:\ > /d
Contents of start_server.bat
c:\nfs\WinNFSd.exe -addr 192.168.0.8 -pathFile "c:\nfs\paths.txt"

sudo pacman -S nfs-utils  or yum install nfs-utils
By default it will try to connect using nfs v4 protocol. We will set the default version to 3 because as i have already told you, WinNFSd does not support nfs v4
Find the line with #Defaultvers=4 and instead of 4 or anything else write 3 and also remove the # to uncomment the line and save the file. Control + S will save the file on nano.
sudo vi /etc/nfsmount.conf
sudo mkdir /nfs_host
sudo mount -t nfs 192.168.0.8:/c /nfs_host/kubedata -v
sudo umount /nfs_host -lf
172.16.16.102
-----winNFSd setup--------------------------
[ Kube 14 ] Using Secrets in Kubernetes
    kubectrl create -f 5-secrets.yaml
    kubectl get secrets
    kubectl get secret secret-demo -o yaml
    kubectl describe secret secret-demo
    kubectl delete secret secret-demo
    kubectl create secret --help
    kubectl create secret generic --help
    kubectl create secret generic secret-demo --from-literal=username="kubeadmin" --from-literal=password=mypassword
    vi username, vi password
     kubectl create secret generic secret-demo --from-file=./username="kubeadmin" --from-file=./password=mypassword
    kubectl create -f 5-pod-secret-env.yaml
    kubectl exec it busybox -c <containername> -- sh
    env | grep myusername
    echo $myusername
    kubectl delete pod busybox
    vi 5-pod-secret-volume.yaml
    kubectl get secrets
    kubectl create -f 5-pod-secret-volume.yaml
    kubectl get pods -o wide
    kubectl exec -it busybox -- sh
    env | grep myusername
    ls /mydata
    cat /mydata/username; echo
    --Changing the secrets
    echo -n "randompassword" | base64
    kubectl get secrets
    echo -n "raj" | base64
    kubectl apply -f 5-secrets.yaml
    kubectl describe secret secret-demo
    kubectl get pods
    kubectl exec -it busybox -- sh
    ls /mydata
    cat /mydata/name;echo
    cat /mydta/password;echo
[ Kube 15 ] Using ConfigMaps in Kubernetes Cluster


[ Kube 20 ] NFS Persistent Volume in Kubernetes Cluster
kubectl get clusterrole,clusterrolebinding,role,rolebinding | grep nfs
kubectl create -f rbac.yaml
kubectl get storageclass
kubectl create -f class.yaml
kubectl create -f depolyment.yaml
kubectl describe pod <pod-name>
kubectl get pv,pvc
kubectl get storageclass
kubectl create -f 4-pvc-nfs-new.yaml
kubectl create -f 4-busybox-pv-hostpath-label.yaml
kubectl exec -it busybox -- sh
    ls /mydata
    touch /mydata/hello
    ls /mydata
    exit
--verify your nfs server folder
kubectl delete pod <busybox>
kubectl delete pvc  --all
for /l %g in () do @(kubectl get all & timeout /t 2)

[ Kube 25 ] Running Jenkins in Kubernetes Cluster using Helm

kubectl create -f rbac.yaml -f class.yaml -f depolyment.yaml
kubectl -n kube-system create serviceaccount tiller
helm search jenkins
helm install stable/jenkins
helm inspect values stable/jenkins > /tmp/jenkins.values
helm install stable/jenkins --values ./jenkins.values --name myjenkins --namespace demo
helm install --values ./jenkins.values stable/jenkins --generate-name
kubectl logs -f <podname>
helm ls -- get the release name
helm uninstall <jenkins-1597748405>
helm install  --values ./jenkins.values stable/jenkins  --namespace demo --generate-name
[ Kube 30 ] Deploying Kubernetes Cluster using LXC Containers
git clone https://github.com/justmeandopensource/vagrant.git
cd VagrantFile/ubuntu18
edit vagrantfile>edit memory=10240;cpus=1
vagrant up
vagrant ssh
lsb_release -dirc
which lxd
which lxc
sudo ap install lxd
sudo systemctl status lxd
getent group lxd
sudo gpassword -a vagrant lxd
id vagrant 
sudo systemctl start lxd
sudo systemctl status lxd
lxd init
lxc list
git clone https://github.com/justmeandopensource/kubernetes.git
cd lxd-provisioning
free -m 
nproc
lxc profile list
lxc profile show default
lxc profile copy default k8s
lxc profile list
lxc profile edit k8s
lxc profile show k8s
lxc launch images:centos/7 kmaster --profile k8s
lxc list
ping <ipaddress>
lxc list
lxc launch images:centos/7 kworker1 --profile k8s
lxc exec kmaster bash
>cat /etc/redhat-release
less bootstrap-kube.sh
cat bootstrap-kube.sh |lxc exec kmaster bash
cat bootstrap-kube.sh |lxc exec kworker1 bash
lxc list
lxc exec kmaster bash
kubectl cluster-info
kubectl version
kubectl get pods --all-namespaces
kubectl run nginx --image nginx
kubectl describe deploy nginx
[ Kube 30.1 ] Kubernetes 1.17 on LXC Containers

[Windows Terminal Commands]
Create a new pane, splitting horizontally: Alt+Shift+- (Alt, Shift, and a minus sign)
Create a new pane, splitting vertically: Alt+Shift++ (Alt, Shift, and a plus sign)
Move pane focus: Alt+Left, Alt+Right, Alt+Down, Alt+Up
Resize the focused pane: Alt+Shift+Left, Alt+Shift+Right, Alt+Shift+Down, Alt+Shift+Up
Close a pane: Ctrl+Shift+W

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
kubectl create clusterrolebinding default-admin --clusterrole cluster-admin --serviceaccount=default:default
token=$(kubectl get secrets -o jsonpath="{.items[?(@.metadata.annotations['kubernetes\.io/service-account\.name']=='default')].data.token}"|base64 --decode)
echo $token
eyJhbGciOiJSUzI1NiIsImtpZCI6Ii1aVE1QLTVGMWRWWllxUkRCdGNlWHoxWGVsX0lVdE1WRnNaUGpzM2JnTVkifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tanB4ZDUiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImNiNzJjMjRkLTFiOTMtNGJkZC1hZmZhLWI2NmYzYmQ5ZTE0ZCIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.dyqi6KCGws0ZGUXVRR21UpXPDOPshOFZ8sb6fCSv-soXclu7d0uDE8XU_Df3iJhSjDL_N2qQT9lk5hgpDCJUG_SFBtAtihtmDGv37qfR_ER0tqBfRQrsVh8Hp02qEjbgU_Cz0dzmMPA0CoCXs1hnsR5VhHRy_PTyETGlipl0szkuzdJqVO2ezMfVMJYknWk9tcrFTpkHnwMQpMY0WZ6VDO4j0_CIgmi1QD3itmtUJcfNUdtgdCQo2fJthDJwg2QWQYdZRh4k5KdDBMtnyKtZDW0nd5HB1fyHz_5dbwSXape762lErS-r8XVp6cFJcCw5vjrz92MH7aB8JgkWI9ldbg

kubectl proxy
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

[ Kube 50 ] Installing Istio in Kubernetes Cluster
curl -L https://istio.io/downloadIstio | sh -
mv istio-1.7.3/bin/istioctl /usr/local/bin/
istioctl install --set profile=demo
Rancher Meetup - May 2020 - Simplifying Your Cloud-Native Development Workflow With K3s, K3c and K3d
git clone https://github.com/iwilltry42/k3d-demo.git
k3d cluster create demo --api-port 6550 --servers 1 --agents 3 --port 8080:80@loadbalancer --volume /mnt/e/Kubernetes/k3d-play/k3d-demo/sample:/src@all --wait
k3d cluster get
docker build sample/ -f sample/Dockerfile -t sample-app:local
k3d image import -c demo sample-app:local
kubectl create namespace demo
kubectl config set-context --current --namespace=demo
helm upgrade --install sample-app sample/conf/charts/sample-app --namespace demo --set app.image=sample-app:local
sample.k3d.localhost:8080
kubectl get cm -n kube-system
kubectl edit cm traefik -n kube-system
kubectl -n kube-system scale deploy treafik --replicas 0
kubectl -n kube-system scale deploy treafik --replicas 1
kubectl -n kube-system port-forward deployment/traefik 4100
k3d cluster create demo --api-port 6550 --servers 1 --agents 2 --no-lb --wait
kubectl get svc  -n istio-system istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
k3d kubeconfig merge mycluster --merge-default-kubeconfig
docker build kitchen-web/ -f kitchen-web/Dockerfile -t kitchen-ui:local
k3d cluster create kitchenCastle --api-port 6550 --servers 1 --agents 3 --port 8081:80@loadbalancer  --wait
k3d image import -c kitchenCastle kitchen-ui:local
helm upgrade --install kitchen-ui /mnt/e/Kubernetes/civo/lab/kitchen/kitchen-web/conf/charts/kitchen-ui --namespace kitchen --set app.image=kitchen-ui:local
docker run --rm -p 5000:5000 -ti  -e PORT=5000 kitchen-ui:local
----------Deploy Dotnet app------
https://www.openfaas.com/blog/asp-net-core/
https://www.civo.com/learn/guide-to-build-a-crud-api-with-postgresl-and-node-js-with-openfaas

civo kubernetes ls
civo kubernetes config --save kitchen-infra
kubectl config get-contexts
curl -sLSf https://cli.openfaas.com | sudo sh
export OPENFAAS_PREFIX="15091983"
9f591d11-f4fb-4142-880a-e74e27283fbb.k8s.civo.com
export DNS="9f591d11-f4fb-4142-880a-e74e27283fbb.k8s.civo.com" # As per dashboard
export OPENFAAS_URL=http://$DNS:31112
PASSWORD=$(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)
echo -n $PASSWORD | faas-cli login --username admin --password-stdin
faas-cli template store list
faas-cli new --lang dockerfile api
faas-cli build
faas-cli up
faas-cli logs kitchen-api

http://e5044069-1863-4b89-a143-32a59a960bd3.k8s.civo.com.k8s.civo.com:31112/function/api.openfaas-fn

mongo mongodb://hLfr9jmt9CmZrsBvizBh2yom9De8cu:aHJvW8frImLm6LAYHD2dleuDtnNVaq@mongodb:27017/test?authSource=admin
-------------------
https://www.civo.com/learn/set-up-a-private-docker-registry-with-tls-on-kubernetes
docker run --rm -p 8080:8080 -ti 15091983/kitchen-api:latest


docker run --rm -d -p 27017:27017 -v /mnt/e/Kubernetes/civo/lab/kitchen/Data:/data/db mongo
k3d kubeconfig merge kitchenCastle --merge-default-kubeconfig
kubectl config get-contexts
kubectl config set-context kitchen-infra
kubectl config use-context kitchen-infra

kubectl port-forward svc/prometheus-operator-grafana 8080:80  -n monitoring
kubectl port-forward svc/prometheus-prometheus-operator-prometheus 9090
kubectl port-forward svc/prometheus-prometheus-operator-alertmanager 9093
Username: admin and Password: prom-operator

/root/.ssh/id_rsa
https://91.211.153.196:6443
export IP="91.211.153.196"

export USER="hLfr9jmt9CmZrsBvizBh2yom9De8cu"
export PASS="aHJvW8frImLm6LAYHD2dleuDtnNVaq"
export HOST="mongodb-59698d7847-k2658.mongodb.default.kitchen-infra-v1"

kubectl create secret generic -n openfaas-fn db \
  --from-literal db-username="$USER" \
  --from-literal db-password="$PASS" \
  --from-literal db-host="$HOST"

kubectl create secret generic secret-appsettings --from-file=./secret-appsettings=appsettings.secrets.json -n openfaas-fn
kubectl create secret generic secret-api-key \
  --from-file=secret-api-key=secret-api-key.txt \
  --namespace openfaas-fn 

  <k8s-pod-name>.<k8s-internal-service-name>.<k8s-namespace>.<cluster-name>
  mongodb-59698d7847-k2658.mongodb.default.kitchen-infra-v1
  ----Install mongodb in local k3d
  curl -sLS https://dl.get-arkade.dev | sudo sh
  arkade install mongodb
    --using /root/.kube/config
  mongodb.default.svc.cluster.local
  export MONGODB_ROOT_PASSWORD=$(kubectl get secret --namespace default mongodb -o jsonpath="{.data.mongodb-root-password}" | base64 --decode)
  kubectl run --namespace default mongodb-client --rm --tty -i --restart='Never' --image docker.io/bitnami/mongodb:4.2.4-debian-10-r0 --command -- mongo admin --host mongodb --authenticationDatabase admin -u root -p $MONGODB_ROOT_PASSWORD
  kubectl port-forward --namespace default svc/mongodb 27017:27017 &
mongo --host 127.0.0.1 --authenticationDatabase admin -p $MONGODB_ROOT_PASSWORD