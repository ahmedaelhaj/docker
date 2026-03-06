# docker login 
# docker login -u username -p pwd
# docker logout
# docker top container-name
# docker version
# List running containers
docker ps
# List all containers
docker ps -a
# Start contaier
docker start 0029399333
# Stop container
docker stop 110223939
# List all images
docker images
# Container logs
docker logs 007b402fb4da
# access the container ssh to
docker exec -it container-name /bin/bash
docker exec -it container-name /bin/sh
# Remove image
docker rmi aelhajinfra/nginx-app
# Remove image forcefully
docker rmi -f  aelhajinfra/nginx-app:v1
# Remove contaier
docker rm aelhajinfra/nginx-app
# Remove container forcefully
docker rm -f  aelhajinfra/nginx-app:v1
# Remove all container forcefully
docker rm -f $(docker ps -a -q)
# Remove all images forcefully
docker rmi -f $(docker images -q)
# Docker build
docker build -t aelhajinfra/nginx-app:v1 .
# Docker run
# Exposed port on the Dockerfile
docker run --name nginx-cimv1 -d aelhajinfra/nginx-app:v1
# Exposed port not mentioned on the Dockerfile or override here
docker run --name nginx-cimv1 -p 80:80 -d aelhajinfra/nginx-app:v1
# Change images Tag
docker-Dockerfile % docker tag aelhajinfra/nginx-app:v1 aelhajinfra/nginx-app:v1-release
# Bush image to docker hub
docker push aelhajinfra/nginx-app:v1-release 
# Fix Arch mis-match Build and push for both architectures
docker buildx build --platform linux/amd64,linux/arm64 \
  -t aelhajinfra/nginx-app:v1-release --push .
# push exsistant img to a diffrent docker hub
# 1- Tag the existing image
# First, tag your existing image with the new repository
docker tag existing-image:tag new-username/new-repo:new-tag
# Then push it
docker push new-username/new-repo:new-tag
# 2- Tag during push
# Tag and push in one step
docker push original-image:tag new-username/new-repo:new-tag