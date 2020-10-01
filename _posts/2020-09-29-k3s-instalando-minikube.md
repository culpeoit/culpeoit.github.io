---
layout: post
title:  "Instalando k3s fácil: Minikube"
date:   2020-09-29 
categories: kubernetes
---

Siguiendo un poco con k8s *(despues de un buen tiempo)*, ya que estoy preparando CKA, me dispuse a levantar mi propio cluster, así que acá voy a resumir un poco lo que hice con `minikube`.

## ¿Qué es minikube?

Minikube es una herramienta que busca hacer más sencillo el correr Kubernetes de manera local. Minikube corre clusters de kubernetes reducido (k3s), de un solo nodo, que para practicar un poco o para entornos de prueba está más que bien.

Un dato a tener en cuenta es que minikube funciona con un motor de VM como lo es VirtualBox o VMWare, pero en mi caso opté por instalarlo directamente sobre una VM linux, por lo que es un método un poco menos convencional.

## Requisitos

### VM

Primero, obviamente, vamos a necesitar nuestra VM linux de 64 bits, en mi caso estoy usando un Debian 10.

### Docker

Ya con nuestra VM lista necesitamos instalar Docker. En este caso, lo más recomendable es instalarlo directamente desde el paquete `.deb` y no con `apt`, por lo que segui las instrucciones de [Docker docs](https://docs.docker.com/engine/install/debian/#install-from-package):

Instalé los paquetes de `containerd`, `docker-ce-cli` y `docker-ce` . Acto seguido agregué mi usuario al grupo de docker:

```bash
usermod -aG docker culpeo
```

### Kubectl

Otro componente importante es `kubectl`, pero es bastante facil de instalar:

Descargamos el binario:
```bash
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
```

Lo hacemos ejecutable:
```bash
chmod +x ./kubectl
```

Lo movemos a nuestro PATH:
```bash
sudo mv ./kubectl /usr/local/bin/kubectl
```

Y listo! Para más información también pùeden visitar la [documentación de kubernetes.](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-linux)

### Minikube

Y obviamente no podía faltar el mismo `minikube`:

Descargamos el binario:
```bash
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube
```

Lo hacemos ejecutable:
```bash
sudo mkdir -p /usr/local/bin/
sudo install minikube /usr/local/bin/
```

## Inicializando el cluster

Si todo salió bien, ya deberíamos poder inicializar minikube:

```bash
minikube start --driver=docker
```

Y deberiamos tener una salida similar a esta:

```bash
culpeo@k3s-minikube:$ minikube start --driver=docker
😄  minikube v1.13.1 on Debian 10.6
✨  Using the docker driver based on existing profile

🧯  The requested memory allocation of 1930MiB does not leave room for system overhead (total system memory: 1930MiB). You may face stability issues.
💡  Suggestion: Start minikube with less memory allocated: 'minikube start --memory=1930mb'

👍  Starting control plane node minikube in cluster minikube
🔄  Restarting existing docker container for "minikube" ...
🐳  Preparing Kubernetes v1.19.2 on Docker 19.03.8 ...
🔎  Verifying Kubernetes components...
🌟  Enabled addons: dashboard, default-storageclass, storage-provisioner
🏄  Done! kubectl is now configured to use "minikube" by default
```

Comprobando que está funcionando:
```console
culpeo@k3s-minikube:/root$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

Ya con minikube funcionando podremos ejecutar varios comandos de kubectl para administrar nuestro - *pequeño* - cluster.

```console
culpeo@k3s-minikube:/$ kubectl get all --all-namespaces
NAMESPACE              NAME                                            READY   STATUS    RESTARTS   AGE
default                pod/nginx-6799fc88d8-ff5nl                      1/1     Running   0          7s
kube-system            pod/coredns-f9fd979d6-xqz9x                     1/1     Running   1          101m
kube-system            pod/etcd-minikube                               1/1     Running   0          4m57s
kube-system            pod/kube-apiserver-minikube                     1/1     Running   0          4m56s
kube-system            pod/kube-controller-manager-minikube            1/1     Running   1          101m
kube-system            pod/kube-proxy-mc7qp                            1/1     Running   1          101m
kube-system            pod/kube-scheduler-minikube                     1/1     Running   1          101m
kube-system            pod/storage-provisioner                         1/1     Running   2          101m
kubernetes-dashboard   pod/dashboard-metrics-scraper-c95fcf479-85kt4   1/1     Running   1          66m
kubernetes-dashboard   pod/kubernetes-dashboard-5c448bc4bf-x64zf       1/1     Running   2          66m

NAMESPACE              NAME                                TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE
default                service/kubernetes                  ClusterIP      10.96.0.1        <none>        443/TCP                  2m26s
default                service/nginx                       LoadBalancer   10.98.82.109     <pending>     80:31299/TCP             7s
kube-system            service/kube-dns                    ClusterIP      10.96.0.10       <none>        53/UDP,53/TCP,9153/TCP   101m
kubernetes-dashboard   service/dashboard-metrics-scraper   ClusterIP      10.97.214.189    <none>        8000/TCP                 66m
kubernetes-dashboard   service/kubernetes-dashboard        ClusterIP      10.102.118.229   <none>        80/TCP                   66m

NAMESPACE     NAME                        DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   daemonset.apps/kube-proxy   1         1         1       1            1           kubernetes.io/os=linux   101m

NAMESPACE              NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
default                deployment.apps/nginx                       1/1     1            1           7s
kube-system            deployment.apps/coredns                     1/1     1            1           101m
kubernetes-dashboard   deployment.apps/dashboard-metrics-scraper   1/1     1            1           66m
kubernetes-dashboard   deployment.apps/kubernetes-dashboard        1/1     1            1           66m

NAMESPACE              NAME                                                  DESIRED   CURRENT   READY   AGE
default                replicaset.apps/nginx-6799fc88d8                      1         1         1       7s
kube-system            replicaset.apps/coredns-f9fd979d6                     1         1         1       101m
kubernetes-dashboard   replicaset.apps/dashboard-metrics-scraper-c95fcf479   1         1         1       66m
kubernetes-dashboard   replicaset.apps/kubernetes-dashboard-5c448bc4bf       1         1         1       66m
```

```console
culpeo@k3s-minikube:/$ kubectl create deploy nginx --image=nginx && kubectl expose deploy nginx --port=80 --type=LoadBalancer
deployment.apps/nginx created
service/nginx exposed
culpeo@k3s-minikube:/$ kubectl get all
NAME                         READY   STATUS    RESTARTS   AGE
pod/nginx-6799fc88d8-ff5nl   1/1     Running   0          86s

NAME                 TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP        3m45s
service/nginx        LoadBalancer   10.98.82.109   <pending>     80:31299/TCP   86s

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/1     1            1           86s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-6799fc88d8   1         1         1       86s
```


Y asi de fácil ya podemos empezar a jugar en nuestro cluster!