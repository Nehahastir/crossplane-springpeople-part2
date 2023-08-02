## Implementing GitOps for Cloud Infra

Pre-requisites

1) Flux installed on the Hub Cluster




#### 1) Install Flux on the Hub Cluster

Install the flux cli by following the doc at https://fluxcd.io/flux/installation/#install-the-flux-cli

Or you can run below handy command.

```
curl -s https://fluxcd.io/install.sh | sudo bash
```

Install flux and its components on the kubernetes cluster with below command.

```
flux install
```

#### 1) Create a GitRepository resource.

Run the below command to create a GitRepository for the podinfo project. 

```
cat <<EOF | kubectl apply -f -
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: podinfo
  namespace: flux-system
spec:
  interval: 1m
  ref:
    branch: master
  url: https://github.com/basil1987/podinfo
EOF
```

#### 2) Create a kustomization 

```
cat <<EOF | kubectl apply -f -
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: podinfo
  namespace: flux-system
spec:
  interval: 1m0s
  path: ./kustomize
  prune: true
  retryInterval: 2m0s
  sourceRef:
    kind: GitRepository
    name: podinfo
  targetNamespace: default
  timeout: 3m0s
  wait: true
EOF
```

#### 3) Verify the podinfo app running. 

Flux will deploy the podinfo app into the default namespace. Verify by running below command.

```
kubectl get all
```

#### 4) Changing Desired State. 

Go to the repository and make any changes in the https://github.com/basil1987/podinfo repository under "kustomize" folder.

For example, change the image version from 6.3.4 to 6.4.0 

Flux is watching your changes in GitHub and apply the settings to the cluster automatically. 

After one minute, try running below command to check the image version in the cluster.

```
kubectl get deployments podinfo -o yaml |grep image:
```

You should see the version has changed to 6.4.0



## Applying GitOps to your compositions. 

You just implemented GitOps for your application deployment, lets do the same for your compositions.


#### 1) Add your compositions repository as a GitRepository in flux.

```
cat <<EOF | kubectl apply -f -
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: crossplane-repo
  namespace: flux-system
spec:
  interval: 1m
  ref:
    branch: master
  url: https://github.com/basil1987/crossplane-springpeople-part2
EOF
```



#### 2) Create a kustomization for your EKS compositions. 

```
cat <<EOF | kubectl apply -f -
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: eks-compositions
  namespace: flux-system
spec:
  interval: 1m0s
  path: ./compositions/crossplane-aws-provider/eks
  prune: true
  retryInterval: 1m0s
  sourceRef:
    kind: GitRepository
    name: crossplane-repo
  targetNamespace: default
  timeout: 3m0s
  wait: true
EOF
```
