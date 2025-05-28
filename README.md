# 💀 DNS Spoofing Simulation with Ettercap in a Controlled Lab (Kali vs Kali)

> **⚠️ Educational Purpose Only**  
> This project demonstrates a DNS spoofing attack in a **completely isolated lab environment** using **Ettercap** and a fake Facebook page. Do NOT replicate this outside of a controlled setting.

---

## 🧠 1. Machine Configuration

| Role       | OS        | IP Address      | Description                                |
|------------|-----------|------------------|--------------------------------------------|
| 🛡️ Victim   | Kali Linux | `192.168.219.135` | Regular Kali VM with default network config |
| 🎯 Attacker | Kali Linux | `192.168.219.134` | Runs Ettercap and hosts the fake site       |

> Both machines are connected via **VMware Host-Only Adapter** and can ping each other.

---

## ⚙️ 2. Attack Methodology: DNS Spoof via Ettercap

### 🧩 Tools Used
- `Ettercap`: To perform DNS spoofing in real time
- `Apache2`: To serve the fake Facebook page
- `HTML/CSS`: Custom phishing-style page

---

### 🔥 Attack Flow

1. **Victim browses `www.facebook.com`** as usual.
2. **Ettercap intercepts the DNS request** on the attacker machine.
3. The attacker **spoofs the DNS reply**, telling the victim that `www.facebook.com` = `192.168.219.134`.
4. Victim gets redirected to a **fake Facebook site** that says:
   > **"You Are Doomed!"**

---

## 🚀 3. Execution Steps (Attacker Side)

### 📁 Step 1: Set Up the Fake Facebook Page

Create the page content:

```bash
sudo mkdir -p /var/www/html/facebook
sudo nano /var/www/html/facebook/index.html
```
Paste the following code:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Facebook - You Are Doomed</title>
  <style>
    body {
      background-color: #3b5998;
      color: white;
      font-family: Arial, sans-serif;
      text-align: center;
      padding-top: 100px;
    }
    h1 {
      font-size: 60px;
      text-shadow: 2px 2px #000;
    }
  </style>
</head>
<body>
  <h1>YOU ARE DOOMED!</h1>
  <p>This is a fake Facebook page.</p>
</body>
</html>
```
Start Apache:
```bash
sudo systemctl start apache2
```
