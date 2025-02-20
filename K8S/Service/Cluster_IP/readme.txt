check service 
1)kubectl get service <service-name> -n <namespace>
kubectl get service
NAME             TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
nginx-service1   ClusterIP   10.96.234.38   <none>        80/TCP    20h

check endpoints
2)kubectl get endpoints <service-name> -n <namespace>
my pods and service in default namespace
kubectl get endpoints
NAME             ENDPOINTS                                   AGE
nginx-service1   10.244.1.3:80,10.244.2.4:80,10.244.3.3:80   20h

Check DNS inside a pod
3)kubectl exec -it <pod-name> -n <namespace> -- /bin/bash
    curl http://<service-name>.<namespace>.svc.cluster.local
sample : 
if one of my pods name is : nginx-deployment1-96b9d695-kqc9l
 kubectl exec -it nginx-deployment1-96b9d695-kqc9l -- /bin/bash
if my service name is  :nginx-service1 and and my service and pods are in default namespace
curl http://nginx-service1.default.svc.cluster.local


4)check logs
kubectl logs <pod-name> -n <namespace>
sample
kubectl logs nginx-deployment1-96b9d695-z9hbd
10.244.1.3 - - [20/Feb/2025:06:21:55 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/7.88.1" "-"


5)2)kubectl get service <service-name> -n <namespace> -o yaml
apiVersion: v1
items:
- apiVersion: v1
  kind: Service
  metadata:
    creationTimestamp: "2025-02-19T09:56:08Z"
    labels:
      component: apiserver
      provider: kubernetes
    name: kubernetes
    namespace: default
    resourceVersion: "457957"
    uid: 38416690-6cbf-408f-9607-28ef5603b876
  spec:
    clusterIP: 10.96.0.1
    clusterIPs:
    - 10.96.0.1
    internalTrafficPolicy: Cluster
    ipFamilies:
    - IPv4
    ipFamilyPolicy: SingleStack
    ports:
    - name: https
      port: 443
      protocol: TCP
      targetPort: 6443
    sessionAffinity: None
    type: ClusterIP
  status:
    loadBalancer: {}
- apiVersion: v1
  kind: Service
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"name":"nginx-service1","namespace":"default"},"spec":{"ports":[{"port":80,"protocol":"TCP","targetPort":80}],"selector":{"app":"nginx"},"type":"ClusterIP"}}
    creationTimestamp: "2025-02-19T09:53:35Z"
    name: nginx-service1
    namespace: default
    resourceVersion: "457697"
    uid: 2518f0e9-e919-4132-a043-c95f1ea2847f
  spec:
    clusterIP: 10.96.234.38
    clusterIPs:
    - 10.96.234.38
    internalTrafficPolicy: Cluster
    ipFamilies:
    - IPv4
    ipFamilyPolicy: SingleStack
    ports:
    - port: 80
      protocol: TCP
      targetPort: 80
    selector:
      app: nginx
    sessionAffinity: None
    type: ClusterIP
  status:
    loadBalancer: {}
kind: List
metadata:
  resourceVersion: ""
