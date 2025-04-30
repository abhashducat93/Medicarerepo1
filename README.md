# Medicare

Medicare is a comprehensive healthcare management platform designed to simplify the process of booking appointments, managing patient records, and streamlining pharmacy and product management. 

This project leverages modern web technologies to create a responsive and user-friendly interface, offering features like appointment scheduling, a dynamic admin dashboard, and a seamless checkout system for medical products and services.

## What's New

Medicare is the composition of pharmacy management and the online health checking with the integration of the AI tools.## Table of Contents
- [About](#about)
- [Features](#features)
- [Technologies Used](#technologies-used)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Screenshots](#screenshots)
- [Contributing](#contributing)

## About

MedicareProject is a modern web-based healthcare management system aimed at improving the efficiency and accessibility of medical services. This platform is designed to cater to patients, healthcare providers, and administrators by providing:

- **Appointment Booking**: Effortlessly schedule in-shop or home consultations.
- **Pharmacy Management**: Explore and purchase medical products with ease.
- **Admin Dashboard**: Manage user details, products, and appointments seamlessly.
- **Checkout System**: A simple and secure payment process with dynamic discount calculations.

Built using the MERN stack (MongoDB, Express, React, Node.js) and Tailwind CSS, MedicareProject provides a clean, intuitive, and responsive interface, ensuring accessibility across all devices. 
## Features

- **Appointment Scheduling**: Book and manage appointments for in-shop or home consultations.
- **Pharmacy Management**: Browse and purchase medical products with a seamless checkout process.
- **Admin Dashboard**: Add, edit, and manage users, products, and appointments dynamically.
- **Responsive Design**: Optimized for both desktop and mobile devices.
- **Secure Payments**: Multiple payment options with real-time feedback on selection.
## Technologies Used

- **Frontend**: React.js, Tailwind CSS
- **Backend**: Node.js, Express.js
- **Database**: MongoDB
- **Deployment**: Vercel 
## Getting Started

Follow these steps to set up the project locally:

### Prerequisites
- **Node.js**: v14+ recommended
- **MongoDB**: Local or cloud-based instance (e.g., MongoDB Atlas)
- **Git**: Installed for version control

### Installation

1. **Clone the repository**:
   ```bash
   git clone https://github.com/your-username/medicareproject.git
   cd medicareproject

2. 
**a. Backend**:
```bash
cd server
npm install
```

**b. Frontend**:

```bash
cd client
npm install
```
3. **Set up environment variables**:

```bash
MONGO_URI=<your-mongodb-uri>
JWT_SECRET=<your-jwt-secret>
CLOUDINARY_URL=<your-cloudinary-url> # if applicable
```
4. **Start the Application**:

```bash
cd client
npm run both
```

## Usage

1. **User Actions**:
   - Patients can book appointments for in-shop or at-home consultations.
   - Users can browse and purchase medical products with a secure checkout process.

2. **Admin Dashboard**:
   - Add, edit, and manage patient details, products, and appointments in real time.

3. **Interactive UI**:
   - Experience a fully responsive design optimized for both desktop and mobile devices.

4. **Payment System**:
   - Choose from multiple payment methods with real-time visual feedback on selections.


## 🔗 Links
[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/prince-368909285/)



## Screenshots

![Home](./client/src/assets/bg.jpg)

## Contributing

We welcome contributions to MedicareProject! If you'd like to contribute






The 404 error when running your Dockerized React app typically occurs because Nginx can't find the `index.html` file in the expected location. Let's fix this by ensuring the build files are correctly copied and Nginx is properly configured.

Here's an updated Dockerfile that should resolve the issue:

```dockerfile
# Stage 1: Build the React application
FROM node:18-alpine AS builder

WORKDIR /app

# Copy package files first to leverage Docker cache
COPY client/package.json client/package-lock.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application files
COPY client/ ./

# Build the application (assuming you're using create-react-app)
RUN npm run build

# Stage 2: Serve the application using Nginx
FROM nginx:alpine

# Remove default Nginx configuration
RUN rm -rf /etc/nginx/conf.d/default.conf

# Copy custom Nginx configuration
COPY client/nginx.conf /etc/nginx/conf.d

# Copy the built app from the builder stage to Nginx
COPY --from=builder /app/build /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]
```

You'll also need to create an `nginx.conf` file in your `client/` directory with this content:

```nginx
server {
    listen 80;
    server_name localhost;
    
    location / {
        root /usr/share/nginx/html;
        index index.html index.htm;
        try_files $uri $uri/ /index.html;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }
}
```

### Key Fixes:
1. **Added proper Nginx configuration** - The default Nginx config might not handle React Router properly
2. **Explicit try_files directive** - Ensures all routes fall back to index.html (important for client-side routing)
3. **Removed default config** - Prevents conflicts with our custom configuration

### Steps to implement:
1. Create the `nginx.conf` file in your `client/` directory
2. Rebuild your Docker image: `docker build -t medicare-client .`
3. Run the container: `docker run -p 3000:80 medicare-client`

This should now serve your React application properly, including support for client-side routing. If you're still seeing issues, you might want to:
1. Verify the build output exists in `/app/build` after the build stage
2. Check that your React app's `package.json` has the correct build script
3. Ensure your base route in React Router matches the Nginx configuration




Dockerfile Deep Dive: Multi-Stage React with Nginx
This Dockerfile uses a multi-stage build process to create an optimized production-ready container for a React application served by Nginx. Let's break it down in detail:

Stage 1: Builder Stage (React Application Build)
dockerfile
FROM node:18-alpine AS builder
Base Image: Uses node:18-alpine (Node.js v18 on Alpine Linux)

AS builder: Names this stage "builder" for reference in later stages

Why Alpine?: Alpine is lightweight (~5MB) which keeps image size small

dockerfile
WORKDIR /app
Sets /app as the working directory for all subsequent commands

dockerfile
COPY client/package.json client/package-lock.json ./
Optimization: Copies only package files first to leverage Docker layer caching

If these files don't change, Docker will reuse cached layers for npm install

dockerfile
RUN npm install
Installs all Node.js dependencies

Runs in the context of /app (due to WORKDIR)

dockerfile
COPY client/ ./
Copies the rest of the React application source code

This happens after npm install so code changes don't trigger dependency reinstallation

dockerfile
RUN npm run build
Builds the production-optimized React application

Creates static files in /app/build (default Create React App output directory)

Stage 2: Production Stage (Nginx Server)
dockerfile
FROM nginx:alpine
Starts a new stage using nginx:alpine as base

Doesn't include Node.js or build tools, making the final image smaller (~20MB)

dockerfile
RUN rm -rf /etc/nginx/conf.d/default.conf
Removes Nginx's default configuration

Prevents conflicts with our custom configuration

dockerfile
COPY client/nginx.conf /etc/nginx/conf.d
Copies a custom Nginx configuration file

This should be pre-configured for React routing (handling client-side routing)

dockerfile
COPY --from=builder /app/build /usr/share/nginx/html
Key Multi-Stage Benefit: Copies only the built artifacts from the builder stage

Takes the /app/build contents and places them in Nginx's default web root

Doesn't include Node.js, source code, or dev dependencies

dockerfile
EXPOSE 80
Documents that the container listens on port 80 (HTTP)

Doesn't actually publish the port (that's done with docker run -p)

dockerfile
CMD ["nginx", "-g", "daemon off;"]
Starts Nginx in the foreground (daemon off)

Required for Docker containers (they need a foreground process)

Key Benefits of This Structure
Small Final Image: Only includes necessary production files

Security: No build tools or Node.js in the final image

Layer Caching: Dependency installation is cached unless package files change

Separation of Concerns: Build environment separate from runtime environment

Production Optimized: Uses Nginx, a high-performance web server

Typical nginx.conf for React
While not shown in the Dockerfile, the referenced nginx.conf would typically include:

nginx
server {
    listen 80;
    
    location / {
        root /usr/share/nginx/html;
        index index.html index.htm;
        try_files $uri $uri/ /index.html;
    }
    
    # Other optimizations like gzip, cache headers, etc.
}
The try_files directive is crucial for client-side routing to work properly.
