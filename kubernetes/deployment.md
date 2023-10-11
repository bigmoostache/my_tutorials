To deploy your `drazou/dummyapi:v1` image on Kubernetes, you'll need a few YAML files to define the necessary Kubernetes resources. I'll provide you with the basic YAML files you need to get started:

1. **Namespace**: First, let's create a namespace called `tutorial`.

```yaml
# namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: tutorial
```

2. **Deployment**: This will create a pod running your Docker image.

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dummyapi-deployment
  namespace: tutorial
spec:
  replicas: 1
  selector:
    matchLabels:
      app: dummyapi
  template:
    metadata:
      labels:
        app: dummyapi
    spec:
      containers:
      - name: dummyapi-container
        image: drazou/dummyapi:v1
        ports:
        - containerPort: 8087
```

3. **Service**: To expose your pod inside the cluster on port `6678`, you'll need a Service resource.

```yaml
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: dummyapi-service
  namespace: tutorial
spec:
  selector:
    app: dummyapi
  ports:
    - protocol: TCP
      port: 6678
      targetPort: 8087
```

To deploy these resources, first create the namespace, then the deployment, and finally, the service:

```bash
kubectl apply -f namespace.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

This will expose your FastAPI application internally within the Kubernetes cluster on port `6678`. If you want to expose this to the external world (outside the cluster), you can either use a `NodePort` service or an `Ingress` based on your environment and requirements. But for the scope of this request, I've kept it internal to the cluster.


4. **Port Forwarding**:

To use `kubectl port-forward` with a service, you'll forward a port on your local machine to a port on the service. Since we defined the service to expose the application on port `6678` internally within the Kubernetes cluster, you'll port-forward to that port.

Here's the command to do that:

```bash
kubectl port-forward -n tutorial svc/dummyapi-service 8080:6678
```

Here's a breakdown:

- `-n tutorial`: Specifies the namespace (`tutorial`) where the service is located.
- `svc/dummyapi-service`: Specifies that you're port-forwarding to a service (`svc`) named `dummyapi-service`.
- `8080:6678`: This means you're forwarding local port `8080` to the service's port `6678`. You can choose any available local port instead of `8080` if you like.

After executing this command, if everything works as expected, you should be able to access your FastAPI application by navigating to `http://localhost:8080` on your local machine.

Remember, for `kubectl port-forward` to work, the targeted pod must be running. If the pod is in a CrashLoopBackOff or is otherwise not running, the port-forward command will not work.

5. **Second option: Ingress Resource**:

Deploying your application using an Ingress resource allows you to expose it to external traffic, and you can configure routing based on paths or hostnames.

Given that you already have the ingress-nginx controller set up and the domain `tuto.blends.fr` pointed to `57.128.19.183`, here's how you can deploy an Ingress resource to route traffic for this domain to your FastAPI application.

**Ingress Resource**:

```yaml
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dummyapi-ingress
  namespace: tutorial
  annotations:
    # Use the nginx ingress controller
    kubernetes.io/ingress.class: "nginx"
    # If you want to enable SSL redirect, uncomment the next line
    # nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  rules:
  - host: tuto.blends.fr
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: dummyapi-service
            port:
              number: 6678
```

**Apply the Ingress Resource**:

To deploy the Ingress resource, execute the following command:

```bash
kubectl apply -f ingress.yaml
```

Once the Ingress resource is applied, the ingress-nginx controller will pick up the configuration and start routing external traffic from `tuto.blends.fr` to your FastAPI application service (`dummyapi-service`) on port `6678`.

**Access the Application**:

With everything set up, you should be able to access your FastAPI application by navigating to:
```
http://tuto.blends.fr
```

If you plan to use SSL in the future (which is recommended for production use-cases), you would also need to set up TLS certificates (you could use cert-manager for automated Let's Encrypt certificates) and modify the Ingress to use them. But for the scope of this tutorial, the provided setup will expose your service over HTTP.

And that's it! Your students should now have a basic understanding of how to expose applications on Kubernetes using Ingress resources.