## Create a EKS Cluster in AWS


1) Create the subnet compositions.


```
git clone https://github.com/basil1987/crossplane-springpeople-part2.git
cd crossplane-springpeople-part2
cd compositions/crossplane-aws-provider/vpc-subnets
kubectl apply -f .
```


2) Verify above by creating XR through a claim


```
cd 
cd crossplane-springpeople-part2
git pull
cd examples/aws-provider-crossplane/composite-resources/vpc-subnets
kubectl apply -f vpc-subnets-claim.yaml
```

Wait for sometime and XR will be ready. To verify

```
kubectl get composite
```

3) Delete the claim and XR

```
cd 
cd crossplane-springpeople-part2
git pull
cd examples/aws-provider-crossplane/composite-resources/vpc-subnets
kubectl delete -f vpc-subnets-claim.yaml
```

4) Create the EKS compositions.


```
cd
cd crossplane-springpeople-part2
git pull
cd compositions/crossplane-aws-provider/eks
kubectl apply -f definition.yaml
kubectl apply -f eks-managed-node-group.yaml
kubectl apply -f eks-managed-node-group-subnet-labels.yaml
kubectl apply -f autoscaler.yaml
```