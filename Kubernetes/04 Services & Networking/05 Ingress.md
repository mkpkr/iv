What if we add another externally accessible service and another LoadBalancer.

We would then need another load balancer or proxy, that could redirect to the correct LoadBalancer service based on the URL path.

Every time you add a new service you must reconfigure the load balancer.

Also you need to enable SSL for your applications, but where? Application level? Proxy level? LoadBalancer level?

Ideally we want this configuration all in one place.

Ingress helps your users access your application via a single externally accessible URL that you can configure to route to different services within your cluster based on the URL path, and at the same time implement SSL too.

Think of Ingress as a layer 7 load balancer built in to the k8s cluster.

![[Pasted image 20250903211241.png]]
You still must expose ingress as a NodePort or cloud native LoadBalancer.

How does it load balance? How does it implement SSL?

Without ingress, how would you do these? A reverse proxy/load balancing solution such as Nginx or HAproxy, deploy them on k8s cluster, define routes, configure SSL certificates etc.

Ingress is similar, deploy a supported solution (GCP Load Balancer & Nginx are currently supported by k8s; others include HA Proxy, Istio, Traefik) and then specify a set of rules to configure this.

Deploy Ingress Controller (not in k8s by default) and configure Ingress Resources (like we configured other services).

# Controller

We will use Nginx.

Note I think its easier to use Helm charts to deploy, but this is the manual way:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ingress-controller
 
spec:
  replicas: 1
  selector:
    matchLabels:
      name: nginx-ingress
  template:
    metadata:
      labels:
        name: nginx-ingress
    spec:
      containers:
      - name: nginx-ingress-controller
        image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller
 
      args:
      - /nginx-ingress-controller
      - --configmap-$(POD_NAMESPACE)/nginx-configuration
 
      env:
      - name: POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namesapce
 
      ports
      - name: http
        containerPort: 80
      - name: https
        containerPort: 443


```

We also need a NodePort Service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-ingress
 
spec:
  type: NodePort
 
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
    name: http
  - port: 443
    targetPort: 443
    protocol: TCP
    name: https
 
  selector:
    name: nginx-ingress
```

We use a config map, so when you make configuration changes, you will just need to update the config map, and not worry about editing the nginx deployment:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-configuration
…
```

And a service account with correct roles for the Ingress:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nginx-ingress-serviceaccount
…
```

# Ingress Resource

Configure rules

```yaml
apiVersion: extensions/v1beta1      # deprecated, now networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-wear
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
 
spec:
  backend:
    serviceName: wear-service
    servicePort: 80
```

This will route all traffic to the wear-service.

We can have multiple rules, each rule handles a specific type of request (i.e. URL):

## Path Rules

![[Pasted image 20250903211447.png]]
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-wear-watch
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
 
spec:
  rules:
  - http:
      paths:
      - path: /wear
        backend:
          serviceName: wear-service
          servicePort: 80
      - path: /watch
        backend:
          serviceName: watch-service
          servicePort: 80
```

## Host Rules

![[Pasted image 20250903211519.png]]

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-wear-watch
 
spec:
  rules:
  - host: wear.my-online-store.com    # we did not specify this previously; if we leave it out then default is *
    http:
      paths:
      - backend:
          serviceName: wear-service
          servicePort: 80
  - host: watch.my-online-store.com
    http:
      paths:
      - backend:
          serviceName: watch-service
          servicePort: 80
```

We have only one path mapping per rule in this example, but we could add more so we have both host and path mapping.

## Default Backend

If the user attempts to access a URL that does not match, they are directed to the Default Backend (see `kubectl describe ingress <ingress>` to see the default backend).

It is shown as default-http-backend:80 - we must deploy that service.