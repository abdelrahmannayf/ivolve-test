# 1) Create Docker volume for nginx logs
docker volume create nginx_logs

# 2) Create bind mount directories
mkdir -p nginx-bind/html

# 3) Create index.html with content
echo "Hello from Bind Mount" > nginx-bind/html/index.html

# 4) Run nginx container with volume + bind mount
docker run -d \
  --name nginx-lab7 \
  -p 8080:80 \
  -v nginx_logs:/var/log/nginx \
  -v $(pwd)/nginx-bind/html:/usr/share/nginx/html \
  nginx

# 5) Verify nginx page
curl http://localhost:8080

# 6) Change index.html content
echo "Hello from Bind Mount - Updated" > nginx-bind/html/index.html

# 7) Verify nginx page again
curl http://localhost:8080

# 8) Verify logs exist in the volume
docker volume inspect nginx_logs

# 9) Stop and remove container
docker stop nginx-lab7
docker rm nginx-lab7

# 10) Delete the volume
docker volume rm nginx_logs
 ![Step 1](Screenshots/lap71.jpg)
![Step 2](Screenshots/lap72.jpg)
