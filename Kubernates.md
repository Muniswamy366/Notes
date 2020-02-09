# kubectl Commands

-	`kubectl version`
-	`kubectl cluster-info`
-	`kubectl run nginx1 --image=nginx:alpine` to create deployment for a pod Syntax: kubectl run [container-name] --image=[image-name]
-	`kubectl create -f pod.yml` create a resource Syntax: kubectl create [resource]
-	`kubectl apply -f pod.yml` create or modify a resource Syntax: kubectl apply [resource]
-	`kubectl port-forward podid 8080:80` pods and containers are accessible within kubernetes cluster by default. to expose outside use port-forward Syntax: kubectl port-forward [pod-name] [ports]
-	`kubectl expose ` expose a port for a deployment/pod.
-	`kubectl get pods` information about pods
-	`kubectl get services`
-	`kubectl get all` information about pods, deployment, services
-	`kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml`
-	`kubectl describe secret -n kube-system` copy this token Type:  kubernetes.io/service-account-token
-	`kubectl proxy` to start dashboard
-	`kubectl delete pod pod-id` pod will be recreated Syntax: kubectl delete pod [pod-id]
-	`kubectl delete -f pod.yml` delete pod using yml
-	`kubectl delete deployment nginx1` delete deployment that manages pod, pod will not be recreated. Syntax: kubectl delete deployment [pod-name]
-	``