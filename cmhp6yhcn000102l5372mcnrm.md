---
title: "What the heck is Cron?"
seoTitle: "What the Heck is Cron? Master Cron Jobs Like a Pro (with Fun Examples)"
seoDescription: "Cron made fun! 💻 Basir breaks down how those timely emails and jobs run automatically - simple, real, and hilarious!"
datePublished: Fri Nov 07 2025 18:30:50 GMT+0000 (Coordinated Universal Time)
cuid: cmhp6yhcn000102l5372mcnrm
slug: what-the-heck-is-cron
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/sJHO8cJcgZc/upload/f77ac1f569ff66a2f204264afbcddd0c.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1762540082275/e26ff33c-341e-45ab-88b2-9176b6504e2f.jpeg

---

Have you ever noticed promotional emails, marketing campaigns, or reminder messages landing in your inbox at the same time every day or week? If you’re a curious tech person like me, you might wonder what’s running behind the scenes. Is it a person hitting “send”? A magic button?  
Meet **cron** the (not scary) job scheduler.

**What is cron?**  
`cron` is a Unix/Linux utility that schedules and runs tasks automatically at fixed times, dates, or intervals. A “cron job” is simply a command or script that cron runs on a schedule you define. People use cron for all kinds of repetitive automation: sending emails, generating reports, cleaning up temporary files, running backups, triggering data pipelines, and more.

**Why developers love cron (and its modern cousins)**

* It’s reliable: once scheduled, cron will run tasks at the right time without human intervention.
    
* It’s simple: a single text file (the crontab) can express complex schedules.
    
* It scales to many use cases: from simple daily cleanups to orchestrating big data workflows.  
    That said, for complex workflows and dependencies, teams often use more advanced schedulers (e.g., Apache Airflow) or custom distributed schedulers. But cron remains the simplest, battle-tested tool for time-based automation.
    

**Real-world uses (quick picture)**

* Sending promotional or reminder emails at scheduled times.
    
* Running ETL and analytics jobs nightly.
    
* Cleaning logs or rotating backups.
    
* Triggering batch jobs, like invoice generation or report exports.
    

## “The Starry Secret of Cron Jobs!”

Ever seen something like `* * * * *` in a cron job and thought — “what the heck are all these stars doing here?” 😅  
Well, each `*` (asterisk) represents a **time field** , **minute, hour, day of month, month, and day of week** — in that exact order. So when you see `* * * * *`, it literally means **“run every minute of every hour of every day of every month, forever.”**

In simple words, it’s like telling your server:  
💬 *“Bro, no breaks. Work every single minute — 24x7!”* 😆  
You don’t get it naaa 😆 , I know here is the image for better understanding ?

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762537570579/c2b8349c-1c3c-4202-90fc-7a9c31ec09ac.png align="center")

  
  
🧠 Let’s Be Serious Now - Time to Practice Some Cron Magic

Alright guys, enough of the theory talk! Let’s get our hands dirty and see how those little stars actually work. If you want to really **grasp cron**, you’ve got to *practice* these patterns instead of just reading them. So here are a few simple, real-life examples that’ll help you master cron expressions like a pro

#### 🕒 1. Every day at 3 PM

```bash
0 15 * * *
```

**Meaning:**  
The first `0` means **start at the 0th minute**, the `15` means **the 15th hour (3 PM)**, and the rest of the stars mean **every day, every month, every weekday**.  
So this runs exactly once a day at **3:00 PM**.

#### 🕐 2. Every day at 1 :30PM

```bash
30 13 * * *
```

**Meaning:**  
Run the task when the **hour is 13 (1 PM)** and **minute is 30** simple as that! Cron uses **24-hour format**, so 13 stands for 1 PM.

#### 📅 3. Every week on Monday

```bash
0 9 * * 1
```

**Meaning:**  
This runs at **9:00 AM every Monday**. The `1` at the end represents **Monday** (because cron counts days of the week as 0–6, where 0 = Sunday).

#### 🗓️ 4. Every month on the 1st

```bash
0 10 1 * *
```

**Meaning:**  
The `1` in the third position means **the 1st day of every month**, and `10` means **10 AM**.  
So this runs once a month on the 1st day, at **10:00 AM** sharp.

---

💡 *Tip:* Always remember the order **minute, hour, day of month, month, day of week.** Once you get that in your head, cron expressions will start making perfect sense.

## Let’s Get Serious Guys - Time to Play with Cron Like a Real One

Alright legends enough of the talking, it’s time to touch some code. You’ve read the stars, now let’s *make them shine.*

We’re going to make cron print the current date every minute (so you can flex that “automation” muscle). After that, we’ll move to something more *Basir-level* automatic database backups every day at 2 PM. Yeah, prod stuff baby 😎

---

### 🧠 Step 1: Get into the Machine

If you’re already on **Ubuntu**, cool. If not, spin one up anywhere - Docker, AWS, whatever you like.  
I don’t care how, just get inside a terminal. You can’t learn cron by just reading this isn’t a fairytale, it’s tech.

Check if cron is alive:

```bash
sudo systemctl status cron
```

If it’s not active, just hit:

```bash
sudo systemctl start cron
```

Cron is like your background butler. He doesn’t speak, but he gets the job done quietly every time. 🕶️

---

### 🕒 Step 2: Let’s Make Cron Talk — Print Date Every Minute

Now fire this command:

```bash
crontab -e
```

Inside that file, drop this line:

```bash
* * * * * date >> ~/dates.txt
```

That’s it. Every single minute, this command will run `date` and append the output to `dates.txt`.  
After 2–3 minutes, check it:

```bash
cat ~/dates.txt
```

If you see multiple timestamps there - congrats, you just made cron do your bidding. 😎  
Every minute, like clockwork.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762539509650/3549ba21-b529-4df3-b726-29fb9c89d24c.png align="center")

---

### 🧍‍♂️ A Little Self-Love Moment

You’re on **Basir’s Blog**, my friend.  
And Basir doesn’t just *read* commands - he *runs* them, breaks them, fixes them, and runs them again till it’s perfect. 😏  
I’m a big believer in *doing prod things*, even when it’s just a demo. Because that’s how you become dangerous (in a good way).

---

## 💾 Let’s Do Some “Prod Stuff” — Database Backup Every Day

Now we’re talking. Real-world example.  
Let’s make cron handle database backups - because no one wants to be that engineer who forgot to back up.

---

### 🐳 Step 1: Spin Up MySQL in Docker

Run this command like a boss:

```bash
docker run -d \
  --name mysql-demo \
  -e MYSQL_ROOT_PASSWORD=rootpass \
  -e MYSQL_DATABASE=mydb \
  -p 3306:3306 \
  mysql:8
```

Quick breakdown:

* `-d` → runs in background
    
* `--name` → gives your container a cool name
    
* `-e` → sets env variables (root password, db name)
    
* `-p` → connects MySQL’s port to your machine
    

Check if it’s alive:

```bash
docker ps
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762539659346/55d05b35-729f-4d9b-963c-c6760abed9f2.png align="center")

---

### 🍽️ Step 2: Add Some Data

We need data to back up, right? Let’s cook some up 🍳

Create a table:

```bash
docker exec -it mysql-demo mysql -uroot -prootpass -e \
"CREATE TABLE mydb.users (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(50));"
```

Insert fake data:

```bash
docker exec -i mysql-demo mysql -uroot -prootpass -D mydb -e "
INSERT INTO users (name) VALUES
('Alice'), ('Bob'), ('Charlie'), ('Diana'), ('Ethan');
"
```

Verify it:

```bash
docker exec -it mysql-demo mysql -uroot -prootpass -D mydb -e "SELECT * FROM users;"
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762539737926/f817cd57-790f-428b-b000-ab8d07de138f.png align="center")

---

### 🧠 Step 3: Manual Backup — Just to Feel It

Create a backup folder first:

```bash
sudo mkdir -p /backups
sudo chmod 777 /backups
```

Now dump the database:

```bash
docker exec mysql-demo mysqldump -uroot -prootpass mydb > /backups/mysql_backup_$(date +%F).sql
```

Boom 💥 You just created your first SQL backup with today’s date in the filename.

---

### 🕑 Step 4: Automate Like a Boss

Alright, let’s make cron work for us again - this time, every night at **2 AM**.  
Because while we sleep, cron grinds. 😴

Open crontab again:

```bash
crontab -e
```

Add this line:

```bash
0 2 * * * docker exec mysql-demo mysqldump -uroot -prootpass mydb > /backups/mysql_backup_$(date +\%F).sql
```

💡 Notice the `\%F` — the backslash tells cron, “Yo, don’t get confused, that’s just my date.”

If you’re too impatient to wait till 2 AM (like me waiting for a girl’s reply 💔), change it temporarily to:

```bash
*/2 * * * * sudo docker exec mysql-demo mysqldump -uroot -prootpass mydb > /backups/mysql_backup_$(date +\%F).sql
```

That’ll run every 2 minutes. Instant results, instant dopamine 😏

---

### 🧹 Step 5: Clean Up — Delete Old Backups

Because no one wants a folder heavier than their ex’s emotional baggage 😬

Add this line to remove backups older than 7 days:

```bash
0 3 * * * find /backups -name "mysql_backup_*.sql" -mtime +7 -delete
```

Boom. Fresh backups only. Every day. Clean and classy.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1762539977894/af2e17fe-897c-4e8a-89b9-0afcb4108a56.png align="center")

---

### 🧩 Example Folder

```bash
/backups/
 ├── mysql_backup_2025-11-05.sql
 ├── mysql_backup_2025-11-06.sql
 └── mysql_backup_2025-11-07.sql
```

---

### 💣 Bonus Trick — Compressed Backups

Want to save space and look cool doing it? Use gzip:

```bash
0 2 * * * docker exec mysql-demo mysqldump -uroot -prootpass mydb | gzip > /backups/mysql_backup_$(date +\%F).sql.gz
```

Now your backup’s smaller and faster like a code snippet that just got optimized.

  
Wait, Basir this is too raw, man! Where’s my favorite backend language, my tool, my code? Ohh baby, hold on I’ve got to bring everyone along on this ride. I don’t fall for syntactical sugar; I love those raw commands that make you feel like a real dev at 2 AM 😎. And guess what? Most of your favorite backend frameworks secretly do the same thing under the hood. You can use cron in any language you love trust me, you’re just **one search away** from making it happen!

---

### ⚡ Final Thought

Cron is that quiet friend who never asks for credit but makes sure everything runs on time.  
You sleep. Cron works. You forget. Cron remembers.

And that’s why I love it. It’s not just automation - it’s discipline in code form.  
So go ahead - set it, forget it, and let cron handle your boring stuff while you build something epic.

Because remember… **Basir doesn’t wait for miracles - he schedules them.** 💥