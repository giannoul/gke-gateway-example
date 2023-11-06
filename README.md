# gke-gateway-example
This is an example setup for using GKE Gateway

### Kubernetes Resources Used

#### Gateway
https://gateway-api.sigs.k8s.io/api-types/gateway/

#### HTTPRoute
https://gateway-api.sigs.k8s.io/api-types/httproute/


#### How To Apply
Make sure that your k8s cluster is using `gateway-api`. You can modify it via e.g.:
```
gcloud container clusters update cluster-2 --gateway-api=standard --location=us-central1-c
```

Apply manifests:
```
kubectl apply -f manifests/gateway.yaml
kubectl apply -f manifests/web.yaml
kubectl apply -f manifests/api.yaml
kubectl apply -f manifests/http-routes.yaml
```

Check the resources that have been created:
```
# kubectl get Gateway
NAME            CLASS         ADDRESS          PROGRAMMED   AGE
external-http   gke-l7-gxlb   34.107.168.157   True         30m

# kubectl get HTTPRoute
NAME   HOSTNAMES   AGE
api                26m
web                26m
```

Test the routing of the requests:
```
$ curl -v -H 'IGEnvironment: web' 34.107.168.157
*   Trying 34.107.168.157:80...
* Connected to 34.107.168.157 (34.107.168.157) port 80 (#0)
> GET / HTTP/1.1
> Host: 34.107.168.157
> User-Agent: curl/7.81.0
> Accept: */*
> IGEnvironment: web
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Date: Mon, 06 Nov 2023 13:02:50 GMT
< Content-Length: 256
< Content-Type: text/plain; charset=utf-8
< Via: 1.1 google
< 
{
  "name": "web",
  "uri": "/",
  "type": "HTTP",
  "ip_addresses": [
    "10.44.0.4"
  ],
  "start_time": "2023-11-06T13:02:50.205093",
  "end_time": "2023-11-06T13:02:50.205156",
  "duration": "63.009µs",
  "body": "Response from web",
  "code": 200
}
```

```
$ curl -v -H 'IGEnvironment: api' 34.107.168.157
*   Trying 34.107.168.157:80...
* Connected to 34.107.168.157 (34.107.168.157) port 80 (#0)
> GET / HTTP/1.1
> Host: 34.107.168.157
> User-Agent: curl/7.81.0
> Accept: */*
> IGEnvironment: api
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Date: Mon, 06 Nov 2023 13:02:56 GMT
< Content-Length: 256
< Content-Type: text/plain; charset=utf-8
< Via: 1.1 google
< 
{
  "name": "api",
  "uri": "/",
  "type": "HTTP",
  "ip_addresses": [
    "10.44.2.7"
  ],
  "start_time": "2023-11-06T13:02:56.094482",
  "end_time": "2023-11-06T13:02:56.094581",
  "duration": "98.772µs",
  "body": "Response from api",
  "code": 200
}
```
