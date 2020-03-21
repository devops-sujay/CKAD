# Configmap and secrets :

Create Configmap named config with value key=value,key2=value2

```$xslt
k create configmap config --from-literal=key=value --from-literal=key2=value2
```

Display configmap values

```$xslt
k describe cm config
```

Create configmap from a file 

```$xslt
echo -e "key=value" > config.txt
k create configmap cmap --from-file config.txt
```

Configuring a Pod to Use a ConfigMap

Create a new file named config.txt with the following environment variables as key/value pairs on each line.
DB_URL equates to localhost:3306
DB_USERNAME equates to postgres
Create a new ConfigMap named db-config from that file.
Create a Pod named backend that uses the environment variables from the ConfigMap and runs the container with the image nginx.
Shell into the Pod and print out the created environment variables. You should find DB_URL and DB_USERNAME with their appropriate values.

```$xslt
echo -e "DB_URL=localhost:3306\nDB_USERNAME=postgres" > config.txt
kubectl create cm db-config --from-file=config.txt  
kubectl run backend --image=nginx --restart=Never --dry-run  -o yaml >backend.yaml
vi backend.yaml =
```

add the following :
```
envFrom:
    - configMapRef:
        name: db-config
```
Configuring a Pod to Use a Secret

Create a new Secret named db-credentials with the key/value pair db-password=passwd.
Create a Pod named backend that defines uses the Secret as environment variable named DB_PASSWORD and runs the container with the image nginx.
Shell into the Pod and print out the created environment variables. You should find DB_PASSWORD variable.
```$xslt
 kubectl create secret generic db-credentials --from-literal=db-password=password
```
```$xslt
k run backend --image=nginx --restart=Never --dry-run -o yaml >backend-secret.yaml
```
Edit yaml file , add the following
```$xslt
env:
    -name: DB_PASSWORD
        valueFrom:
            secretKeyFrom:
                name: db-credentials
                key: db-password
```

```$xslt

spec:
  containers:
  - image: nginx
    name: backend
    env:
      - name: DB_PASSWORD
        valueFrom:
          secretKeyRef:
            name: db-credentials
            key: db-password
```
k exec -it backend -- /bin/sh
# env | grep DB
DB_PASSWORD=password

    Creating s Security Context for a Pod
Create a Pod named secured that uses the image nginx for a single container. Mount an emptyDir volume to the directory /data/app.
Files created on the volume should use the filesystem group ID 3000.
Get a shell to the running container and create a new file named logs.txt in the directory /data/app. List the contents of the directory and write them down.

```$xslt
kubectl run secured --image=nginx  --restart=Never --dry-run -o yaml >secured.yaml

    volumeMounts:
    - name: data-vol
      mountPath: /data/app
    resources: {}
  volumes:
  - name: data-vol
    emptyDir: {}

```

Defining a Pod’s Resource Requirements

Create a resource quota named apps under the namespace rq-demo using the following YAML definition in the file rq.yaml.
```$xslt
apiVersion: v1
kind: ResourceQuota
metadata:
  name: app
spec:
  hard:
    pods: "2"
    requests.cpu: "2"
    requests.memory: 500m
```

```$xslt
k create namespace rq-demo
k apply -f rq.yaml 
```
Explain the quota
```$xslt
kubectl describe quota --namespace=rq-demo
```
Error:
```$xslt
kaf mypod.yaml -n rq-demo
Error from server (Forbidden): error when creating "mypod.yaml": pods "mypod" is forbidden: exceeded quota: app, requested: requests.memory=1G, used: requests.memory=0, limited: requests.memory=500m
```

<h1>Using Service Account</h1>

Create a new service account named backend-team.
Print out the token for the service account in YAML format.
Create a Pod named backend that uses the image nginx and the identity backend-team for running processes.
Get a shell to the running container and print out the token of the service account.

```$xslt
kubectl create serviceaccount backend-team
k describe serviceaccount backend-team
 k get serviceaccount backend-team -o yaml --export
Flag --export has been deprecated, This flag is deprecated and will be removed in future.
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: null
  name: backend-team
  selfLink: /api/v1/namespaces/pizza/serviceaccounts/backend-team
secrets:
- name: backend-team-token-tlgg4

Get token from Running container :

 cat token
eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJwaXp6YSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJiYWNrZW5kLXRlYW0tdG9rZW4tdGxnZzQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiYmFja2VuZC10ZWFtIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiYjRhZjFiNTUtZTBjZS00MTE4LWE3ZTMtMjg4MWU0MTczNmM2Iiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50OnBpenphOmJhY2tlbmQtdGVhbSJ9.hJ8JCXFByhgyT-27Ypq5XulIx3kdLBl09MOmCBqPT99F4V7MeRblWWADcNjawkfrcIIhUdUCMitr4DzW5o9VCPpkfZLX9q-tvY2iuyBwKesaJtokrNnxNKUmGBck_5j9UvgXI61RSdB9FXhVqs7W-59C7LYLb6fYLdFu9H54DFAu1CsH1O1idH-hO6vhWB7k1_CmGumhM3NmFoCIz_GhtB0HAWhW6FEblwG9ueGVAIxOc8ksAyALH0lDSuQp6j3QcGFyHLwlawOnE-fm8X-8s6w0PQJvT90PLCzLJJ0FPws06jugOOXEu4lbLfZO-BMqG4AXn2L6cYLrqd-wX8nT4A# pwd
/var/run/secrets/kubernetes.io/serviceaccount

```
