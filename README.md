# coreapi-k8s-svc-discovery-example
This app is example which is showing k8s Service Disvovery 

## Create two instances app in two others namespaces
```
kubectl create ns app-namespace-1
kubectl apply -f app.yaml -n app-namespace-1

kubectl create ns app-namespace-2
kubectl apply -f app.yaml -n app-namespace-2
```

## Access to app in k8s
```
kubectl get svc -n app-namespace-1
NAME                        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
aspcore-svc-discovery-app   ClusterIP   10.96.30.238   <none>        80/TCP    45h

kubectl get svc -n app-namespace-2
NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
aspcore-svc-discovery-app   ClusterIP   10.102.59.133   <none>        80/TCP    45h
```
You can call api by using ip
```
http://10.96.30.238/api/values
http://10.102.59.133/api/values
```

### Access to app by k8s CoreDns
Kubernetes has bulit-in dns server coreDNS. Inside k8s pod you can use coreDNS dns names to have access to other pod. App in service in namespace app-namespace-1 can call api from app in namespace app-namespace-2 and from app-namespace-1 call api in app-namespace-2.

DNS records A
```
service-name (works only inside namespace)
service-name.namespace-name
service-name.namespace-name.svc.cluster.info
```

Example records A
``` 
aspcore-svc-discovery-app.app-namespace-2/api/values
aspcore-svc-discovery-app.app-namespace-2.svc.cluster.local/api/values

aspcore-svc-discovery-app.app-namespace-2/api/values
aspcore-svc-discovery-app.app-namespace-2.svc.cluster.local/api/values
```

### App.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: aspcore-svc-discovery-app
  labels:
    app: aspcore-svc-discovery-app
spec:
  containers:
  - name: aspcore-svc-discovery-app
    image: aspcore-svc-discovery:20200324T211500
    resources: {}
    ports:
    - containerPort: 80
      name: http
---
kind: Service
apiVersion: v1
metadata:
  name: aspcore-svc-discovery-app
spec:
  selector:
    app: aspcore-svc-discovery-app
  type: ClusterIP
  ports:
  - name: http
    port: 80
    targetPort: 80
```

