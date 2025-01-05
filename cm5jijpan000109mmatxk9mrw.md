---
title: "My Own Dns Server"
seoTitle: "My Your Own DNS Server | Build and Deploy Step-by-Step Guide"
seoDescription: ""Learn how to build and deploy your own DNS server with our comprehensive step-by-step guide. Optimize domain name resolution, improve network efficiency, a"
datePublished: Sun Jan 05 2025 11:12:11 GMT+0000 (Coordinated Universal Time)
cuid: cm5jijpan000109mmatxk9mrw
slug: my-own-dns-server
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1736075335457/f28e324b-4965-4dcb-91fe-22914e3bf40e.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1736075389871/5e11a050-54a7-4563-8f2f-0c0522cbc853.webp
tags: dns, ec2, aws, devops

---

## How to use ?  

For calculation:-

```bash
dig @dns.deploylite.tech MX calculate.2+2
#feel free to change 2+2 to something else 
dig @dns.deploylite.tech MX calculate.2+2 +short
```

For generating random number:-

```bash
dig @dns.deploylite.tech MX generate-random.rand
#using +short
dig @dns.deploylite.tech MX generate-random.rand +short
```

For getting any timezone:-  

```bash
dig @dns.deploylite.tech MX timezone.ASIA/KOLKATA
#using +short
dig @dns.deploylite.tech MX timezone.ASIA/KOLKATA +short
dig @dns.deploylite.tech MX timezone.US/Pacific +short
dig @dns.deploylite.tech MX timezone.Singapore +short
#you can use any time zone for the query?
#for supported timezone visist:-https://timeapi.io/api/timezone/availabletimezones
```

For ai response . Ask anything to ai:-  

```bash
dig @dns.deploylite.tech MX ai.what.is.dns +short
#Modify this query . And if you have space on your prompt add .
dig @dns.deploylite.tech MX ai.what.is.js +short
```

For Piyush Sir's Playlist recommendation: Ask your query about the resources you need for a tutorial, and you'll get a full-fledged playlist curated by Piyush Sir:-

```bash
dig @dns.deploylite.tech MX tutorial.nextjs +short
dig @dns.deploylite.tech MX tutorial.advanced.js +short
#feel free to ask what do you want.
dig @dns.deploylite.tech MX tutorial.appwrite +short
#gives and not found message if it's not there
```

For Piyush Sir's Course recommendation: Ask your query:-

```bash
dig @dns.deploylite.tech MX course.docker +short
dig @dns.deploylite.tech MX course.nextjs +short
#feel free to ask of your choice
dig @dns.deploylite.tech MX course.web.dev.cohort +short
```

---

Currently, these features are available, with more exciting features coming soon.

Here is a step by step guide how to deploy your own dns server .

## How to Deploy ?

1. Start an ec2 instance and below run the commands to cotinue
    

allow 53 port in security group - &gt; custom udp → 53 → allow access from anywhere

```bash
sudo apt update && upgrade -y
```

Install nodejs

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
source ~/.bashrc
nvm install v21.7.0
```

Clone Your repo

```bash
git clone repo_url
cd repo_folder
npm i
```

Basic Configuration for opening port 53. I see that systemd-resolved is using port 53. Let's disable it and free up the port:

1. First, stop and disable systemd-resolved:
    

```bash
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
```

You might need to update your DNS resolver settings. Edit `/etc/resolv.conf`:

```bash
sudo nano /etc/resolv.conf
```

Replace its contents with:

```bash
nameserver 8.8.8.8  # Google DNS
nameserver 8.8.4.4  # Google DNS backup
```

To prevent systemd-resolved from starting again on reboot:

```bash
sudo systemctl mask systemd-resolved
```

If you get permission errors, make sure you've set the capabilities correctly:

```bash
sudo setcap cap_net_bind_service=+ep $(which node)
```

Start the dns server on background. Install Pm2.

```bash
npm i -g pm2
```

```bash
pm2 start "node index.js" --name dns
```

```bash
pm2 save
```

**Congratulations Your Server is up and running.**

---