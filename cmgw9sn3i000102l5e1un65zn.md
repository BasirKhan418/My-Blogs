---
title: "Setting up CI/CD Through Github Actions"
seoTitle: "Setting up CI/CD Through Github"
seoDescription: "Setting up CI/CD Through Github"
datePublished: Sat Oct 18 2025 12:44:57 GMT+0000 (Coordinated Universal Time)
cuid: cmgw9sn3i000102l5e1un65zn
slug: setting-up-cicd-through-github-actions
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1762587435744/01125b42-26d5-4d91-b389-b303dd325710.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1762587448982/43f9e1f4-51b1-4dd8-97d8-2dc86b6c791b.webp

---

# **Step 1 — Generate a CI/CD-friendly SSH key on your EC2**

Open your EC2 terminal and run:

```bash
# Generate a new SSH key specifically for GitHub Actions
ssh-keygen -t rsa -b 4096 -m PEM -C "github-actions" -f ~/github_ec2_ci_key -N ""
```

Explanation:

* `-t rsa` → RSA key type
    
* `-b 4096` → 4096-bit for strong encryption
    
* `-m PEM` → ensures compatibility (some tools like GitHub Actions need PEM format)
    
* `-C "github-actions"` → a comment for identification
    
* `-f ~/github_ec2_ci_key` → saves key in your home directory
    
* `-N ""` → **no passphrase** (required for CI/CD)
    

This will create **two files**:

```bash
~/github_ec2_ci_key      → private key
~/github_ec2_ci_key.pub  → public key
```

---

# **Step 2 — Add the public key to authorized\_keys**

```bash
# Ensure the .ssh directory exists
mkdir -p ~/.ssh

# Append the public key to authorized_keys
cat ~/github_ec2_ci_key.pub >> ~/.ssh/authorized_keys

# Set proper permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

✅ Now your EC2 **trusts this key** for SSH login.

---

# **Step 3 — Test SSH locally**

```bash
ssh -i ~/github_ec2_ci_key ubuntu@<EC2_PUBLIC_IP>
```

* You should log in **without typing a passphrase**.
    
* If it works, your key setup is correct.
    

---

# **Step 4 — Copy private key to GitHub Secrets**

1. Run:
    

```bash
cat ~/github_ec2_ci_key
```

2. Copy everything starting from:
    

```bash
-----BEGIN RSA PRIVATE KEY-----
```

…to:

```bash
-----END RSA PRIVATE KEY-----
```

3. Go to GitHub → **Settings → Secrets → Actions → New repository secret**
    
4. Name it: `EC2_KEY`
    
5. Paste the private key there
    

> **No passphrase needed**, so you don’t need a `passphrase:` field in GitHub Actions.

---

# **Step 5 — Create GitHub Actions workflow**

Create `.github/workflows/deploy.yml` in your repository:

```bash
name: Deploy to EC2

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Deploy to EC2
        uses: appleboy/ssh-action@v0.1.9
        with:
          host: ${{ secrets.EC2_HOST }}        # Your EC2 public IP
          username: ${{ secrets.EC2_USER }}    # Typically 'ubuntu'
          key: ${{ secrets.EC2_KEY }}          # Private key secret
          port: 22
          script: |
            cd /var/www/artistic_backend
            git pull origin main
            npm install
            npm run build
            pm2 restart all
```

## OR

```bash
name: Deploy to EC2

on:
  push:
    branches:
      - master

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Deploy to EC2
        uses: appleboy/ssh-action@v0.1.9
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USER }}
          key: ${{ secrets.EC2_KEY }}
          port: 22
          script: |
            # Load Node.js environment (handles nvm or global install)
            if [ -f ~/.nvm/nvm.sh ]; then
              echo "Using nvm environment"
              source ~/.nvm/nvm.sh
              nvm use node
            else
              echo "Using system-wide Node.js"
              export PATH=$PATH:/usr/local/bin:/usr/bin
            fi

            cd /home/ubuntu/quick-test/backend
            echo "Pulling latest code..."
            git pull origin master

            echo "Installing dependencies..."
            npm install

            echo "Building TypeScript..."
            npx tsc -b

            echo "Restarting PM2 process..."
            pm2 restart all || pm2 start ecosystem.config.js --update-env
```

---

# **Step 6 — Secrets you need in GitHub**

* `EC2_HOST` → your EC2 public IP
    
* `EC2_USER` → usually `ubuntu`
    
* `EC2_KEY` → the private key you copied
    

No passphrase is required because the key is unencrypted.

---

# ✅ **Optional: Remove passphrase from an existing key**

If you already generated a key **with a passphrase**, you can remove it:

```bash
ssh-keygen -p -f ~/existing_key
# Enter current passphrase
# For new passphrase: press ENTER
# Confirm: press ENTER
```

---

# **Step 7 — Verify workflow**

1. Push to the `main` branch.
    
2. GitHub Actions will:
    

* Checkout the code
    
* SSH into your EC2
    
* Pull latest code, install dependencies, build, and restart PM2
    

No more `ssh: handshake failed` errors because:

* The key is trusted by EC2
    
* The private key is unencrypted
    
* GitHub Actions can use it in Docker without needing a passphrase