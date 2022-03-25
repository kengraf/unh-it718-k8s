# UNH-IT718-k8s
Lab to support K8s discussion in class

It is best you have completed the steps in [Github UNH-IT718-docker](https://github.com/kengraf/UNH-IT718-docker).  The result being a repo of YOUR_NAME/hpa-example:v1 hosted on Docker Hub.  If not, this lesson without edits pulls from the default repo of billiardyoda/hpa-example.

## Lesson 
Now that we have a deploy focused Docker image, it is time to learn about scaling it with Kubernetes.  
If you interested in a more complete deployment with back & front ends check out Google's Kubenetes examples: [GCP demo](https://cloud.google.com/kubernetes-engine/docs/tutorials/guestbook)  

Insure your Cloudshell settings are current.  This is needed if you have to reconnect after an inactivity timeout.
```
gcloud config set project YOUR_PROJECT_ID
gcloud config set compute/zone us-west1-a
```

Create a new cluster for the deployment
```
gcloud container clusters create hpa-example --num-nodes=4
gcloud container clusters list
gcloud container clusters describe hpa-example
```

Apply a "yaml" to deploy what is needed
```
kubectl apply -f hpa-example.yaml
kubectl get -f hpa-example.yaml
kubectl autoscale deployment hpa-example --cpu-percent=50 --min=1 --max=10
kubectl get hpa
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://hpa-example; done"
```

Commands to check status
```
kubectl get deployments
kubectl get services
kubectl rollout status deployment/hpa-example
kubectl get rs
kubectl get pods --show-labels
docker run --rm -it -v ~/.kube/config:/root/.kube/config quay.io/derailed/k9s
```

```
# Run this in a separate terminal
# so that the load generation continues and you can carry on with the rest of the steps
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 1; do wget -b -q -O- http://EXTERNAL_LOADBALANCER_IP/dowork; done"
```


Clean up Kubernetes
```
kubectl delete service hpa-example
kubectl delete deployment hpa-example
kubectl delete hpa hpa-example
kubectl delete pod load-generator
gcloud compute forwarding-rules list
gcloud container clusters delete hpa-example
```



