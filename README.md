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
	kubectl describe pod [POD_NAME]<br />
	kubectl describe pod demo<br />
	```
9. Running a command within a pod<br />
kubectl exec [POD_NAME] -- [command]<br />
kubectl exec demo env<br />
kubectl exec demo ps aux<br />
kubectl exec demo cat /proc/1/mounts<br />
kubectl exec demo ls /<br />
kubectl exec -it [POD_NAME] -- [command]<br />
kubectl exec -it demo -- /bin/bash<br />
kubectl exec -it demo -c demo-container1 -- /bin/bash<br />
10. Getting logs for a pod<br />
kubectl logs [POD_NAME]<br />
kubectl logs demo<br />

### AK8S-04 Creating a GKE Cluster via Cloud Shell
1. Create a Kubernetes cluster<br />
export my_zone=us-central1-a<br />
export my_cluster=standard-cluster-1<br />
gcloud container clusters create $my_cluster --num-nodes 3 --zone $my_zone --enable-ip-alias<br />
2. Modify standard-cluster-1 to have four nodes<br />
gcloud container clusters resize $my_cluster --zone $my_zone --size=4<br />
3. To create a kubeconfig file with the credentials of the current user and print out the content of the kubeconfig file<br />
gcloud container clusters get-credentials $my_cluster --zone $my_zone<br />
vi ~/.kube/config<br />
kubectl config view<br />
4. Print out the cluster information for the active context<br />
kubectl cluster-info<br />
5. print out the active context<br />
kubectl config current-context<br />
6. Print out some details for all the cluster contexts in the kubeconfig file<br />
kubectl config get-contexts<br />
7. Change the active context<br />
kubectl config use-context gke_${GOOGLE_CLOUD_PROJECT}_us-central1-a_standard-cluster-1<br />
8. View the resource usage across the nodes of the cluster<br />
kubectl top nodes<br />
9. Enable bash autocompletion for kubectl<br />
source <(kubectl completion bash)<br />
10. Deploy the latest version of nginx as a Pod named nginx-1<br />
kubectl run nginx-1 --image nginx:latest<br />
11. View all the deployed Pods in the active context cluster<br />
kubectl get pods<br />
12. View the complete details of the Pod you just created<br />
export my_nginx_pod=[your_pod_name]<br />
kubectl describe pod $my_nginx_pod<br />
13. Push a file into a container<br /> 
vi ~/test.html<br />
kubectl cp ~/test.html $my_nginx_pod:/usr/share/nginx/html/test.html<br />
14. Create a service to expose our nginx Pod externally<br />
kubectl expose pod $my_nginx_pod --port 80 --type LoadBalancer<br />
15. View details about services in the cluster<br />
kubectl get services<br />
16. Verify that the nginx container is serving the static HTML file that you copied<br />
curl http://[EXTERNAL_IP]/test.html<br />
17. View the resources being used by the nginx Pod<br />
kubectl top pods<br />
18. Deploy your manifest<br />
kubectl apply -f ./new-nginx-pod.yaml<br />
kubectl get pods<br />
19. Start an interactive bash shell in the nginx container<br />
kubectl exec -it new-nginx /bin/bash<br />
20. Set up port forwarding from Cloud Shell to the nginx Pod (from port 10081 of the Cloud Shell VM to port 80 of the nginx container)<br />
kubectl port-forward new-nginx 10081:80<br />
curl http://127.0.0.1:10081/test.html<br />
21. Display the logs and to stream new logs as they arrive (and also include timestamps) for the new-nginx Pod<br />
kubectl logs new-nginx -f --timestamps

### Create Deployments
1. There are three ways to create a Deployment
<br />a.kubectl apply -f [DEVELOYMENT_FILE]
<br />b.kubectl run [DEPLOYMENT_NAME] \
--image [IMAGE]:[TAG] \
--labels [KEY]=[VALUE] \
--port 8080 \
--generatoe deployment/apps.v1 \
--save-config
<br />c.GCP console

## Reference Link
1. Cloud SDK - gcloud Reference - gcloud container - get-credentials<br />
https://cloud.google.com/sdk/gcloud/reference/container/clusters/get-credentials<br />
