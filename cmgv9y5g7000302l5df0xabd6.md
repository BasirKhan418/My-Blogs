---
title: "Valkey Installation on ubuntu"
datePublished: Fri Oct 17 2025 20:01:28 GMT+0000 (Coordinated Universal Time)
cuid: cmgv9y5g7000302l5df0xabd6
slug: valkey-installation-on-ubuntu

---

## Install Docker

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Check docker installation status

```bash
sudo systemctl status docker
```

## Set the conf  

```bash
sudo rm -rf /data/valkey/valkey.conf
sudo nano /data/valkey/valkey.conf
```

### Paste the conf make sure to edit the password

```bash
bind 0.0.0.0
port 6379
protected-mode yes

requirepass StrongP@ssw0rd!

save 60 1
appendonly yes
appendfilename "appendonly.aof"
appendfsync everysec

dir /data

logfile /data/valkey.log
loglevel notice

maxmemory 1gb
maxmemory-policy allkeys-lru
```

Check is it good or not  

```bash
ls -l /data/valkey/valkey.conf
```

```bash
-rw-r--r-- 1 ubuntu ubuntu 350 Oct 18 16:10 /data/valkey/valkey.conf
```

## Run the container  

```bash
sudo docker run -d \
  --name valkey \
  -p 6379:6379 \
  -v /data/valkey:/data \
  -v /data/valkey/valkey.conf:/etc/valkey/valkey.conf:ro \
  --restart unless-stopped \
  valkey/valkey:latest \
  valkey-server /etc/valkey/valkey.conf
```

Check all good

```bash
sudo tail -f /data/valkey/valkey.log
```

Check  
Ready to accept connections  
this message should come up

## conf this if you think that kernel should allocate as much they can  
**1️⃣** `maxmemory = 1 GB`

* This is a **hard cap** inside ValKey.
    
* Redis **will never store more than 1 GB of data in memory**, even if your machine has 4 GB free.
    
* Eviction (`allkeys-lru`) will kick in once that limit is reached.
    
* **Effect:** Redis memory usage is limited to 1 GB for keys/data.
    

---

### **2️⃣** `vm.overcommit_memory=1`

* This is **a Linux-level setting**, not a ValKey setting.
    
* Linux allows Redis to request memory **without being blocked**, even if the kernel thinks the system might run out of RAM.
    
* **Important:** This does **not change the** `maxmemory` limit inside Redis.
    

So in your case:

* ValKey will **still only store 1 GB** of data (because of `maxmemory=1gb`).
    
* Linux may allocate slightly more than 1 GB to Redis internally for **overhead, buffers, bookkeeping**, without killing the process.
    
* Redis **cannot “go beyond 1 GB for data”** just because `overcommit=1` is set.  
    

```bash
# Temporary (until next reboot)
sudo sysctl vm.overcommit_memory=1

# Permanent
echo "vm.overcommit_memory=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Here is your password  
make sure to allow 6379 on security group as in bound rule  

```bash
redis://default:<pass>@<EC2_PRIVATE_OR_PUBLIC_IP>:6379
```

## Test command in cli  

```bash
docker exec -it valkey valkey-cli
> auth StrongP@ssw0rd!
OK
> set test "persistent"
OK
> get test
"persistent"
```

## Node js Client sample code  

```bash
// index.js
import { createClient } from 'redis';

async function main() {
  // Replace <EC2_IP> with your EC2 public/private IP
  const client = createClient({
    url: 'redis://default:StrongP@ssw0rd!@<EC2_IP>:6379'
  });

  client.on('error', (err) => console.error('Redis Client Error', err));

  try {
    await client.connect();
    console.log('✅ Connected to ValKey successfully!');

    // Set a key with expiry of 60 seconds
    await client.set('test-key', 'Hello ValKey!', { EX: 60 });
    console.log('✅ Key "test-key" set with 60 seconds expiry');

    // Get the key
    const value = await client.get('test-key');
    console.log('🔹 Retrieved value:', value);

  } catch (err) {
    console.error('Connection failed:', err);
  } finally {
    await client.quit();
    console.log('Connection closed');
  }
}

main();
```

```bash
npm install redis
node index.js
```

```bash
✅ Connected to ValKey successfully!
✅ Key "test-key" set with 60 seconds expiry
🔹 Retrieved value: Hello ValKey!
Connection closed
```

### 🔹 Bottom line

Your current setup is **okay for small production workloads** or a single-app environment:

* One EC2 instance
    
* Domain-based access
    
* Persistent storage via EBS
    
* Password protection
    

…but for **enterprise-level production** with high availability, failover, and monitoring, you’ll need to implement:

* Clustered ValKey / Redis replication
    
* TLS/SSL connections
    
* Monitoring and alerting
    
* Automated backups