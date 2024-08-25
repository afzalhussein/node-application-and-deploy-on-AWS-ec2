# node-application-and-deploy-on-AWS-ec2

Containerizing a Node.js application and deploying it on AWS EC2 involves several steps. In this guide, I'll walk you through the process of containerizing a Node.js application using Docker and then deploying it on an AWS EC2 instance.

### Prerequisites

Before you begin, make sure you have the following tools and accounts set up:

1. **Node.js**: Install Node.js on your local machine. You can download it from the official website.

2. **Docker**: Install Docker on your local machine. You can download it from the official website.

3. **AWS Account**: You need an AWS account to create and manage EC2 instances. Make sure you have your AWS access key and secret key ready.

### Step 1: Create a Node.js Application

For this example, we'll create a simple Node.js application that serves a "Hello, Docker!" message through an HTTP server. Follow these steps:

1. Create a new directory for your project:

   ```bash
   mkdir node-docker-app
   cd node-docker-app
   ```

2. Initialize a Node.js project:

   ```bash
   npm init -y
   ```

3. Install the `express` package to create a basic HTTP server:

   ```bash
   npm install express
   ```

4. Create an `index.js` file with the following content:

   ```javascript
   const express = require('express');
   const app = express();
   const port = process.env.PORT || 3000;

   app.get('/', (req, res) => {
       res.send('Hello, Docker!');
   });

   app.listen(port, () => {
       console.log(`Server is running on port ${port}`);
   });
   ```

### Step 2: Create a Dockerfile

A Dockerfile contains instructions for building a Docker image. Create a `Dockerfile` in the project directory with the following content:

```Dockerfile
# Use an official Node.js runtime as a parent image
FROM node:14

# Set the working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json to the container
COPY package*.json ./

# Install application dependencies
RUN npm install

# Copy the rest of the application code to the container
COPY . .

# Expose a port for the application to listen on
EXPOSE 3000

# Define the command to run the application
CMD [ "node", "index.js" ]
```

### Step 3: Build the Docker Image

Open your terminal, navigate to the project directory containing the `Dockerfile`, and run the following command to build the Docker image:

```bash
docker build -t your-image-name .
```

Replace `your-image-name` with a name for your Docker image (e.g., `my-node-docker-app`).

### Step 4: Deploy the Docker Container on AWS EC2

Now that you have a Docker image, you can deploy it to an AWS EC2 instance.

1. **Launch an AWS EC2 Instance**:

   - Log in to your AWS Management Console.
   - Navigate to the EC2 service.
   - Launch a new EC2 instance. You can choose an Amazon Linux or Ubuntu AMI.
   - Ensure that the instance has a public IP address and security group rules allowing incoming traffic on port 3000 (or the port you specified in your Node.js application).

2. **Transfer the Docker Image**:

   - Use the `docker save` command to save your Docker image to a file:

     ```bash
     docker save -o your-image-name.tar your-image-name
     ```

   - Upload the image file to your EC2 instance using `scp` or other file transfer methods.

3. **Load the Docker Image on EC2**:

   - Connect to your EC2 instance using SSH.

   - Use the `docker load` command to load the Docker image from the uploaded file:

     ```bash
     docker load -i your-image-name.tar
     ```

4. **Run the Docker Container**:

   - Start the Docker container on your EC2 instance:

     ```bash
     docker run -d -p 3000:3000 your-image-name
     ```

   - The `-p` option maps port 3000 inside the container to port 3000 on the EC2 instance.

5. **Access the Application**:

   - Open a web browser and navigate to the public IP address of your EC2 instance followed by port 3000 (e.g., `http://ec2-public-ip:3000`). You should see the "Hello, Docker!" message served by your Node.js application.

### Step 5: Cleanup

To remove the Docker image and container from your EC2 instance when you're done, you can use the following commands:

```bash
docker stop container-id  # Stop the running container
docker rm container-id    # Remove the container
docker rmi your-image-name  # Remove the Docker image
```

### Conclusion

You've containerized a Node.js application using Docker and deployed it on an AWS EC2 instance. This allows you to run your application in a consistent environment and easily scale it as needed. Containerization is a powerful way to package and deploy applications, making them more portable and manageable across different environments.
