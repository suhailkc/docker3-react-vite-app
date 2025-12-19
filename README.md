# Docker React Vite App

A React + TypeScript + Vite application containerized with Docker, featuring two deployment options: Node.js `serve` and Nginx.

## 🐳 Docker Overview

This project includes two Docker configurations for deploying the React application:

1. **Dockerfile** - Uses Node.js with `serve` (simpler, good for development/testing)
2. **Dockerfile.nginx** - Uses Nginx (production-ready, optimized for static files)

Both configurations use multi-stage builds to create optimized production images.

## 📋 Prerequisites

- Docker installed on your system
- Docker Compose (optional, for easier management)

## 🚀 Quick Start

### Option 1: Using Node.js Serve (Dockerfile)

Build and run the application using Node.js `serve`:

```bash
# Build the image
docker build -t react-vite-app:serve .

# Run the container
docker run -p 3000:3000 react-vite-app:serve
```

The application will be available at `http://localhost:3000`

### Option 2: Using Nginx (Dockerfile.nginx) - Recommended for Production

Build and run the application using Nginx:

```bash
# Build the image
docker build -f Dockerfile.nginx -t react-vite-app:nginx .

# Run the container
docker run -p 8080:80 react-vite-app:nginx
```

The application will be available at `http://localhost:8080`

## 🏗️ Docker Architecture

### Multi-Stage Build Process

Both Dockerfiles use a two-stage build process:

#### Stage 1: Builder
- Base image: `node:22-alpine`
- Installs dependencies using `pnpm`
- Builds the React application
- Output: Production-ready static files in `/app/dist`

#### Stage 2: Production
- **Dockerfile**: Uses `node:22-alpine` with `serve` package
- **Dockerfile.nginx**: Uses `nginx:alpine` for serving static files

### Why Multi-Stage Builds?

- **Smaller final image**: Only production artifacts are included
- **Security**: Build tools and dependencies are not in the final image
- **Optimization**: Final image contains only what's needed to run the app

## 📁 Docker Files Explained

### Dockerfile
- **Port**: 3000
- **Server**: Node.js `serve` package
- **Use case**: Development, testing, simple deployments
- **Size**: Larger (includes Node.js runtime)

### Dockerfile.nginx
- **Port**: 80 (mapped to host port 8080 in examples)
- **Server**: Nginx
- **Use case**: Production deployments
- **Size**: Smaller (Alpine-based Nginx)
- **Features**: 
  - SPA routing support (handles client-side routing)
  - Optimized for serving static files
  - Better performance and lower resource usage

### nginx.conf
Configuration file for Nginx that:
- Listens on port 80
- Serves files from `/usr/share/nginx/html`
- Handles React Router (SPA) routing with `try_files` directive
- Ensures all routes fall back to `index.html` for client-side routing

### .dockerignore
Excludes unnecessary files from Docker build context:
- `node_modules` (installed in container)
- `.gitignore` (not needed in image)
- `README.md` (not needed in image)

## 🔧 Docker Commands

### Build Commands

```bash
# Build with default Dockerfile (Node serve)
docker build -t react-vite-app:serve .

# Build with Nginx Dockerfile
docker build -f Dockerfile.nginx -t react-vite-app:nginx .

# Build with no cache (fresh build)
docker build --no-cache -t react-vite-app:nginx -f Dockerfile.nginx .
```

### Run Commands

```bash
# Run with Node serve (port 3000)
docker run -d -p 3000:3000 --name react-app-serve react-vite-app:serve

# Run with Nginx (port 80 mapped to 8080)
docker run -d -p 8080:80 --name react-app-nginx react-vite-app:nginx

# Run in interactive mode (for debugging)
docker run -it -p 3000:3000 react-vite-app:serve sh
```

### Management Commands

```bash
# View running containers
docker ps

# View logs
docker logs react-app-nginx
docker logs -f react-app-nginx  # Follow logs

# Stop container
docker stop react-app-nginx

# Remove container
docker rm react-app-nginx

# Remove image
docker rmi react-vite-app:nginx

# View image size
docker images react-vite-app:nginx
```

## 🎯 Development Workflow

### Local Development (without Docker)
```bash
# Install dependencies
pnpm install

# Run development server
pnpm dev

# Build for production
pnpm build
```

### Docker Development Workflow
```bash
# 1. Make changes to your code
# 2. Rebuild the Docker image
docker build -f Dockerfile.nginx -t react-vite-app:nginx .

# 3. Stop and remove old container
docker stop react-app-nginx && docker rm react-app-nginx

# 4. Run new container
docker run -d -p 8080:80 --name react-app-nginx react-vite-app:nginx
```

## 📦 Image Optimization

### Current Optimizations
- ✅ Multi-stage builds
- ✅ Alpine Linux base images (smaller size)
- ✅ `.dockerignore` to exclude unnecessary files
- ✅ Layer caching (dependencies copied before source code)

### Image Size Comparison
- **Dockerfile (Node serve)**: ~150-200MB
- **Dockerfile.nginx**: ~50-80MB

## 🔍 Troubleshooting

### Container won't start
```bash
# Check container logs
docker logs <container-name>

# Check if port is already in use
lsof -i :3000  # or :8080
```

### Build fails
```bash
# Clear Docker build cache
docker builder prune

# Check Dockerfile syntax
docker build --no-cache -f Dockerfile.nginx .
```

### Routing issues (404 on refresh)
- Ensure you're using `Dockerfile.nginx` with the `nginx.conf` file
- The nginx config includes SPA routing support with `try_files`

## 🚢 Production Deployment

### Recommended: Use Dockerfile.nginx

For production deployments, use the Nginx configuration:

```bash
# Build production image
docker build -f Dockerfile.nginx -t react-vite-app:prod .

# Tag for registry (example)
docker tag react-vite-app:prod your-registry/react-vite-app:latest

# Push to registry
docker push your-registry/react-vite-app:latest
```

### Environment Variables
To add environment variables, modify the Dockerfiles to use `ARG` and `ENV` directives, or use `docker run -e`:

```bash
docker run -d -p 8080:80 -e REACT_APP_API_URL=https://api.example.com react-vite-app:nginx
```

## 📚 Additional Resources

- [Docker Documentation](https://docs.docker.com/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [Vite Documentation](https://vitejs.dev/)
- [React Documentation](https://react.dev/)

## 📝 Notes

- The application uses `pnpm` as the package manager
- Both Dockerfiles use Node.js 22 Alpine for the build stage
- The Nginx configuration supports Single Page Application (SPA) routing
- Port mappings can be adjusted based on your needs (e.g., `-p 80:80` for direct port 80 access)
