check service 
1)kubectl get service <service-name> -n <namespace>

2)kubectl get service <service-name> -n <namespace> -o yaml
check endpoints
3)kubectl get endpoints <service-name> -n <namespace>

Check DNS inside a pod
4)kubectl exec -it <pod-name> -n <namespace> -- /bin/bash
    curl http://<service-name>.<namespace>.svc.cluster.local

check logs
kubectl logs <pod-name> -n <namespace>
