https://kubernetes.io/blog/2020/05/21/wsl-docker-kubernetes-on-the-windows-desktop/

```
# Update the repositories and list of the packages available
sudo apt update
```

```
# Update the system based on the packages installed > the "-y" will approve the change automatically

sudo list --upgradeable

sudo apt upgrade -y
```

```
# Try to see if the docker cli and daemon are installed
docker version
```

```
# Try to see if the docker cli and daemon are installed
docker version
```

```
# Same for kubectl
kubectl version
```

比Minikube更快，使用Kind快速创建K8S学习环境
KinD: Kubernetes made easy in a container

https://kind.sigs.k8s.io/docs/user/using-wsl2/

```
# echo Linux
echo $(uname)
```

```
# Download the latest version of KinD
# curl -Lo ./kind https://github.com/kubernetes-sigs/kind/releases/download/v0.7.0/kind-$(uname)-amd64
cd ~
curl -Lo ./kind https://github.com/kubernetes-sigs/kind/releases/download/v0.8.1/kind-$(uname)-amd64
```

```
# Make the binary executable
chmod +x ./kind
```

```
# Move the binary to your executable path
sudo mv ./kind /usr/local/bin/
```

KinD: the first cluster

We are ready to create our first cluster:

```
# Check if the KUBECONFIG is not set
echo $KUBECONFIG
```

```
# Check if the .kube directory is created > if not, no need to create it
ls $HOME/.kube
```

```
# Create the cluster and give it a name (optional)
kind create cluster --name wslkind
```

```
# Check if the .kube has been created and populated with files
ls $HOME/.kube
```