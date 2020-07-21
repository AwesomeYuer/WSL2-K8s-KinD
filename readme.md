# https://kubernetes.io/blog/2020/05/21/wsl-docker-kubernetes-on-the-windows-desktop/

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

# 比Minikube更快，使用Kind快速创建K8S学习环境
# KinD: Kubernetes made easy in a container

https://kind.sigs.k8s.io/docs/user/local-registry/

https://kubernetes.io/docs/concepts/containers/images/#using-a-private-registry

https://kind.sigs.k8s.io/docs/user/private-registries/

https://kind.sigs.k8s.io/docs/user/using-wsl2/

https://kind.sigs.k8s.io/docs/user/working-offline/



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

# KinD: the first cluster

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
# kind create cluster --name wslkind
kind create cluster --name wsl2-kind-k8s-cluster01
```

![kind create cluster](images/kind-create-cluster.png)


```
# Check if the .kube has been created and populated with files
ls $HOME/.kube
```


# KinD: counting 1 - 2 - 3

Our first cluster was created and it's the "normal" one/single node cluster:

```
# Check how many nodes it created
kubectl get nodes
```

```
# Check the services for the whole cluster
kubectl get all --all-namespaces
```


# KinD: can I see a nice dashboard?

Working on the command line is always good and very insightful. However, when dealing with Kubernetes we might want, at some point, to have a visual overview.

For that, the Kubernetes Dashboard project has been created. The installation and first connection test is quite fast, so let's do it:


https://github.com/kubernetes/dashboard/releases/tag/v2.0.3

```
# Install the Dashboard application into our cluster
# kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-rc6/aio/deploy/recommended.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.3/aio/deploy/recommended.yaml
```

```
# Check the resources it created based on the new namespace created
kubectl get all -n kubernetes-dashboard
```

```
# Start a kubectl proxy
kubectl proxy
# Enter the URL on your browser: 
# http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

# Let's open a new WSL2 session:

```
# Create a new ServiceAccount
kubectl apply -f - <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
EOF
# Create a ClusterRoleBinding for the ServiceAccount
kubectl apply -f - <<EOF
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF
```

```
# Get the Token for the ServiceAccount
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
# Copy the token and copy it into the Dashboard login and press "Sign in"
```

```
user@YUER-P50:/mnt/c/Users/xiyueyu$ kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
Name:         admin-user-token-q8qbs
Namespace:    kubernetes-dashboard
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: bbe2a105-58cf-4ade-a826-77e58ca18b55

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  20 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6ImZJMmM0MjVOdEpmZWRlbkNRYS1xemMyeWxueG5FYXZiQ2RHWHFLUi1uRUUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLXE4cWJzIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJiYmUyYTEwNS01OGNmLTRhZGUtYTgyNi03N2U1OGNhMThiNTUiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.MsPYP-YvbmBOlQfh-pTyWWJA6dAHsDlh55bTTfFZLJV6fY0GlZCr2wh6XpZtvDjLZF4JAOjvXL9cQrk212jI9z-0TUZ1vEQBdDmAW0T849lT27HOSzQyZKN3ACYfKpErnMtUMoi5etl4wkJHFbrfLa0JpTIG2P3Lz-tsseZc7LKCfCY_RRQ1EIblEC44xaNDLx1b5BBr_vlpZ9jIcinhia2N2Cpod8BRcRyyP7GtUOKls_clrVNX8v3C0IM2KGii_m1zfevaUoyFEywdnd0JKx5rIr-UjbS4rDqfgszZSEEQfb9fC4J9QZwUPQRa538cbygSEkiExVpd8-wRszDrXg
```
