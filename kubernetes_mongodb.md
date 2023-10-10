kubectl get nodes
kubectl label nodes <node> <key>=<value>
kubectl label nodes blends-main size=large

## Create a namespace

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: mongodb-namespace
```


## Create storage class


```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: mongodb-storageclass
  namespace: mongodb-namespace
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
```

## Create persistent volume 

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
    name: mongodb-pv
    namespace: mongodb-namespace
spec:
  capacity:
    storage: 1.6Ti
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: mongodb-storageclass
  local:
    path: /mnt/data
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: size
          operator: In
          values:
            - large
```

## Create persistent volume claim

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
    name: mongodb-pvc
    namespace: mongodb-namespace
spec:
  storageClassName: mongodb-storageclass
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 1.6Ti
```



```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongodb-configmap
  namespace: mongodb-namespace
data:
  database_url: mongodb-service
```



```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  namespace: mongodb-namespace
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
        volumeMounts:     # Added this to mount the volume at /data/db, MongoDB's default data directory
        - name: mongodb-persistent-storage
          mountPath: /data/db
      volumes:           # Added this to reference the PVC
      - name: mongodb-persistent-storage
        persistentVolumeClaim:
          claimName: mongodb-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
  namespace: mongodb-namespace
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
```




```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-express-deployment
  namespace: mongodb-namespace
  labels:
    app: mongodb-express
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb-express
  template:
    metadata:
      labels:
        app: mongodb-express
    spec:
      containers:
        - name: mongodb-express
          image: mongo-express
          ports:
            - containerPort: 8081
          env:
            - name: ME_CONFIG_MONGODB_ADMINUSERNAME
              valueFrom:
                secretKeyRef:
                  name: mongodb-secret
                  key: mongo-root-username
            - name: ME_CONFIG_MONGODB_ADMINPASSWORD
              valueFrom:
                secretKeyRef:
                  name: mongodb-secret
                  key: mongo-root-password
            - name: ME_CONFIG_MONGODB_SERVER
              valueFrom:
                configMapKeyRef:
                  name: mongodb-configmap
                  key: database_url
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb-express-service
  namespace: mongodb-namespace
spec:
  selector:
    app: mongodb-express
  # type: LoadBalancer # Uncomment for external service
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      # nodePort: 30000 # Uncomment for external service
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mongodb-ingress
  namespace: mongodb-namespace
spec:
  # tls:
  # - hosts:
  #   - myapp.com
  #     secretName: myapp-secret-tls
  rules:
    - host: mongo.blends.fr
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: mongodb-express-service
                port:
                  number: 8081
```

^Cdebian@c2-7-gra11:~kubectl run -it --rm debug --image=busybox -- sh -n mongodb-namespacece

# then, connect to the debug pod's shell
kubectl exec -it debug -- sh -n mongodb-namespace