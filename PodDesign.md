<H1> POD Design(20%)</H1>

Create 3 pods with names nginx1,nginx2,nginx3. All of them should have the label app=v1
```$xslt
kubectl run nginx1 --image=nginx --restart=Never --labels=app=v1
kubectl run nginx2 --image=nginx --restart=Never --labels=app=v1
kubectl run nginx3 --image=nginx --restart=Never --labels=app=v1
```
show lables on pod
```
kubectl get pod --show-labels
```

change the label app=v2 to pod nginx2
```$xslt
kubectl label pod nginx2 app=v2 --overwrite
```

Get the pods label as app
```$xslt
kubectl get pods -l app
```

Get the pods label as app=v2
```$xslt
kubectl get pods -l app=v2
```

Set the annotation on the pods deployed 'description=My Database Application'
```$xslt
kubectl annotate pod nginx1 nginx2 nginx3 description="My Database Application" 
pod/nginx1 annotated
pod/nginx2 annotated
pod/nginx3 annotated
```
Remove these pods to have a clean state in your cluster
```$xslt
kubectl delete pods/nginx1 pods/nginx2 pods/nginx3
kubectl delete po nginx{1..3}
```
<H2>Deployments</H2>


