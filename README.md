# Architecting with Google Kubernetes Engine - Workloads - 20190822

## Command Line
1. gcloud container clusters get-credentials - fetch credentials for a running cluster<br />
gcloud container clusters get-credentials testcluster1 \<br />
--zone=us-central1-f<br />
2. Kubectl to give you output in YAML format<br />
kubectl get pods -o=yaml<br />
3. To display the list of pods and wide format for more information<br />
kubectl get pods -o=wide<br />
4. Getting a list of Pods<br />
kubectl get pods<br />
5. Describing a Pod<br />
kubectl describe pod demo<br />
6. Running a command within a pod<br />
kubectl exec demo env<br />
kubectl exec demo ps aux<br />
kubectl exec demo cat /proc/1/mounts<br />
kubectl exec demo ls /<br />
kubectl exec -it demo -- /bin/bash<br />
kubectl exec -it demo -c demo-container1 -- /bin/bash<br />
7. Getting logs for a pod<br />
kubectl logs demo<br />

## Reference Link
1. Cloud SDK - gcloud Reference - gcloud container - get-credentials<br />
https://cloud.google.com/sdk/gcloud/reference/container/clusters/get-credentials<br />
