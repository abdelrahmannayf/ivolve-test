![Step 1](Screenshots/lap14.png)
minikube start
minikube ssh
sudo mkdir -p /mnt/mysql-data
sudo chmod 777 /mnt/mysql-data
exit
kubectl apply -f pv.yaml 
kubectl apply -f secret.yaml
kubectl apply -f service.yaml
kubectl apply -f statefulset.yaml
kubectl get pods
kubectl get pvc
kubectl get pv
kubectl get svc
kubectl exec -it mysql-0 -- mysql -u root -p
root123
