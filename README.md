# Production-Ready Next.js with Docker, PM2, and NGINX

This repository demonstrates how to containerize a Next.js application for production using Docker, manage the Node.js process with PM2 Runtime, and leverage NGINX as a reverse proxy with caching capabilities.

---

## Prerequisites

- Docker and Docker Compose installed on your machine
- Basic familiarity with command-line Git and Docker

---

## Quick Start with Docker Compose

1. From the root of this project, run:
   ```bash
   docker-compose up --build
   ```
2. Open your browser at `http://localhost/` to see the Next.js app.

This command will:
- Build the Next.js and NGINX images
- Create containers connected via a custom bridge network
- Expose NGINX on port 80

---

## Manual Setup Without Compose

1. **Build the Docker images**:
   ```bash
   docker build -t nextjs-app .
   docker build -t nginx-proxy ./nginx
   ```

2. **Create an isolated network**:
   ```bash
   docker network create app-network
   ```

3. **Launch the Next.js container**:
   ```bash
   docker run -d \
     --name nextjs-container \
     --network app-network \
     nextjs-app
   ```

4. **Start the NGINX container** (linking to the Next.js service):
   ```bash
   docker run -d \
     --name nginx-proxy \
     --network app-network \
     --link nextjs-container:app \
     -p 80:80 \
     nginx-proxy
   ```

> Inside NGINX, the Next.js server can be reached at `http://app:3000`.

---

## PM2 Runtime Integration

PM2 Runtime ensures your application restarts on crashes or after deployments. You can run PM2 commands directly inside the Next.js container:

```bash
# View real-time metrics
docker exec -it nextjs-container pm2 monit

# List managed processes
docker exec -it nextjs-container pm2 list

# Display process details
docker exec -it nextjs-container pm2 show app

# Reload all processes without downtime
docker exec -it nextjs-container pm2 reload all
```

Adjust container names as needed.

---

## Directory Layout

```
├── docker-compose.yml    # Orchestrates Next.js and NGINX services
├── Dockerfile            # Builds the Next.js application image
├── nginx/                # NGINX configuration and Dockerfile
│   ├── default.conf
│   └── Dockerfile
└── src/                  # Your Next.js application source code
```

---

## Customization Tips

- Tailor NGINX’s cache settings in `nginx/default.conf` to suit your traffic patterns.
- Modify the Dockerfile’s build and start scripts to include environment variables or additional build steps.
- Secure NGINX with HTTPS by mounting certificate files and updating the NGINX config.

---

## License

This project is released under the MIT License. Feel free to adapt and extend it for your own Next.js deployments.

