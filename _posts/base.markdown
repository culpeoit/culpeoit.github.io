---
layout: post
title:  "Instalando Kubernetes con kubeadm en Debian"
date:   2020-08-28 
categories: kubernetes
---

Siguiendo un poco con k8s, ya que estoy preparando CKA, me dispuse a levantar mi propio cluster así que acá voy a resumir un poco lo que hice.

**Disclaimer:** *Simplemente voy a tradcuri y simplificar lo que dice la documentación oficial de Kubernetes. [Pueden visitarla y seguramente estará mejor explicada que acá](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/).*

## Requisitos

* VM/s con Debian 9+
* 2GB+ de RAM y 2+ vCPUs por VM

## Prerando el ambiente para kubeadm

Como primer instancia, necesitamos desactivar el swap con `swapoff -a` y editando el archivo `/etc/fstab` .

Una vez tengamos el swap desactivado vamos a activar el modulo br_netfilter con el comando `modprobe br_netfilter` y modificar `sysctl` :

```console
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```

[También necesitamos tener en cuenta los puertos que se van a utilizar](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#check-required-ports).

Y necesitamos tener algún motor de contenedores instalado también, puede ser docker, containerd, etc. En mi caso instalé Docker [según la documentación oficial](https://docs.docker.com/engine/install/debian/).

## Instalando kubeadm

Instalar kubeadm es bastante sencillo, solo hay que seguir los siguientes pasos:

```console
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

## Inicializando el cluster

Una vez tengamos kubeadm instalado, necesitamos inicializar nuestro cluster, para esto vamos a usar el comando `kubeadm init` , que va a revisar que este todo en orden.

Revisar el output del init, si vemos las siguiente linea significa que todo salió bien:

`Your Kubernetes control-plane has initialized successfully!`

Ahora necesitamos ejecutar los siguientes comandos por fuera del usuario root:

```console
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Uniendo un nodo a un cluster

Si estás uniendo un nuevo nodo a un cluster, en vez de `kubeadm init` vamos a ejecutar el comando:

```console
kubeadm join --token <token> <control-plane-host>:<control-plane-port> --discovery-token-ca-cert-hash sha256:<hash>
```

Donde:

* <token> : `kubeadm token list`
* <control-plane-host> : `IP del master`
* <control-plane-port> : `6443 por defecto`

```console
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | \
   openssl dgst -sha256 -hex | sed 's/^.* //'
```