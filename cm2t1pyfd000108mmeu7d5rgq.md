---
title: "How to Host a React App on an EC2 Instance: A Step-by-Step Guide"
seoTitle: "How to Host a React App on AWS EC2: Step-by-Step Guide"
seoDescription: "Learn how to deploy your React app on an AWS EC2 instance with this comprehensive step-by-step guide. From creating the instance to configuring Nginx, follo"
datePublished: Mon Oct 28 2024 13:19:44 GMT+0000 (Coordinated Universal Time)
cuid: cm2t1pyfd000108mmeu7d5rgq
slug: how-to-host-a-react-app-on-an-ec2-instance-a-step-by-step-guide
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1730115205789/7dbd416d-fdb1-4876-9e2f-a2a4c52b018e.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1730121462476/972c29e5-2a87-4102-bd66-0f0498e04509.webp
tags: web-development, nginx, nodejs, cloud-computing, reactjs, aws-ec2, aws-tutorials, deploy-react-app

---

Hosting your React app on an EC2 instance is a great way to have more control over your web application’s infrastructure. In this guide, I'll walk you through each step, from setting up the EC2 instance to deploying your app. Whether you're a beginner or have some experience, this guide is for you!

## **Prerequisites**

Before we dive in, make sure you have the following:

* A basic React app ready to deploy.
    
* An AWS account.
    
* Basic knowledge of SSH, Linux commands, and AWS services.
    

---

### Step 1: **Create an EC2 Instance**

1. **Log into your AWS Console**  
    Go to the [AWS Management Console](https://aws.amazon.com/) and log in to your account.
    
2. **Navigate to EC2**  
    From the AWS Console, search for **EC2** and click on **Launch Instance**.
    
3. **Configure the Instance**
    
    * **Choose an Amazon Machine Image (AMI)**: Select the latest version of **Ubuntu** (20.04 or higher).
        
    * **Choose Instance Type**: For most basic projects, a t2.micro instance (eligible for free tier) works fine.
        
    * **Configure Instance Details**: Keep the default settings.
        
    * **Add Storage**: Stick with the default size unless your app requires more space.
        
    * **Add Tags**: This is optional, but you can tag your instance for easy identification.
        
    * **Configure Security Group**: Allow the following:
        
        * **HTTP (port 80)** for web access
            
        * **SSH (port 22)** for remote login
            
        * **HTTPS (port 443)** if you plan to use SSL.
            
4. **Launch the Instance**  
    Choose or create a new key pair to SSH into your instance, then launch the instance. Your EC2 instance will take a few moments to spin up.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730115368983/0120a247-afe7-4694-9787-4437f7eead20.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730115459702/3eb80cc8-b39c-4314-a7db-fdfd66cedbba.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730115475636/0acd1251-41c1-4b09-a0cf-ac763720f62b.png align="center")
    

---

### Step 2: **Connect to Your EC2 Instance**

1. **Download your private key (.pem file)**  
    Ensure you have the `.pem` file of the key pair you created earlier.
    
2. **Connect via SSH**  
    Open your terminal (or Git Bash on Windows) and run the following command to SSH into your EC2 instance:
    
3. ```bash
     chmod 443 "path_to_your_pem_file.pem"
    ```
    
    ```bash
    ssh -i "path_to_your_pem_file.pem" ubuntu@your_ec2_public_ip
    ```
    
    Replace `path_to_your_pem_file.pem` with the location of your `.pem` file and `your_ec2_public_ip` with your EC2 instance’s public IP address (available in the EC2 dashboard).
    
4. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730115841141/9c88491e-84a0-415f-a64f-4af5742f9944.png align="center")
    
    #### **Option 2: Connecting Through AWS Console**
    
    If you're unfamiliar with SSH or facing issues, you can also connect directly through the **AWS Console**:
    
    1. In your EC2 dashboard, select your instance.
        
    2. Click on the **Connect** button at the top.
        
    3. Choose **EC2 Instance Connect**, and you can connect directly from the browser without needing a private key.
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730115973312/d50dd0c3-3c4e-42d4-a838-8c1f021400f9.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730116018140/84fe3e0c-feed-4409-8d2b-9329501a161c.png align="center")
    

---

### Step 3: **Update and Install Dependencies**

Once you’re inside your EC2 instance, it’s time to install the required software.

1. **Update the system packages**:
    
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
    
2. Installing Node Using the Node Version Manager:[  
    ](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04#option-3-installing-node-using-the-node-version-manager)<mark>React apps run on Node.js, so we need to install it.</mark>
    
    ```bash
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh
    ```
    
    ```bash
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
    ```
    
    ```bash
    source ~/.bashrc
    ```
    
    ```bash
    nvm install v20.9.0
    ```
    
    You can check the versions installed with:
    
    ```bash
    node -v
    npm -v
    ```
    
3. **Install Nginx**:  
    Nginx will be used as a reverse proxy to serve your React app.
    
    ```bash
    sudo apt install nginx -y
    ```
    
4. **Start Nginx and enable it**:
    
    ```bash
    sudo systemctl start nginx
    sudo systemctl enable nginx
    ```
    
    You can check if Nginx is working by navigating to your EC2 instance’s public IP in a browser. You should see the default Nginx welcome page.
    
5. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730121330865/c44928e8-a1dd-4898-b556-d17220fe4af3.png align="center")
    

---

### Step 4: **Configure Firewall Rules**

1. **Open necessary ports**:  
    Make sure your security group allows incoming traffic on HTTP (port 80). You can modify this from the AWS EC2 console under **Security Groups**.
    
2. <div data-node-type="callout">
    <div data-node-type="callout-emoji">💡</div>
    <div data-node-type="callout-text">We have done this step while creating ec2 instance in this blog.</div>
    </div>
    

---

### Step 5: **Deploy Your React App**

Now it's time to get your React app onto the server.

1. **Clone your React app from GitHub**:
    
    First, install Git:
    
    ```bash
    sudo apt install git -y
    ```
    
    Then, clone your app:
    
    ```bash
    git clone https://github.com/your-username/your-react-app.git
    ```
    
2. **Navigate to your app directory**:
    
    ```bash
    cd your-react-app
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730117117091/2af4c9b3-f650-49e3-aaee-a9935fedd407.png align="center")
    
3. **Install the dependencies**:
    
    ```bash
    npm install
    ```
    
4. **Build your React app**:
    
    ```bash
    npm run build
    ```
    
    This command creates a production-ready version of your React app in the <mark>build/dist</mark> folder.
    
5. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730117317444/c9d6a420-6559-4504-b75d-3c901ab0a928.png align="center")
    

---

### Step 6: **Configure Nginx to Serve the React App**

1. **Remove the default Nginx configuration**:
    
    ```bash
    sudo rm /etc/nginx/nginx.conf
    ```
    
2. **Create a new Nginx configuration file** for your React app:
    
    ```bash
    sudo vi /etc/nginx/nginx.conf
    ```
    
3. **Add the following configuration**:
    
    ```nginx
    events {
        # Event directives...
    }
    
    http {
    	server {
        listen 80;
        server_name "your public ip";
    
        location / {
            proxy_pass http://localhost:{PORT LIKE 8080};
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }
    	}
    }
    ```
    
4. **Reload Nginx**
    
    ```bash
    sudo nginx -s reload
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730119836027/d0599479-4121-4766-aaa7-557817de64c6.png align="center")
    

---

## Step 7: **Start your react app**

Before starting the app we have to install some dependecies.

### 1\. **PM2 (Process Manager 2)**:

PM2 is a production-ready process manager for Node.js applications. It allows you to run, manage, and monitor Node.js processes in the background. Key features include:

* **Process management**: Easily start, stop, restart, and manage multiple Node.js applications.
    
* **Monitoring**: Provides real-time logs, metrics, and performance monitoring.
    
* **Load balancing**: Automatically balances load across multiple CPU cores for better performance.
    
* **Startup scripts**: Automatically restarts apps on crashes or server reboots.
    

**Installation**:

```bash
npm install -g pm2
```

### 2\. **Serve**:

Serve is a simple and lightweight HTTP server designed to serve static files, such as the `dist` folder generated by build tools like React. It's ideal for quickly serving a single-page application (SPA) or static content.

Key features include:

* **Easy static file hosting**: Serve static files from any folder with a single command.
    
* **Single-page application (SPA) support**: Perfect for apps like React, with fallback routing.
    
* **Lightweight and fast**: Minimal setup for quick static file hosting.
    

**Installation**:

```bash
npm install -g serve
```

These two libraries together allow you to serve static content (like a React app) and manage its processes efficiently in a production environment.

### 3 .Finally starting the react app

```bash
cd "your react app folder"
```

```bash
pm2 start "serve -s dist -l 8080" --name react-app
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730120484276/7c380ff7-db34-4a35-9243-13de0c8928d8.png align="center")

---

### Step 8: **Access Your React App**

Now, when you navigate to your EC2 instance’s public IP in a browser, you should see your React app running!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730120551319/93d74555-7ac8-47ab-9248-400ea44b2e08.png align="center")

---

### Bonus: **Add a custom domain to your app.**

1. Login to your domain name provider and add a “A” Record with your corresponding to the public ip.
    
2. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730120801530/c105577c-2f04-48eb-9eb7-0542adba96d7.png align="center")
    
    <div data-node-type="callout">
    <div data-node-type="callout-emoji">💡</div>
    <div data-node-type="callout-text">In my case, I am using Cloudflare for managing DNS, so I don't need to add an SSL certificate. However, if you're not using Cloudflare, please secure your app by adding an SSL certificate through Let's Encrypt or AWS Certificate Manager.</div>
    </div>
    
3. Update your Nginx configuration (Optional)
    
    ```bash
    events {
        # Event directives...
    }
    
    http {
    	server {
        listen 80;
        server_name "your domain name";
    
        location / {
            proxy_pass http://localhost:{PORT LIKE 8080};
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;
        }
    	}
    }
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730121207541/99d1f327-6bdc-4064-aa7f-805a7c2d1403.png align="center")
    

---

### Conclusion

That’s it! 🎉 You’ve successfully hosted your React app on an EC2 instance. By following these steps, you’ve learned how to:

* Set up an EC2 instance.
    
* Install necessary dependencies (Node.js, npm, Nginx).
    
* Deploy a React app using Nginx as a reverse proxy.
    
* Optionally, add a custom domain to your app.