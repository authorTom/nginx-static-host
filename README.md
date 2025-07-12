# NGINX Static Host

A simple Docker setup using NGINX to host fast, secure static websites. This repository provides a straightforward and efficient way to serve static content.

## Versions

This repository offers two versions, each catering to different needs:

*   **Version 1.0**: The initial, basic setup for serving a static website with NGINX.
*   **Version 1.1**: An enhanced version with added security and performance features.

The `Dockerfile` and `nginx.conf` for each version are located within their respective version directories in the repository.

## Features

*   **Lightweight**: Based on the official NGINX Alpine image, keeping the footprint small.
*   **Secure**: Provides a solid foundation for a secure static site. Version 1.1 includes additional security headers.
*   **Performant**: Configured for efficient delivery of static assets with caching headers.
*   **Simple**: Easy to understand and set up, even for those new to Docker or NGINX.

## Setup Instructions

These instructions will guide you through setting up a static site on a host or server.

### 1. Create a Project Folder

First, create a directory for your project and navigate into it.

```bash
mkdir ~/my-static-site && cd ~/my-static-site
```

All subsequent commands should be run from within this directory.

### 2. Add Your Static Files

Place your website's files, such as HTML, CSS, JavaScript, and images, into a sub-folder named `public`.

```bash
mkdir public
echo '<h1>Hello, Docker + NGINX!</h1>' > public/index.html
# (Copy the rest of your site into ./public)
```

NGINX will serve the contents of the `public` directory.

### 3. Add a Custom NGINX Configuration

For most simple websites, the default configuration provided by NGINX is sufficient. However, for more advanced features like clean URLs, enhanced caching, or specific configurations for Single-Page Applications (SPAs), a custom `nginx.conf` is necessary.

You can find the `nginx.conf` files for each version in the `docker/nginx` directory of this repository.

*   **For Version 1.0 (Basic):** This configuration sets up a basic server to listen on port 80 and serve files.
*   **For Version 1.1 (Secure & Performant):** This version includes optimisations such as security headers, caching policies for different file types, and Gzip compression to improve performance.

Choose the configuration that best suits your needs and place it in the root of your project folder. NGINX configuration is controlled by directives organised into blocks or contexts. For example, the `server` block defines the settings for a virtual server, and the `location` block is used to configure how NGINX handles requests for different URIs.

### 4. Create a Dockerfile

The `Dockerfile` is a text file that contains the commands to assemble a Docker image. You will find a `Dockerfile` for each version inside the `docker/nginx` directory.

The `Dockerfile` performs the following steps:
*   **`FROM nginx:alpine`**: Specifies the lightweight `nginx:alpine` image as the base for our build.
*   **`COPY public/ /usr/share/nginx/html`**: Copies your static files from the `public` directory on your host into the webroot of the NGINX server inside the container.
*   **`COPY nginx.conf /etc/nginx/conf.d/default.conf`**: Copies your custom `nginx.conf` into the NGINX configuration directory within the container, overwriting the default configuration.
*   **`EXPOSE 80`**: Informs Docker that the container listens on port 80 at runtime.

Copy the appropriate `Dockerfile` from the repository into the root of your project directory.

### 5. Build the Image

Now, build the Docker image from your `Dockerfile`.

```bash
docker build -t my-static-site:1.0 .
```

The `-t` flag tags the image with a name and version. The `.` at the end specifies that the build context is the current directory.

### 6. Run the Container Locally

Finally, run the container from the image you just built.

```bash
docker run -d --name my-static-site \
  -p 8080:80 \
  my-static-site:1.0
```

*   `-d`: Runs the container in detached mode (in the background).
*   `-p 8080:80`: Maps port 8080 on the host to port 80 inside the container.

You can now visit `http://localhost:8080` in your web browser to see your site.

**Development Workflow:** When you make changes to your site's files in the `public` directory, you will need to rebuild the image and restart the container. For a more rapid development cycle, consider using a bind mount:

```bash
docker run -d -p 8080:80 -v $(pwd)/public:/usr/share/nginx/html nginx:alpine
```

This command mounts the `public` directory on your host directly into the container, so changes are reflected immediately without needing to rebuild the image.
