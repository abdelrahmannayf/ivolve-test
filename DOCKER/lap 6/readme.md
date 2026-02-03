![Step 1](Screenshots/lap62.png)
![Step 2](Screenshots/lap63.png)
![Step 3](Screenshots/lap64.png)


# 1. Clone the repository
git clone https://github.com/Ibrahim-Adel15/Docker-3.git
cd Docker-3

# 2. Create the Dockerfile
cat <<EOF > Dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install flask
# Setting variables for the 3rd container as requested (production, canada-west)
ENV APP_MODE=production
ENV APP_REGION=canada-west
EXPOSE 8080
CMD ["python", "app.py"]
EOF

# 3. Build the Docker image
docker build -t flask-env-app .

# 4. Create the env file for the 2nd container
echo "APP_MODE=staging" > staging.env
echo "APP_REGION=us-west" >> staging.env

# 5. Run the 3 containers as requested:

# i. Container 1: Variables in the command line (development, us-east)
docker run -d -p 8081:8080 --name container_dev -e APP_MODE=development -e APP_REGION=us-east flask-env-app

# ii. Container 2: Variables from separate file (staging, us-west)
docker run -d -p 8082:8080 --name container_staging --env-file staging.env flask-env-app

# iii. Container 3: Variables from Dockerfile (production, canada-west)
docker run -d -p 8083:8080 --name container_prod flask-env-app

# 6. Verify containers are running
docker ps
![Step 4](Screenshots/lap61.png)
