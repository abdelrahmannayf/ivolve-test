![Step 1](Screenshots/lap16.png) 
2️⃣ إنشاء Secret لكلمات المرور
# ==========================
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
stringData:
  MYSQL_ROOT_PASSWORD: root123
  DB_PASSWORD: ivolve123
EOF

# ==========================
# 3️⃣ إنشاء ConfigMap لإعدادات قاعدة البيانات
# ==========================
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: db-config
data:
  DB_HOST: mysql
  DB_NAME: ivolve
  DB_USER: ivolve_user
EOF

# ==========================
# 4️⃣ نشر MySQL Deployment + Service
# ==========================
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:8.0
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: MYSQL_ROOT_PASSWORD
            - name: MYSQL_DATABASE
              valueFrom:
                configMapKeyRef:
                  name: db-config
                  key: DB_NAME
          ports:
            - containerPort: 3306
          readinessProbe:
            exec:
              command: ["mysqladmin", "ping", "-h", "127.0.0.1"]
            initialDelaySeconds: 10
            periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  selector:
    app: mysql
  ports:
    - port: 3306
EOF

# ==========================
# 5️⃣ نشر Node.js Deployment مع Init Container
# ==========================
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      initContainers:
        - name: init-db
          image: mysql:5.7
          command:
            - sh
            - -c
            - |
              until mysql -h \$DB_HOST -u root -p\$MYSQL_ROOT_PASSWORD -e "SELECT 1;" ; do
                echo "Waiting for MySQL..."
                sleep 3
              done
              mysql -h \$DB_HOST -u root -p\$MYSQL_ROOT_PASSWORD <<EOF2
              CREATE DATABASE IF NOT EXISTS \$DB_NAME;
              CREATE USER IF NOT EXISTS '\$DB_USER'@'%' IDENTIFIED BY '\$DB_PASSWORD';
              GRANT ALL PRIVILEGES ON \$DB_NAME.* TO '\$DB_USER'@'%';
              FLUSH PRIVILEGES;
              EOF2
          env:
            - name: DB_HOST
              valueFrom:
                configMapKeyRef:
                  name: db-config
                  key: DB_HOST
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: MYSQL_ROOT_PASSWORD
            - name: DB_NAME
              valueFrom:
                configMapKeyRef:
                  name: db-config
                  key: DB_NAME
            - name: DB_USER
              valueFrom:
                configMapKeyRef:
                  name: db-config
                  key: DB_USER
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: DB_PASSWORD
      containers:
        - name: node-app
          image: emma175/kubernets-app:1.0
          ports:
            - containerPort: 3000
          env:
            - name: DB_HOST
              valueFrom:
                configMapKeyRef:
                  name: db-config
                  key: DB_HOST
            - name: DB_USER
              valueFrom:
                configMapKeyRef:
                  name: db-config
                  key: DB_USER
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: DB_PASSWORD
EOF

# ==========================
# 6️⃣ إنشاء Service لتطبيق Node.js
# ==========================
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  name: node-app-service
spec:
  type: NodePort
  selector:
    app: node-app
  ports:
    - port: 3000
      targetPort: 3000
EOF

# ==========================
# 7️⃣ التحقق من حالة Pods و Services
# ==========================
kubectl get pods
kubectl get svc

# ==========================
# 8️⃣ فتح تطبيق Node.js في المتصفح (Minikube)
# ==========================
minikube service node-app-service
