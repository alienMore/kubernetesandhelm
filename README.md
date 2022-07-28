<h1 align="center">docker install fedora 34</h1>

---
### Install docker
```bash
1)
dnf -y install dnf-plugins-core
2)
dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
3)
dnf install docker-ce docker-ce-cli containerd.io
```
kubeadm: the command line utility to bootstrap the cluster.
kubelet: the component that runs on all of the machines in your cluster and does things like starting pods and containers.
kubectl: the command line utility to talk to your cluster.
kubelet — сервис, который запускается и работает на каждом узле кластера. Следит за работоспособностью подов.
kubeadm — утилита для управления кластером Kubernetes.
kubectl — утилита для отправки команд кластеру Kubernetes.

### Uninstall Docker Engine
Uninstall the Docker Engine, CLI, and Containerd packages:
```bash
sudo dnf remove docker-ce docker-ce-cli containerd.io
Images, containers, volumes, or customized configuration files on your host are not automatically removed. To delete all images, containers, and volumes:
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
You must delete any edited configuration files manually.
```

https://programmersought.com/article/67684366173/


### kubernetes(Проект Kubernetes (сокращенно K8s)) install
```bash
1)
dnf install bridge-utils
2)
add in /etc/hosts
3)
cat > /etc/yum.repos.d/kubernetes.repo <<EOF
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
       https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

on master node and workerS
a) yum install kubeadm kubectl kubelet kubernetes-cni
kubectl-1.21.0-0.x86_64
kubelet-1.21.0-0.x86_64
kubeadm-1.21.0-0.x86_64
kubernetes-cni-0.8.7-0.x86_64

Dependencies resolved.
=============================
 Package
=============================
Installing:
 kubeadm
Installing dependencies:
 conntrack-tools
 containernetworking-plugins
 cri-tools
 kubectl
 kubelet
 libnetfilter_cthelper
 libnetfilter_cttimeout
 libnetfilter_queue
 socat

Transaction Summary
=============================
Install  10 Packages
b) systemctl  enable kubelet && systemctl restart kubelet
   systemctl status kubelet.service
   swapoff -a (dracut --regenerate-all --force)(dnf remove zram-generator-defaults)(systemctl status dev-zram0.swap)
```

### kubeadm init phase kubelet-start
```bash
kubeadm init
kubeadm init --pod-network-cidr=10.244.0.0/16
kubeadm init --control-plane-endpoint 18.x.x.16 --pod-network-cidr=10.244.0.0/16
kubeadm init --control-plane-endpoint 18.x.x.16 --pod-network-cidr=10.244.0.0/16 --ignore-preflight-errors=DirAvailable--var-lib-etcd
kubeadm init --apiserver-advertise-address=192.168.10.31 --pod-network-cidr 10.244.0.0/16
```
- **api-advertise-addresses** - устанавливает IP-адрес, на котором будет слушать API-сервис, нужный для взаимодействия с кластером утилиты kubectl. В нашем случае - 192.168.0.4. Если не указывать этот параметр, будет использоваться IP-адрес интерфейса, смотрящего в сеть шлюза по-умолчанию;
- **pod-network-cidr** - сообщает кластеру адресное пространство (10.244.0.0/16) виртуальных сетей используемых во flannel. Оно задается в файле конфигурации flannel, ссылка на который приведена по URL из вывода kubeadm;
- **api-external-dns-names** - указывает на внешнее DNS имя хоста (в нашем случае master-1.comp.avmaksimov.ru); это позволит вам пройти аутентификацию и авторизацию при использовании kubectl с рабочего места;

Your Kubernetes control-plane has initialized successfully!
To start using your cluster, you need to run the following as a regular user:
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
Alternatively, if you are the root user, you can run:
```bash
export KUBECONFIG=/etc/kubernetes/admin.conf
```

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.10.31:6443 --token sz23bt.cfshbz7wl3297p8e --discovery-token-ca-cert-hash sha256:552a9cef2dac78644dfe76e9c642abe1e5d1bca54b40e9965fcf04f973407d4b

### Подключаем worker ноды
```bash
kubeadm join 192.168.10.31:6443 --token sz23bt.cfshbz7wl3297p8e --discovery-token-ca-cert-hash sha256:552a9cef2dac78644dfe76e9c642abe1e5d1bca54b40e9965fcf04f973407d4b
```



on slave nodea)
b)

5)
Чтобы установить Flannel(наиболее распространенный сетевой плагин)
```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl config view
kubectl cluster-info
kubectl version -o json
kubeadm init --pod-network-cidr 192.168.0.0/16
```
[21:05 root@kuberNode0:lib]#kubeadm init --pod-network-cidr 192.168.0.0/16  
[init] Using Kubernetes version: v1.21.0  
[preflight] Running pre-flight checks  
[preflight] Pulling images required for setting up a Kubernetes cluster  
[preflight] This might take a minute or two, depending on the speed of your internet connection  
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'  
[certs] Using certificateDir folder "/etc/kubernetes/pki"  
[certs] Using existing ca certificate authority  
[certs] Using existing apiserver certificate and key on disk  
[certs] Using existing apiserver-kubelet-client certificate and key on disk  
[certs] Using existing front-proxy-ca certificate authority  
[certs] Using existing front-proxy-client certificate and key on disk  
[certs] Using existing etcd/ca certificate authority  
[certs] Using existing etcd/server certificate and key on disk  
[certs] Using existing etcd/peer certificate and key on disk  
[certs] Using existing etcd/healthcheck-client certificate and key on disk  
[certs] Using existing apiserver-etcd-client certificate and key on disk  
[certs] Using the existing "sa" key  
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"  
[kubeconfig] Using existing kubeconfig file: "/etc/kubernetes/admin.conf"  
[kubeconfig] Using existing kubeconfig file: "/etc/kubernetes/kubelet.conf"  
[kubeconfig] Using existing kubeconfig file: "/etc/kubernetes/controller-manager.conf"  
[kubeconfig] Using existing kubeconfig file: "/etc/kubernetes/scheduler.conf"  
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"  
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"  
[kubelet-start] Starting the kubelet  
[control-plane] Using manifest folder "/etc/kubernetes/manifests"  
[control-plane] Creating static Pod manifest for "kube-apiserver"  
[control-plane] Creating static Pod manifest for "kube-controller-manager"  
[control-plane] Creating static Pod manifest for "kube-scheduler"  
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"  
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests".   This can take up to 4m0s  
[kubelet-check] Initial timeout of 40s passed.  
[apiclient] All control plane components are healthy after 72.004538 seconds  
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace  
[kubelet] Creating a ConfigMap "kubelet-config-1.21" in namespace kube-system with the configuration for the kubelets in the cluster  
[upload-certs] Skipping phase. Please see --upload-certs  
[mark-control-plane] Marking the node kubernode0 as control-plane by adding the labels:
[node-role.kubernetes.io/master(deprecated) node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
[mark-control-plane] Marking the node kubernode0 as control-plane by adding the taints
[node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: qz5rxu.zoiufn036jky616c  
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles  
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to get nodes  
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials  
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token  
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster  
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace  
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key  
[addons] Applied essential addon: CoreDNS  
[addons] Applied essential addon: kube-proxy  
Your Kubernetes control-plane has initialized successfully!

```bash
To start using your cluster, you need to run the following as a regular user:
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.183.133:6443 --token qz5rxu.zoiufn036jky616c \
        --discovery-token-ca-cert-hash sha256:a246582b4855352728766ecc33d8d3dc506fce528ce2866b75d4381c5ac754d6

kubeadm join 192.168.183.133:6443 --token b4rbxp.kdex8kiab1gty85q --discovery-token-ca-cert-hash sha256:f308f604b31f9fe8a5c0379cba59e303c2906b497f63d090fa3883d47dba4fd2
kubeadm join 192.168.183.133:6443 --token 2u49tt.opyjaipebol5h2k5 --discovery-token-ca-cert-hash sha256:f308f604b31f9fe8a5c0379cba59e303c2906b497f63d090fa3883d47dba4fd2
```

### kubeadm token list
```bash

kubeadm token create --print-join-command
kubeadm join 192.168.183.133:6443 --token 2u49tt.opyjaipebol5h2k5 --discovery-token-ca-cert-hash sha256:f308f604b31f9fe8a5c0379cba59e303c2906b497f63d090fa3883d47dba4fd2

[21:23 root@kuberNode0:~]#openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
f308f604b31f9fe8a5c0379cba59e303c2906b497f63d090fa3883d47dba4fd2

kubectl cluster-info
Kubernetes control plane is running at https://192.168.183.133:6443
CoreDNS is running at https://192.168.183.133:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.


kubeadm join 192.168.183.132:6443 \
  --token rn16pe.0cpftyq2joj8mvs1 \
  --discovery-token-ca-cert-hash sha256:f308f604b31f9fe8a5c0379cba59e303c2906b497f63d090fa3883d47dba4fd2

kubectl proxy --port=8080
```

## install flane

1) export KUBECONFIG=/etc/kubernetes/admin.conf или альтернатива в $HOME

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

Warning: policy/v1beta1 PodSecurityPolicy is deprecated in v1.21+, unavailable in v1.25+
podsecuritypolicy.policy/psp.flannel.unprivileged created
clusterrole.rbac.authorization.k8s.io/flannel created
clusterrolebinding.rbac.authorization.k8s.io/flannel created
serviceaccount/flannel created
configmap/kube-flannel-cfg created
daemonset.apps/kube-flannel-ds created

kubectl get nodes
NAME         STATUS     ROLES                  AGE   VERSION  
kubernode0   NotReady   control-plane,master   62m   v1.21.0  

NAMESPACE     NAME                                 READY   STATUS    RESTARTS   AGE  
kube-system   coredns-558bd4d5db-4w7wv             0/1     Pending   0          63m  
kube-system   coredns-558bd4d5db-9b6vb             0/1     Pending   0          63m  
kube-system   etcd-kubernode0                      1/1     Running   3          63m  
kube-system   kube-apiserver-kubernode0            1/1     Running   0          63m  
kube-system   kube-controller-manager-kubernode0   1/1     Running   3          63m  
kube-system   kube-flannel-ds-nvvq6                1/1     Running   0          4m11s  
kube-system   kube-proxy-bhbmz                     1/1     Running   0          63m  
kube-system   kube-scheduler-kubernode0            1/1     Running   4          63m  

```bash
kubectl -n kube-system get pods

kubectl describe nodes
Name:               kubernode0
Roles:              control-plane,master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=kubernode0
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node-role.kubernetes.io/master=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"c2:46:84:12:dd:29"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 192.168.183.133
                    kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Tue, 11 May 2021 21:07:09 +0300
Taints:             node-role.kubernetes.io/master:NoSchedule
                    node.kubernetes.io/not-ready:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  kubernode0
  AcquireTime:     <unset>
  RenewTime:       Tue, 11 May 2021 23:27:09 +0300
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Tue, 11 May 2021 22:06:52 +0300   Tue, 11 May 2021 22:06:52 +0300   FlannelIsUp                  Flannel is running on this node
  MemoryPressure       False   Tue, 11 May 2021 23:23:35 +0300   Tue, 11 May 2021 21:07:09 +0300   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Tue, 11 May 2021 23:23:35 +0300   Tue, 11 May 2021 21:07:09 +0300   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Tue, 11 May 2021 23:23:35 +0300   Tue, 11 May 2021 21:07:09 +0300   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                False   Tue, 11 May 2021 23:23:35 +0300   Tue, 11 May 2021 21:07:09 +0300   KubeletNotReady              container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
Addresses:
  InternalIP:  192.168.206.133
  Hostname:    kubernode0
Capacity:
  cpu:                2
  ephemeral-storage:  97803548Ki
  hugepages-2Mi:      0
  memory:             2947648Ki
  pods:               110
Allocatable:
  cpu:                2
  ephemeral-storage:  90135749688
  hugepages-2Mi:      0
  memory:             2845248Ki
  pods:               110
System Info:
  Machine ID:                 9f7d438a34ad4f02852ff26ee384714f
  System UUID:                564d80bc-21d4-21cb-cecf-68d767f84ae0
  Boot ID:                    ca6ce944-e6df-4e19-8f65-44e9cad857fc
  Kernel Version:             5.11.12-300.fc34.x86_64
  OS Image:                   Fedora 34 (Xfce)
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://20.10.6
  Kubelet Version:            v1.21.0
  Kube-Proxy Version:         v1.21.0
PodCIDR:                      192.168.0.0/24
PodCIDRs:                     192.168.0.0/24
Non-terminated Pods:          (6 in total)
  Namespace                   Name                                  CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                  ------------  ----------  ---------------  -------------  ---
  kube-system                 etcd-kubernode0                       100m (5%)     0 (0%)      100Mi (3%)       0 (0%)         140m
  kube-system                 kube-apiserver-kubernode0             250m (12%)    0 (0%)      0 (0%)           0 (0%)         140m
  kube-system                 kube-controller-manager-kubernode0    200m (10%)    0 (0%)      0 (0%)           0 (0%)         140m
  kube-system                 kube-flannel-ds-nvvq6                 100m (5%)     100m (5%)   50Mi (1%)        50Mi (1%)      80m
  kube-system                 kube-proxy-bhbmz                      0 (0%)        0 (0%)      0 (0%)           0 (0%)         139m
  kube-system                 kube-scheduler-kubernode0             100m (5%)     0 (0%)      0 (0%)           0 (0%)         140m
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests    Limits
  --------           --------    ------
  cpu                750m (37%)  100m (5%)
  memory             150Mi (5%)  50Mi (1%)
  ephemeral-storage  100Mi (0%)  0 (0%)
  hugepages-2Mi      0 (0%)      0 (0%)
Events:              <none>
```
### Замена сертификатов
```bash
cd /etc/kubernetes/pki/
mv {apiserver.crt,apiserver-etcd-client.key,apiserver-kubelet-client.crt,front-proxy-ca.crt,front-proxy-client.crt,front-proxy-client.key,front-proxy-ca.key,apiserver-kubelet-client.key,apiserver.key,apiserver-etcd-client.crt} ~/
kubeadm init phase certs all --apiserver-advertise-address <IP>
cd /etc/kubernetes/
mv {admin.conf,controller-manager.conf,kubelet.conf,scheduler.conf} ~/
kubeadm init phase kubeconfig all
reboot

kubeadm init phase kubeconfig all

логи пода

kubectl --namespace kube-system logs kube-flannel-ds-dqh7g
kubectl --namespace <имя неймспейса> logs <имя пода>

удалить pod

kubectl delete pod pod-nginx
kubectl delete pod <pod name>

Создать Pod

kubectl create -f pod-nginx.yaml

kubectl get pod
kubectl describe pod pod-nginx

kubectl get nodes
NAME         STATUS     ROLES                  AGE   VERSION
kubernode0   NotReady   control-plane,master   56m   v1.21.0

Verify all nodes have joined. At this point nodes have joined but they are in NotReady state, because Kubernetes can’t find a networking provider and configuration.

Смотреть namespace
kubectl get ns

Для добавлении ноды, на мастер сервере делаем: kubeadm token create --print-join-command
И получившуюся команду выполняем на ноде которую хотим добавить, перед этм утановив на этой ноде (dnf install kubectl-1.21.0-0.x86_64 kubelet-1.21.0-0.x86_64 kubeadm-1.21.0-0.x86_64) -> dnf install openssl
yum -y install kubernetes-cni

kubeadm join 192.168.183.133:6443 --token iu8miw.0z3tylgne4eu44fe --discovery-token-ca-cert-hash sha256:f308f604b31f9fe8a5c0379cba59e303c2906b497f63d090fa3883d47dba4fd2

Можно добавить расширенное логирование опцией -> --v=5


kubectl taint node kubernode1 key=value:NoSchedule
kubectl taint node kubernode1 key=value:Schedule
kubectl taint nodes nodename special=true:NoSchedule

если что-то глючит то можно выполнить -> kubeadm reset

kubectl describe nodes kubernode0 |grep Taints
kubectl get componentstatuses
kubectl get nodes -o wide
kubectl get nodes -o json
kubectl get pods -o wide -n kube-system

редактирование созданной ноды
kubectl edit nodes nodename

kubectl delete pod --selector=app=flannel -n kube-system

kubectl get service --all-namespaces
NAMESPACE     NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
default       kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP                  3h27m
kube-system   kube-dns     ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   3h27m

kubectl describe nodes

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

kubectl create ns frontend
namespace/frontend created

kubectl run nginx --image=nginx --namespace frontend
deployment.apps/nginx created
kubectl get pods -n frontend
NAME                    READY   STATUS    RESTARTS   AGE
nginx-76df748b9-gjbs4   0/1     Pending   0          9s


kubectl get events -n frontend

LAST SEEN   TYPE      REASON              OBJECT                       MESSAGE
<unknown>   Warning   FailedScheduling    pod/nginx-76df748b9-gjbs4    0/4 nodes are available: 1 node(s) had taint {app: frontend}, that the pod didn't tolerate, 3 node(s) had taint {node-role.kubernetes.io/controlplane: true}, that the pod didn't tolerate.
<unknown>   Warning   FailedScheduling    pod/nginx-76df748b9-gjbs4    0/4 nodes are available: 1 node(s) had taint {app: frontend}, that the pod didn't tolerate, 3 node(s) had taint {node-role.kubernetes.io/controlplane: true}, that the pod didn't tolerate.


kubectl get pods --all-namespaces

kubectl cluster-info
Kubernetes control plane is running at https://192.168.183.133:6443
CoreDNS is running at https://192.168.183.133:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
kubectl get serviceaccount
NAME      SECRETS   AGE
default   1         26d
```

### Просмотр существующих секретов
```bash
kubectl get secret

NAME                  TYPE                                  DATA   AGE
default-token-8mmdq   kubernetes.io/service-account-token   3      26d
----------------------------------------------------------------------------------------------------
kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   26d
kubectl get service --all-namespaces
NAMESPACE     NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
default       kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP                  26d
kube-system   kube-dns     ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   26d
----------------------------------------------------------------------------------------------------
kubectl get secret
NAME                  TYPE                                  DATA   AGE
default-token-8mmdq   kubernetes.io/service-account-token   3      26d

kubectl describe secret default-token-8mmdq
Name:         default-token-8mmdq
Namespace:    default
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: default
              kubernetes.io/service-account.uid: 55e12ff3-a898-4550-bcb7-22fb660df008

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1066 bytes
namespace:  7 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IjM1NWJLQlFzUncteUkyUWszWnoyZkhaVGdsMEM0bU8wU2o5YmV5ZWZlcHMifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tOG1tZHEiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjU1ZTEyZmYzLWE4OTgtNDU1MC1iY2I3LTIyZmI2NjBkZjAwOCIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.puYQnuHsFlC9Lfn-yL7nMn1Vb0d_0Vt_-XtBwlYVzsKmd6vgFHkBL5NnOKgHH5KFoiJn0FzyyeRUpwdFmN5WrevyNUyVuW8etq0D06iiXmEjFQJ_Sgf24ElxTiQlNnv891S0ZL4Ys5KkOlH0WaL0sxGA6pcqirYUrtdnyjV3yyLq_znNzvUsMzVQsIUo9XNt3v03uMNZtB3vqL9rz8nHiDV5hDv5MUMKQHs9a6EQoRBGOjO9vjSUHSpB5_Nl2gwuRz9KvsZExG5u3_NOje258hNB2wiSbBP2gZdWH5uVrUp-sX5X94RWecyqpqBh2_cN4qqiP7LkLSlQ6GXV8QOD6g

kubectl config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin

- показывает, что мы подключены к кластеру kubernetes-admin@kubernetes Чтобы переключаться между запущенными кластерами:
kubectl config use-context context-name

Создадим serviceaccount для tiller:

kubectl -n kube-system create serviceaccount tiller
serviceaccount/tiller created

Затем мы привяжем serviceaccount tiller к роли cluster-admin:
kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
clusterrolebinding.rbac.authorization.k8s.io/tiller created
```
## Удаление кластера Kubernetes
```bash
kubectl get nodes

запустите:
kubectl drain <node name> --delete-local-data --force --ignore-daemonsets
kubectl drain kubernode1 --delete-local-data --force --ignore-daemonsets
kubectl drain kubernode0 --delete-local-data --force --ignore-daemonsets
(kubernode0 -> мастер нода)
kubectl delete node <node name>
kubectl delete node kubernode1
kubectl delete node kubernode0

Затем на удаляемом узле сбросьте все установленное состояние kubeadm:
kubeadm reset

rm -rf ~/.kube
```
### helm
```bash
curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > install-helm.sh
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  6666  100  6666    0     0  14715      0 --:--:-- --:--:-- --:--:-- 14715
ll
total 8
-rw-r--r-- 1 root root 6666 Jul 11 21:07 install-helm.sh
chmod u+x install-helm.sh
ll
total 8
-rwxr--r-- 1 root root 6666 Jul 11 21:07 install-helm.sh
./install-helm.sh
Downloading https://get.helm.sh/helm-v2.17.0-linux-amd64.tar.gz
Preparing to install helm and tiller into /usr/local/bin
helm installed into /usr/local/bin/helm
tiller installed into /usr/local/bin/tiller
Run 'helm init' to configure helm.

Создадим serviceaccount для tiller:

kubectl -n kube-system create serviceaccount tiller
serviceaccount/tiller created

Затем мы привяжем serviceaccount tiller к роли cluster-admin:

kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
clusterrolebinding.rbac.authorization.k8s.io/tiller created
kubectl cluster-info
Kubernetes control plane is running at https://192.168.10.31:6443
CoreDNS is running at https://192.168.10.31:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
helm init --service-account tiller
Creating /root/.helm
Creating /root/.helm/repository
Creating /root/.helm/repository/cache
Creating /root/.helm/repository/local
Creating /root/.helm/plugins
Creating /root/.helm/starters
Creating /root/.helm/cache/archive
Creating /root/.helm/repository/repositories.yaml
Adding stable repo with URL: https://charts.helm.sh/stable
Adding local repo with URL: http://127.0.0.1:8879/charts
$HELM_HOME has been configured at /root/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
To prevent this, run `helm init` with the --tiller-tls-verify flag.
For more information on securing your installation see: https://v2.helm.sh/docs/securing_installation/

helm install stable/kubernetes-dashboard --name dashboard-demo --set=service.externalPort=8090,resources.limits.cpu=200m

kubectl get po --all-namespaces

2022/07/11 19:36:53 Synchronizer kubernetes-dashboard-key-holder-kube-system exited with error: kubernetes-dashboard-key-holder-kube-system watch ended with timeout
2022/07/11 19:36:56 Restarting synchronizer: kubernetes-dashboard-key-holder-kube-system.
2022/07/11 19:36:56 Starting secret synchronizer for kubernetes-dashboard-key-holder in namespace kube-system
2022/07/11 19:36:56 Synchronizer kubernetes-dashboard-key-holder-kube-system exited with error: kubernetes-dashboard-key-holder-kube-system watch ended with timeout
2022/07/11 19:36:57 Storing encryption key in a secret
panic: secrets is forbidden: User "system:serviceaccount:default:kubernetes-dashboard" cannot create resource "secrets" in API group "" in the namespace "kube-system"

goroutine 1 [running]:
github.com/kubernetes/dashboard/src/app/backend/auth/jwe.(*rsaKeyHolder).init(0xc42000a6a0)
        /home/travis/build/kubernetes/dashboard/.tmp/backend/src/github.com/kubernetes/dashboard/src/app/backend/auth/jwe/keyholder.go:131 +0x35e
github.com/kubernetes/dashboard/src/app/backend/auth/jwe.NewRSAKeyHolder(0x1367500, 0xc4200d0120, 0xc4200d0120, 0x1213a6e)
        /home/travis/build/kubernetes/dashboard/.tmp/backend/src/github.com/kubernetes/dashboard/src/app/backend/auth/jwe/keyholder.go:170 +0x64
main.initAuthManager(0x13663e0, 0xc420082300, 0xc4204c1cd8, 0x1)
        /home/travis/build/kubernetes/dashboard/.tmp/backend/src/github.com/kubernetes/dashboard/src/app/backend/dashboard.go:185 +0x12c
main.main()
        /home/travis/build/kubernetes/dashboard/.tmp/backend/src/github.com/kubernetes/dashboard/src/app/backend/dashboard.go:103 +0x26b

Чтобы действительно удалить релиз и очистить все старые версии, используйте флаг --purge с командой helm delete:
helm delete dashboard-demo --purge

helm ls
```
### Отладка созданного чарта
```bash
helm install --name test2 --dry-run --debug ./nginx

helm list
NAME                    REVISION        UPDATED                         STATUS          CHART           APP VERSION     NAMESPACE
innocent-greyhound      2               Sun Jul 24 14:54:37 2022        DEPLOYED        nginx-0.1.0     1.0             default

helm history innocent-greyhound
REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION
1               Fri Jul 22 21:40:47 2022        SUPERSEDED      nginx-0.1.0     1.0             Install complete
2               Sun Jul 24 14:54:37 2022        DEPLOYED        nginx-0.1.0     1.0             Upgrade complete

kubectl get svc
NAME                       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
innocent-greyhound-nginx   NodePort    10.108.83.41   <none>        80:30001/TCP   42h
kubernetes                 ClusterIP   10.96.0.1      <none>        443/TCP        12d

http://192.168.206.133:30001/
```
###  kubernetes offline install
```bash
kubeadm config images list
I0727 21:46:22.478440  433505 version.go:254] remote version is much newer: v1.24.3; falling back to: stable-1.21
k8s.gcr.io/kube-apiserver:v1.21.14
k8s.gcr.io/kube-controller-manager:v1.21.14
k8s.gcr.io/kube-scheduler:v1.21.14
k8s.gcr.io/kube-proxy:v1.21.14
k8s.gcr.io/pause:3.4.1
k8s.gcr.io/etcd:3.4.13-0
k8s.gcr.io/coredns/coredns:v1.8.0

For each image that appears in the list that was returned in the preceding step, execute the following commands to pull the image and save it as a TAR archive:

docker pull k8s.gcr.io/<image name>
docker save k8s.gcr.io/<image name> > <image name>.tar
where <image name> is the name of the image to pull, for example:

docker pull k8s.gcr.io/kube-apiserver:v1.21.2
docker save k8s.gcr.io/kube-apiserver:v1.21.2 > kube-apiserver_v1.21.2.tar
```
### Load Kubernetes Images (offline machine)
```#!/usr/bin/env bash
docker load < <image name>.tar
where <image name> is the name of the image to unpack, for example:
docker load < kube-apiserver_v1.21.2.tar
```
