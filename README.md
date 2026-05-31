Here's a structured reference based on your notes, plus additional commands, a Dockerfile example, and a Docker Compose example to help you refresh and extend your Docker knowledge.

## 📌 Quick Reference: Common Docker Commands

### Container Lifecycle

| Command | Description |
|---------|-------------|
| `docker run -d --name mynginx -p 80:80 nginx` | Run container in background, map port |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers (including stopped) |
| `docker stop <container>` | Gracefully stop a container |
| `docker start <container>` | Start a stopped container |
| `docker restart <container>` | Restart a container |
| `docker rm <container>` | Remove a stopped container |
| `docker rm -f <container>` | Force remove a running container |
| `docker rm -f $(docker ps -aq)` | Remove all containers (force) |
| `docker logs <container>` | Show logs |
| `docker logs --tail 100 -f <container>` | Follow last 100 lines of logs |
| `docker exec -it <container> /bin/bash` | Open interactive shell inside container |

### Images & Registry

| Command | Description |
|---------|-------------|
| `docker images` | List local images |
| `docker rmi <image>` | Remove an image |
| `docker rmi -f $(docker images -q)` | Remove all images |
| `docker build -t myapp:1.0 .` | Build image from Dockerfile |
| `docker tag myapp:1.0 myuser/myapp:1.0` | Tag image for registry |
| `docker push myuser/myapp:1.0` | Push to Docker Hub |
| `docker pull myuser/myapp:1.0` | Pull from registry |
| `docker login` | Log in to registry |
| `docker logout` | Log out |

### Multi-Arch Build (like your `buildx` command)

```bash
# Create a new builder instance (first time)
docker buildx create --name mybuilder --use

# Build and push for both AMD64 and ARM64
docker buildx build --platform linux/amd64,linux/arm64 \
  -t yourusername/yourapp:latest --push .
```

### System & Cleanup

| Command | Description |
|---------|-------------|
| `docker system df` | Show disk usage by images, containers, volumes |
| `docker system prune -a -f` | Remove all stopped containers, unused images, networks |
| `docker volume prune -f` | Remove unused volumes |
| `docker network prune -f` | Remove unused networks |

### Inspect & Debug

| Command | Description |
|---------|-------------|
| `docker inspect <container>` | Show detailed info (JSON) |
| `docker logs <container>` | View stdout/stderr |
| `docker stats` | Real-time resource usage of containers |
| `docker top <container>` | Show running processes inside container |
| `docker cp <container>:/path/file ./local` | Copy file from container to host |

### Networks & Volumes

| Command | Description |
|---------|-------------|
| `docker network create mynet` | Create a bridge network |
| `docker run --network mynet ...` | Attach container to network |
| `docker volume create myvol` | Create named volume |
| `docker run -v myvol:/data ...` | Mount volume into container |

---

## 📄 Dockerfile Example (Production-Ready)

```dockerfile
# Multi-stage build example: Go app
# Stage 1: Build
FROM golang:1.21-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o myapp .

# Stage 2: Final minimal image
FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/myapp .
EXPOSE 8080
CMD ["./myapp"]
```

**Best practices:**
- Use specific tags (`alpine:3.18`, not `latest`)
- Minimize layers by combining `RUN` commands
- Use `.dockerignore` to exclude unnecessary files
- Run as non-root user whenever possible

---

## 🐳 Docker Compose Example (Web + Database)

Create `docker-compose.yml`:

```yaml
version: '3.8'

services:
  web:
    build: .
    image: myapp:latest
    ports:
      - "8080:8080"
    environment:
      - DB_HOST=db
      - DB_USER=myuser
      - DB_PASSWORD=secret
    depends_on:
      - db
    networks:
      - appnet
    volumes:
      - ./uploads:/app/uploads

  db:
    image: postgres:15-alpine
    environment:
      - POSTGRES_DB=mydb
      - POSTGRES_USER=myuser
      - POSTGRES_PASSWORD=secret
    volumes:
      - pgdata:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    networks:
      - appnet

networks:
  appnet:
    driver: bridge

volumes:
  pgdata:
```

### Useful Compose Commands

| Command | Description |
|---------|-------------|
| `docker compose up -d` | Start services in background |
| `docker compose down` | Stop and remove containers, networks |
| `docker compose down -v` | Also remove volumes |
| `docker compose logs -f web` | Follow logs of a service |
| `docker compose exec web /bin/sh` | Open shell in web container |
| `docker compose build --no-cache` | Rebuild without cache |
| `docker compose ps` | List services status |
| `docker compose top` | Show processes inside services |

---

## 🔧 Additional Useful Commands (Not in your list)

```bash
# Save and load images (air-gapped environments)
docker save myapp:1.0 -o myapp.tar
docker load -i myapp.tar

# Commit a container to an image (not recommended for production, but handy for debugging)
docker commit mycontainer myapp:debug

# View port mappings
docker port mycontainer

# Rename a container
docker rename oldname newname

# Pause/unpause container processes
docker pause mycontainer
docker unpause mycontainer

# Wait for container to exit and get exit code
docker wait mycontainer

# Diff filesystem changes in container
docker diff mycontainer

# Show only container IDs (useful for scripting)
docker ps -q
```

---

## 🧪 Example Workflow Based on Your Notes

1. **Build image**  
   `docker build -t aelhajinfra/nginx-app:v1 .`

2. **Run with port mapping**  
   `docker run --name nginx-cimv1 -p 8080:80 -d aelhajinfra/nginx-app:v1`

3. **Test locally**  
   `curl http://localhost:8080`

4. **Tag for Docker Hub**  
   `docker tag aelhajinfra/nginx-app:v1 aelhajinfra/nginx-app:v1-release`

5. **Push (single arch)**  
   `docker push aelhajinfra/nginx-app:v1-release`

6. **Multi-arch push**  
   `docker buildx build --platform linux/amd64,linux/arm64 -t aelhajinfra/nginx-app:v1-release --push .`

7. **Run with Docker Compose** (assumes you have a compose file)  
   `docker compose up -d`

---

## 📚 Quick Troubleshooting

| Problem | Likely command/solution |
|---------|------------------------|
| Permission denied | Add user to docker group: `sudo usermod -aG docker $USER` (then re-login) |
| Port already in use | `docker ps` to see what's using it, or change host port |
| No space left on device | `docker system prune -a -f` |
| Container exits immediately | `docker logs <container>` to see error |
| Cannot connect to container | Check `docker inspect` for IP, verify network |
| Build fails due to architecture | Use `buildx` with `--platform` |
