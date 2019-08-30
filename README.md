# Architecting with Google Kubernetes Engine - Workloads - 20190822

## Command Line
### The kubectl Command/Introspection

1. List of Pods in a cluster<br />
	```
	kubectl get pods
	```
2. kubectl must be configured first<br />
	```
	$HOME/.kube/config
	```
3. Current config<br />
	```
	kubectl config view
	```
4. gcloud container clusters get-credentials - fetch credentials for a running cluster<br />
	```
	gcloud container clusters get-credentials testcluster1 \
	--zone=us-central1-f
	```
5. Kubectl to give you output in YAML format<br />
	```
	kubectl get pods -o=yaml
	```
6. To display the list of pods and wide format for more information<br />
	```
	kubectl get pods -o=wide
	```
7. Getting a list of Pods<br />
	```
	kubectl get pods
	kubectl get pod my-test-app
	kubectl get pod my-test-app -o=yaml
	kubectl get pods -o=wide
	```
8. Describing a Pod<br />
	```
	kubectl describe pod [POD_NAME]
	kubectl describe pod demo
	```
9. Running a command within a pod<br />
	```
	kubectl exec [POD_NAME] -- [command]
	kubectl exec demo env
	kubectl exec demo ps aux
	kubectl exec demo cat /proc/1/mounts
	kubectl exec demo ls /
	kubectl exec -it [POD_NAME] -- [command]
	kubectl exec -it demo -- /bin/bash
	kubectl exec -it demo -c demo-container1 -- /bin/bash
	```
10. Getting logs for a pod<br />
	```
	kubectl logs [POD_NAME]
	kubectl logs demo
	```

### AK8S-04 Creating a GKE Cluster via Cloud Shell
1. Create a Kubernetes cluster<br />
	```
	export my_zone=us-central1-a
	export my_cluster=standard-cluster-1
	gcloud container clusters create $my_cluster --num-nodes 3 --zone $my_zone 	--enable-ip-alias
	```
2. Modify standard-cluster-1 to have four nodes<br />
	```
	gcloud container clusters resize $my_cluster --zone $my_zone --size=4
	```
3. To create a kubeconfig file with the credentials of the current user and print out the content of the kubeconfig file<br />
	```
	gcloud container clusters get-credentials $my_cluster --zone $my_zone
	vi ~/.kube/config
	kubectl config view
	```
4. Print out the cluster information for the active context<br />
	```
	kubectl cluster-info
	```
5. Print out the active context<br />
	```
	kubectl config current-context
	```
6. Print out some details for all the cluster contexts in the kubeconfig file<br />
	```
	kubectl config get-contexts
	```
7. Change the active context<br />
	```
	kubectl config use-context gke_${GOOGLE_CLOUD_PROJECT}_us-central1-a_standard-cluster-1
	```
8. View the resource usage across the nodes of the cluster<br />
	```
	kubectl top nodes
	```
9. Enable bash autocompletion for kubectl<br />
	```
	source <(kubectl completion bash)
	```
10. Deploy the latest version of nginx as a Pod named nginx-1<br />
	```
	kubectl run nginx-1 --image nginx:latest
	```
11. View all the deployed Pods in the active context cluster<br />
	```
	kubectl get pods
	```
12. View the complete details of the Pod you just created<br />
	```
	export my_nginx_pod=[your_pod_name]
	kubectl describe pod $my_nginx_pod
	```
13. Push a file into a container<br /> 
	```
	vi ~/test.html
	kubectl cp ~/test.html $my_nginx_pod:/usr/share/nginx/html/test.html
	```
14. Create a service to expose our nginx Pod externally<br />
	```
	kubectl expose pod $my_nginx_pod --port 80 --type LoadBalancer
	```
15. View details about services in the cluster<br />
	```
	kubectl get services
	```
16. Verify that the nginx container is serving the static HTML file that you copied<br />
	```
	curl http://[EXTERNAL_IP]/test.html
	```
17. View the resources being used by the nginx Pod<br />
	```
	kubectl top pods
	```
18. Deploy your manifest<br />
	```
	kubectl apply -f ./new-nginx-pod.yaml
	kubectl get pods
	```
19. Start an interactive bash shell in the nginx container<br />
	```
	kubectl exec -it new-nginx /bin/bash
	```
20. Set up port forwarding from Cloud Shell to the nginx Pod (from port 10081 of the Cloud Shell VM to port 80 of the nginx container)<br />
	```
	kubectl port-forward new-nginx 10081:80
	curl http://127.0.0.1:10081/test.html
	```
21. Display the logs and to stream new logs as they arrive (and also include timestamps) for the new-nginx Pod<br />
	```
	kubectl logs new-nginx -f --timestamps
	```

### Deployments

1. There are three ways to create a Deployment<br />
	a. Command
	```
	kubectl apply -f [DEVELOYMENT_FILE]
	```
	b. Command
	```
	kubectl run [DEPLOYMENT_NAME] \
	--image [IMAGE]:[TAG] \
	--labels [KEY]=[VALUE] \
	--port 8080 \
	--generatoe deployment/apps.v1 \
	--save-config
	```
	c. GCP Console
2. Output the Deployment config in a YAML format
	```
	kubectl get deployment [DEPLOYMENT_NAME]
	kubectl get deployment nginx-deployment
	kubectl get deployment [DEPLOYMENT_NAME] -o yaml > this.yaml
	```
3. Use the 'describe' command to get detailed info
	```
	kubectl describe deployment [DEPLOYMENT_NAME]
	```

### Deployments - Scaling Deployments
1. Scaling a Deployment manually
	```
	kubectl scale deployment [DEPLOYMENT_NAME] -replicas=5
	```
2. Autoscaling a Deployment
	```
	kubectl autoscale deployment [DEPLOYMENT_NAME] --min=5 --max=15 --cpu-percent=75
	```
### Updating Deployments - Updating Deployments (Rolling Update)

1. Updating a Deployment
	```
	kubectl apply -f [DEPLOYMENT_FILE]
	kubectl set image deployment [DEPLOYMENT_NAME] [IMAGE] [IMAGE]:[TAG]
	```
	```
	kubectl edit \
	  deployment/[DEPLOYMENT_NAME]
	```
	```
	GCP Console - Rolling update
	```

### Updating Deployments - Bule-Green Deployments
1. Applying a bule/green deployment strategy
	```
	kubectl apply -f my-app-v2.yaml
	```
	```
	kubectl patch service my-app-service -p \
	'{"spec":{"selector":{"version":"v2"}}}
	```

### Updating Deployments - Canary Deployments
1. Applying a canary deployment
	```
	kubectl apply -f my-app-v2.yaml
	kubectl scale deploy/my-app-v2 -replicas=10
	kubectl delete -f my-app-v1.yaml
	```
2. Rolling back a Deployment
	```
	kubectl rollout undo deployment
	```
	```
	kubectl rollout undo deployment [DEPLOYMENT_NAME] --to-revision=2
	```
	```
	kubectl rollout history deployment [DEPLOYMENT_NAME] --revision=2
	```

### Managing Deployments - MAnaging Deployments
1. Pasuing a Deployment
	```
	kubectl rollout pause deployment [DEPLOYMENT_NAME]
	```
2. Resuming a Deployment
	```
	kubectl rollout resume deployment [DEPLOYMENT_NAME]
	```
3. Monitoring a Deployment
	```
	kubectl rollout status deployment [DEPLOYMENT_NAME]
	```
4. Deleting a Deployment
	```
	kubectl delete deployment [DEPLOYMENT_NAME]
	```


## Reference Link
1. Cloud SDK - gcloud Reference - gcloud container - get-credentials<br />
https://cloud.google.com/sdk/gcloud/reference/container/clusters/get-credentials<br />
