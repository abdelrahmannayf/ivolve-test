kubectl create namespace ivolve
kubectl get namespaces
kubectl create quota pod-quota --hard=pods=2 -n ivolve
kubectl get quota -n ivolve
kubectl describe quota pod-quota -n ivolve
kubectl run pod1 --image=nginx -n ivolve
kubectl run pod2 --image=nginx -n ivolve
kubectl run pod3 --image=nginx -n …

kubectl create namespace ivolve
kubectl get namespaces
kubectl create quota pod-quota --hard=pods=2 -n ivolve
kubectl get quota -n ivolve
kubectl describe quota pod-quota -n ivolve
kubectl run pod1 --image=nginx -n ivolve
kubectl run pod2 --image=nginx -n ivolve
kubectl run pod3 --image=nginx -n …
![Step 1](Screenshots/lap11.png)
