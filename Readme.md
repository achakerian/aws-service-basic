# TypeScript Web Application Deployment to AWS EC2

This guide provides step-by-step instructions to create a simple web application using TypeScript and deploy it to AWS EC2.

## Prerequisites
- **Node.js** (v14 or later) and **npm** installed.
- **AWS CLI** installed and configured with appropriate permissions.
- **EC2 Key Pair** for SSH access.
- **Git** installed.

## Step 1: Initialize a TypeScript Web Application

1. **Create a new directory for your project**:
    ```bash
    mkdir my-typescript-app
    cd my-typescript-app
    ```

2. **Initialize npm**:
    ```bash
    npm init -y
    ```

3. **Install TypeScript and other dependencies**:
    ```bash
    npm install typescript express @types/express
    ```

4. **Initialize TypeScript configuration**:
    ```bash
    npx tsc --init
    ```

5. **Update `tsconfig.json`**:
    In `tsconfig.json`, ensure the `outDir` is set to `./dist`, and `"esModuleInterop": true` is set to `true`.

6. **Create the Application**:
    Create a new folder `src` and add a file `app.ts`:
    ```typescript
    // src/app.ts
    import express from 'express';

    const app = express();
    const port = process.env.PORT || 3000;

    app.get('/', (req, res) => {
      res.send('Hello, World! This is a TypeScript web app.');
    });

    app.listen(port, () => {
      console.log(`Server is running on http://localhost:${port}`);
    });
    ```

7. **Compile the TypeScript code**:
    ```bash
    npx tsc
    ```

8. **Run the Application**:
    After compilation, run the app using Node.js:
    ```bash
    node dist/app.js
    ```

    Test it by navigating to `http://localhost:3000` in your browser.

## Step 2: Prepare for Deployment

1. **Create a `.gitignore` file**:
    ```bash
    node_modules
    dist
    ```

2. **Create a `package.json` script for production**:
    In `package.json`, add this script to handle building and starting the app:
    ```json
    "scripts": {
      "start": "node dist/app.js",
      "build": "tsc"
    }
    ```

3. **Initialize Git**:
    ```bash
    git init
    git add .
    git commit -m "Initial TypeScript web app commit"
    ```

## Step 3: Launch an EC2 Instance

1. **Login to AWS Console** and navigate to the **EC2 Dashboard**.

2. **Launch a new EC2 instance**:
    - Choose **Amazon Linux 2** as the AMI.
    - Choose an instance type like **t2.micro**.
    - **Configure Security Group**: Allow inbound HTTP (port 80) and SSH (port 22) access.

3. **Connect to your EC2 instance** using SSH:
    ```bash
    ssh -i path/to/key.pem ec2-user@<EC2-PUBLIC-IP>
    ```

## Step 4: Set Up the EC2 Instance for the Web Application

1. **Update the EC2 instance**:
    ```bash
    sudo yum update -y
    ```

2. **Install Node.js** on the EC2 instance:
    ```bash
    curl -sL https://rpm.nodesource.com/setup_16.x | sudo bash -
    sudo yum install -y nodejs
    ```

3. **Install Git**:
    ```bash
    sudo yum install git -y
    ```

## Step 5: Deploy the Web Application to EC2

1. **Clone your repository** from GitHub or manually transfer the files:
    ```bash
    git clone https://github.com/your-username/my-typescript-app.git
    cd my-typescript-app
    ```

2. **Install dependencies**:
    ```bash
    npm install
    ```

3. **Build the TypeScript code**:
    ```bash
    npm run build
    ```

4. **Start the application**:
    ```bash
    npm start
    ```

5. **Verify** the application is running by visiting the EC2 public IP:
    ```bash
    http://<EC2-PUBLIC-IP>:3000
    ```

## Step 6: Configure EC2 for Production (Optional)

1. **Install PM2** to keep your app running:
    ```bash
    sudo npm install -g pm2
    pm2 start dist/app.js
    pm2 startup
    ```

2. **Set up NGINX** as a reverse proxy (optional):
    - Install NGINX:
      ```bash
      sudo amazon-linux-extras install nginx1
      sudo systemctl start nginx
      sudo systemctl enable nginx
      ```

    - Configure NGINX to proxy requests to your Node.js app:
      ```bash
      sudo nano /etc/nginx/nginx.conf
      ```

      Add the following inside the `server` block:
      ```nginx
      location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection 'upgrade';
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
      }
      ```

    - Restart NGINX:
      ```bash
      sudo systemctl restart nginx
      ```

3. **Test** your application by visiting `http://<EC2-PUBLIC-IP>`.

## Step 7: Set Up Automatic Deployment (Optional)

1. **Install and configure Docker** to containerize the app, or set up CI/CD using GitHub Actions or AWS CodeDeploy for automatic deployment.

---

By following these steps, you should have a TypeScript web application running on an AWS EC2 instance. You can further optimise the setup using load balancers, HTTPS, or automatic scaling depending on your needs.
