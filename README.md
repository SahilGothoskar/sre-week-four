# UpCommerce Release Engineering Guide

## Deploying the Stable Version

### Initiate Minikube and Create Namespace

```bash
minikube start
```
![image](https://github.com/SahilGothoskar/sre-week-four/assets/33109078/2c0129c0-3bc3-42d4-a8ef-552f7bfc0c18)


```bash
kubectl create ns sre
```
![image](https://github.com/SahilGothoskar/sre-week-four/assets/33109078/a01ca2e6-33a0-4097-8b6e-e12b93fed06a)

## Create Deployment & Service

```bash
helm install upcommerce ./upcommerce -n sre
```

![image](https://github.com/SahilGothoskar/sre-week-four/assets/33109078/f5608f19-2f24-4779-b7ca-769383e75af3)

Confirm the deployment

```bash
kubectl get deployment -n sre
```


![image](https://github.com/SahilGothoskar/sre-week-four/assets/33109078/fa40fbde-3916-4409-80eb-49d8371011af)

or check the pod

```
kubectl get po -n sre
```

![image](https://github.com/SahilGothoskar/sre-week-four/assets/33109078/3d60591e-ae9c-4f45-9fd5-5114368317af)



## Creating a Canary Version

```bash
canary-deployment.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-canary-app
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Release.Name }}-canary-app
  template:
    metadata:
      labels:
        app: {{ .Release.Name }}-canary-app
    spec:
      containers:
        - name: canary
          image: {{ .Values.canary.image }}
          ports:
            - containerPort: 5003
          imagePullPolicy: {{ .Values.imagePullPolicy }}
          resources:
            limits:
              cpu: {{ .Values.cpuLimit }}
              memory: {{ .Values.memoryLimit }}
```

`canary-service.yml` 
```
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-canary-service
spec:
  selector:
    app: {{ .Release.Name }}-canary-app
  ports:
    - protocol: TCP
      port: 5003
      targetPort: 5003

```


## Deploy the canary 


```
helm upgrade upcommerce ./upcommerce -n sre
```
