---
title: "How to Host a React Website on AWS S3 and Speed it Up with CloudFront"
seoTitle: "How to Host a React Website on AWS S3 with CloudFront for Fast, Global"
seoDescription: "earn how to deploy your React app on AWS S3 and optimize performance with CloudFront. Follow best practices for secure, low-latency, and globally cached hos"
datePublished: Sat Nov 02 2024 22:10:46 GMT+0000 (Coordinated Universal Time)
cuid: cm30pw4yp000009ma2v4dcko7
slug: how-to-host-a-react-website-on-aws-s3-and-speed-it-up-with-cloudfront
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1730583396160/0d9a4679-9b49-43ac-818d-f9d806011fae.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1730585399527/b3ea2adb-57f0-4154-a799-2fd405f01c47.webp
tags: hosting, aws, cloudfront, cloud-computing, reactjs, s3

---

In this guide, we'll explore how to host your React application on Amazon S3 and use CloudFront to speed up content delivery to users worldwide. With S3 and CloudFront, you can create a highly available, low-latency website that’s easy to set up and scale.

We’ll also follow production best practices to ensure your app runs securely, efficiently, and delivers optimal performance.

---

### Why Choose S3 and CloudFront for Hosting?

**Amazon S3** is an affordable, durable, and secure solution for static website hosting. It’s perfect for hosting files like HTML, CSS, JavaScript, images, and other assets.  
**Amazon CloudFront** is a Content Delivery Network (CDN) that caches your website’s content in multiple geographic locations, reducing loading times for users around the world and minimizing load on your S3 bucket.

With these services together, you can host your site with:

* **Lower Latency**: CloudFront’s edge locations around the globe speed up content delivery.
    
* **Improved Performance**: Caching and compression enhance performance.
    
* **Better Security**: Protect your S3 bucket by serving content only through CloudFront.
    

---

## Step-by-Step Guide to Host a React Website on AWS S3 and CloudFront

### Step 1: Build Your React App for Production

1. **Initialize and Build the React App**
    
    * If you haven’t created your React app yet, start with:
        
        ```bash
        npx create-react-app my-app
        ```
        
        Replace `my-app` with your project name.
        
    * Next, navigate to your app folder:
        
        ```bash
        cd my-app
        ```
        
    * Build your app for production:
        
        ```bash
        npm run build
        ```
        
        This generates a `build` folder with optimized files ready for deployment.
        
2. **Verify Your Build**
    
    * Check the `build` folder. It should contain your production-ready HTML, CSS, JavaScript, and other assets.
        

### Step 1.1 : Build Your React App for Production

1. **Clone your react app from github**
    
    * If you donot have an react app please clone it through this url:
        
        ```bash
        git clone https://github.com/BasirKhan418/React-bolierplate-code.git
        ```
        
    * Next, navigate to your app folder:
        
        ```bash
        cd React-bolierplate-code
        ```
        
    * Install Dependencies & Build your app for production:
        
        ```bash
        npm i 
        npm run build 
        ```
        
        This generates a `build` folder with optimized files ready for deployment.
        
2. **Verify Your Build**
    
    * Check the `build` or dist folder. It should contain your production-ready HTML, CSS, JavaScript, and other assets.
        

---

### Step 2: Set Up an S3 Bucket for Static Website Hosting

1. **Create an S3 Bucket for Your Website**
    
    * Open the [S3 Console](https://s3.console.aws.amazon.com/s3/).
        
    * Click **Create Bucket**.
        
    * Enter a unique bucket name and choose a region.
        
    * **Important**: Uncheck **Block all public access** if you want to make the website accessible to everyone.
        
    * ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730584147395/39740234-5d39-45c4-a481-3324321e0416.png align="center")
        
2. **Upload Files to S3**
    
    * In the **Objects** tab, click **Upload** and select all files from your `build` /dist folder.
        
    * ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730584467840/23f78238-8129-4676-85d9-94ba8c7c134e.png align="center")
        
3. **Configure Public Access**
    
    * Go to the **Permissions** tab.
        
    * Under **Bucket Policy**, Click on edit bucket policy and paste the content below of it and make sure to add your s3 arn.
        
        ```json
        {
        	"Version": "2012-10-17",
        	"Statement": [
        		{
        			"Sid": "Statement1",
        			"Principal": "*",
        			"Effect": "Allow",
        			"Action": [
        				"s3:GetObject"
        			],
        			"Resource": "your s3 arn/*"
        		}
        	]
        }
        ```
        
4. **Enable Static Website Hosting**
    
    * Go to **Properties** &gt; **Static website hosting** &gt; **Edit**.
        
    * Enable **Static Website Hosting**.
        
    * For **Index Document**, enter `index.html`.
        
    * For **Error Document**, also enter `index.html` (useful for single-page apps to handle routes).
        
    * Save your settings.
        
    * ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730584849745/787044a3-b326-49aa-82e8-0a976cbf3234.png align="center")
        
    * ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730584767163/2ea25ad5-a11e-4fc0-a95d-3f0ff7b9921d.png align="center")
        
5. **Test Your S3 Website**
    
    * After setting up, open the **Bucket website endpoint** to check if your React app is accessible.
        
    * ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730584799998/3b5a1fc7-6fc4-466e-865b-b536d8278581.png align="center")
        

---

### Step 3: Set Up CloudFront for Global Caching and Lower Latency

1. **Create a CloudFront Distribution**
    
    * Go to the [CloudFront Console](https://console.aws.amazon.com/cloudfront/).
        
    * Click **Create Distribution** &gt; **Web**.
        
2. **Configure the Origin**
    
    * Under **Origin Domain Name**, select your S3 bucket.
        
    * Set **Viewer Protocol Policy** to **Redirect HTTP to HTTPS** for security.
        
    * Configure **Allowed HTTP Methods** to **GET, HEAD** only if you don’t need POST requests.
        
    * For **Origin Shield**, enable **Use Origin Shield** to add an extra caching layer.
        
3. **Set Cache Behavior Settings**
    
    * In **Cache Behavior Settings**, enable **Compress Objects Automatically** to reduce file sizes.
        
    * Choose **Cache Based on Selected Request Headers** as **None** for a basic cache, or **Whitelist** certain headers if you have dynamic content.
        
4. **Configure Custom Error Pages for Single-Page App (SPA) Routing**
    
    * In the **Error Pages** section, configure a 404 error to redirect to your `index.html` and set the response code to `200`. This ensures that client-side routes are handled correctly by serving the main app shell.
        
5. **Restrict S3 Bucket Access to CloudFront Only**
    
    * To secure your app, configure **Origin Access Control (OAC)** on CloudFront so only CloudFront can access S3.
        
    * In the **Permissions** tab of your S3 bucket, update the bucket policy to allow CloudFront access only.
        
6. **Create the Distribution**
    
    * Once configured, click **Create Distribution**. It may take a few minutes to deploy.
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730585082529/1348703c-8261-4b41-b032-f2ef50e9de4a.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730585149261/f500ecce-4aab-470c-91ef-7febd177186c.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1730585301991/ad4707b0-cdd1-44b7-8aa5-051020d2542d.png align="center")
    

---

### Step 4: Set Up a Custom Domain with CloudFront (Optional)

To add a custom domain:

1. **Add Alternate Domain Names**
    
    * In **Alternate Domain Names (CNAMEs)**, add your custom domain (e.g., [`www.yourdomain.com`](http://www.yourdomain.com)).
        
2. **Configure SSL with ACM**
    
    * Go to [AWS Certificate Manager (ACM)](https://console.aws.amazon.com/acm/), request a certificate for your domain, and validate ownership.
        
    * Associate this certificate with your CloudFront distribution.
        
3. **Update DNS Records**
    
    * Use Route 53 or your DNS provider to create a **CNAME** record pointing to your CloudFront distribution URL.
        

---

### Production Best Practices for Hosting on S3 and CloudFront

Here are some best practices to optimize performance, security, and scalability:

1. **Enable Compression in CloudFront**
    
    * Enable **Compress Objects Automatically** in your CloudFront distribution to reduce file sizes and improve load times.
        
2. **Use Cache-Control Headers**
    
    * Set **Cache-Control** headers for static assets in S3 to control how long content is cached. For example, you can use `max-age=31536000` to cache files for a year. Remember to update the file name for any new versions to bypass the cache when needed.
        
3. **Enable Logging**
    
    * Enable CloudFront **Standard Logging** to monitor performance and access patterns.
        
    * Enable S3 **Access Logs** to review bucket access, which is helpful for troubleshooting and analytics.
        
4. **Invalidate Cache on Updates**
    
    * When updating your website, invalidate the CloudFront cache to ensure that users get the latest version. You can do this by creating an **Invalidation** request in the CloudFront console for files you’ve updated (e.g., `/*`).
        
5. **Consider Origin Shield for Heavily Accessed Content**
    
    * If you anticipate heavy traffic, enable **Origin Shield** in CloudFront for extra caching between CloudFront and your S3 bucket, which helps reduce origin fetches and can improve cache hit ratio.
        

---

### Conclusion

Hosting your React website on AWS with S3 and CloudFront is an affordable and scalable solution that boosts your app’s performance globally. Following the above steps and best practices, you’ll achieve a secure, low-latency, and highly available website setup that’s optimized for users worldwide.

By using CloudFront’s caching, compression, and Origin Shield options, along with carefully managed Cache-Control headers, you’ll ensure that your app is fast and responsive.

#### SEO Keywords:

* Host React on AWS S3
    
* Set up CloudFront for React website
    
* Low-latency website hosting
    
* S3 CloudFront integration tutorial
    
* AWS React app production setup
    

---

With these steps, you’re ready to deliver your React website through AWS’s powerful S3 and CloudFront services. Happy hosting!