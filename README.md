# UNH-IT718-k8s
Lab to support K8s discussion in class

This is an example of using Kubernetes' horizontal pod autoscaling (HPA).  A container that provides moderate CPU utilization is needed.
One is provided in [Github UNH-IT718-docker](https://github.com/kengraf/UNH-IT718-docker).  The result being a repo of YOUR_NAME/hpa-example:v1 hosted on Docker Hub.  If not, this lesson without edits pulls from the default repo of billiardyoda/hpa-example.

## Lesson 
Now that we have a deploy focused Docker image, it is time to learn about scaling it with Kubernetes.  
If you are interested in a more complete deployment with back & front ends check out Google's Kubenetes examples: [GCP demo](https://cloud.google.com/kubernetes-engine/docs/tutorials/guestbook)  

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

Pull exmaple yaml
```
git clone https://github.com/kengraf/unh-it718-k8s.git
cd unh-it718-k8s
```

Apply a "yaml" to deploy what is needed
```
kubectl apply -f hpa-example.yaml
```

Commands to check status
```
kubectl get -f hpa-example.yaml

# Review separate parts
kubectl get deployments
kubectl get services
kubectl rollout status deployment/hpa-example
kubectl get rs
kubectl get pods --show-labels
# The next command provides a running view of pod usage
docker run --rm -it -v ~/.kube/config:/root/.kube/config quay.io/derailed/k9s
```

```
# Generate load in a separate terminal
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 1; do wget -q -O- http://hpa-example/dowork; done"
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



