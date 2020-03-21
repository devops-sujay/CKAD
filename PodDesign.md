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

Create a deployment with image nginx:1.7.8, called nginx, having 2 replicas, defining port 80 as the port that this container exposes
```$
k run nginx --image=nginx --port=80 --dry-run -o yaml >q1.yaml 
vi q1.yaml - > change replica:1 to 2, save it
kaf q1.yaml
```
get Yaml of this deployment
```$xslt
kubectl get deploy nginx -o yaml
```
Update nginx image to nginx:1.17.1
```$xslt
kubectl set image deploy nginx nginx=nginx:1.17.1
```
scale deployment to 5 replicas
```$xslt
kubectl scale deploy nginx --replicas=5
```
Auto Scale Deployment pods between 5-10 , when target CPU Utilization at 80%
```$xslt
kubectl autoscale deploy nginx --min=5 --max=10 --cpu-percent=80
Example : k autoscale deploy nginx --min=5 --max=10 --cpu-percent=80 
===>
          horizontalpodautoscaler.autoscaling/nginx autoscaled
k get pods
===>
NAME                     READY   STATUS    RESTARTS   AGE
nginx-554b9c67f9-5bgcv   1/1     Running   0          12s
nginx-554b9c67f9-6g8ww   1/1     Running   0          12s
nginx-554b9c67f9-p6lfz   1/1     Running   0          12s
nginx-554b9c67f9-x7kbf   1/1     Running   0          77s
nginx-554b9c67f9-xhxgs   1/1     Running   0          12s

```
Set image to nginx:1.9.1 on existing deployment
```$xslt
k set image deploy nginx nginx=nginx:1.9.1
```

Create Jobs with image nginx that runs command cat /etc/os-release 
```$xslt
k create job myjob --image=nginx -- cat /etc/os-release
```
Create a job with the image busybox that executes the command 'echo hello;sleep 30;echo world'
```$xslt
k create job hellojob --image=busybox -- /bin/sh -c 'echo hello;sleep 30;echo world'
```

Follow the logs for the pod
```$xslt
k get po
k logs podname
```

Create a job, make it run 5 times, one after the other. Verify its status and delete it
```$xslt
kubectl create job cronjob --image=busybox --dry-run -o yaml -- /bin/sh 'echo hello' >job.yaml
vi job.yaml
Add  completions: 5 in yaml file

cat job.yaml 
apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  name: cronjob
spec:
  completions: 5
  template:
    metadata:
      creationTimestamp: null
    spec:
      containers:
      - command:
        - /bin/sh
        - echo hello
        image: busybox
        name: cronjob
        resources: {}
      restartPolicy: Never
status: {}
```

Create a job to run parallel 5 times

```$xslt
k create job paralleljob --image=busybox --dry-run -o yaml > paralleljob.yaml
```
Add parallelism: 5

```$xslt
k get jobs
NAME          COMPLETIONS   DURATION   AGE
cronjob       5/5           16s        2m44s
paralleljob   5/1 of 5      11s        42s

```

Create job that runs on cron job schedule  "*/1 * * * *" and writes 'date; 
```$xslt
kubectl create cronjob busybox --image=busybox --schedule="*/1 * * * *" -- /bin/sh -c 'date; echo Hello'
```

Get Cron Job 
```$xslt
k get cj
```

Delete Cron Job
```$xslt
k delete cj cronschdulejob  
```
