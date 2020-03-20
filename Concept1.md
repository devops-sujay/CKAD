<H>Chapter 1:Core Concepts :<H>

Create a namespace called 'mynamespace' and a pod with image nginx called nginx on this namespace
```$xslt
kubectl create namespace mynamespace
kubectl run nginx --image=nginx --restart=Never  -n mynamespace
```
