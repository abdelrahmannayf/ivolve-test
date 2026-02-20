![1](screenshots/223.png)
![1](screenshots/22.png)
![1](screenshots/220.png)
![1](screenshots/221.png)
🚀 Java Application CI/CD Pipeline (Lab 22)
An automated CI/CD pipeline for a Java Spring Boot application, built and deployed using industry-standard DevOps tools.

🛠 Tech Stack
Java & Maven: For application development and unit testing.

Jenkins: Orchestrating the entire CI/CD pipeline.

Docker: Containerizing the application into lightweight images.

Docker Hub: Acting as a central registry for versioned images.

Kubernetes (Kind): The target environment for deploying and managing the application.

🛣 Pipeline Stages
The Jenkinsfile defines the following automated stages:

Run Unit Test: Validates the source code using mvn test.

Build App: Compiles and packages the code into an executable .jar file using mvn package.

Build Docker Image: Creates a Docker image tagged with the specific Jenkins Build Number.

Push Docker Image: Uploads the newly created image to the Docker Hub repository.

Deploy to Kubernetes: Updates the deployment.yaml and applies the changes to the cluster using kubectl apply.

⚙️ Configuration & Setup
1. Permissions
To ensure Jenkins can interact with Docker and Kubernetes, the following permissions were configured:

Bash
sudo usermod -aG docker jenkins
sudo chown -R jenkins:jenkins /var/lib/jenkins/.kube
sudo systemctl restart jenkins
2. Environment Variables
The following variables must be set in the Jenkinsfile:

DOCKER_IMAGE: Your Docker Hub repository (e.g., username/app-name).

DEPLOYMENT_FILE: The path to your Kubernetes manifest (e.g., deployment.yaml).

📊 Final Results
✅ Success: Fully automated "Green" pipeline in Jenkins.

✅ Version Control: Numbered image tags successfully pushed to Docker Hub.

✅ Live Deployment: Application is up and running on the Kubernetes cluster.

![1](screenshots/222.png)
🚀 Full CI/CD Hands-on Guide (Lab 22)
Phase 1: Preparation & Permissions
Before running the pipeline, Jenkins must have the authority to control Docker and Kubernetes:

Bash
# 1. Add Jenkins to Docker group
sudo usermod -aG docker jenkins

# 2. Grant Jenkins access to Kubernetes config
sudo chown -R jenkins:jenkins /var/lib/jenkins/.kube

# 3. Apply changes by restarting Jenkins
sudo systemctl restart jenkins
Phase 2: Building & Pushing (Pipeline Logic)
These commands are executed inside your project directory to create and ship the application:

Bash
# 4. Build the Java Application package
mvn clean package

# 5. Build Docker Image with a tag (example tag: 11)
docker build -t abdelrahmannayf/jenkins-app:11 .

# 6. Push the image to Docker Hub
docker push abdelrahmannayf/jenkins-app:11
Phase 3: Kubernetes Deployment & Scheduling Fixes
Deploy the app and fix the "Pending" status issues you encountered:

Bash
# 7. Apply the Kubernetes Deployment file
kubectl apply -f deployment.yaml

# 8. Un-taint Control Plane (Allow it to run pods)
kubectl taint nodes --all node-role.kubernetes.io/control-plane-

# 9. Clear Taints from Worker node (The critical fix)
kubectl patch node ivolve-worker -p '{"spec":{"taints":[]}}'

# 10. Clean up old/unused pods to free resources
kubectl delete pod app-pod mysql random-pod
Phase 4: Verification & Access
The final step to see your app live in the browser:

Bash
# 11. Check if the Pod is "Running"
kubectl get pods

# 12. Create a Service to expose the app (If not in YAML)
kubectl expose deployment jenkins-app --type=NodePort --name=jenkins-app-service --port=8080

# 13. Forward the port to your Localhost
# KEEP THIS TERMINAL OPEN
kubectl port-forward service/jenkins-app-service 8081:8080
Phase 5: Result
Open your browser and navigate to:
http://localhost:8081

Final Tip: Always use kubectl get all to monitor the status of your cluster.
