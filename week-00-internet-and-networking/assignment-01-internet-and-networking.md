# Week 00 - Internet and Networking

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

# 🧑‍💻 Task 1: Using ChatGPT as Your Learning Assistant

## Scenario

You're new to DevOps and will frequently encounter technical questions. ChatGPT can be your learning companion.

## Your Task

Write a clear ChatGPT prompt to help you understand:

> "What is a protocol in networking? Explain with a simple real-life example."

Take a screenshot of your interaction showing:

* Your detailed prompt (with clear expectations)
* ChatGPT's simplified response with an example

## Screenshot

Save your screenshot in the `screenshots` folder and update the file name below.

![alt text](<screenshot of your interaction about protocol.png>)


Replace `task-1-chatgpt.png` with your actual screenshot file name.

---

## What I Learned (2–3 lines)

A networking protocol is a shared set of rules that tells devices how to communicate so they can understand each other and exchange information successfully.


# 🌐 Task 2: Internet and Networking

## Scenario

Your friend is launching an online bookstore named **EpicReads**.

He asked you to explain how users globally can access his website hosted in Finland.

## Your Task

Write a short explanation (**100–150 words**) that includes:

* Packet Switching
* IP Address
* TCP/IP
* HTTP/HTTPS

💡 **Tip:** You may use ChatGPT (as demonstrated in Task 1) to refine your explanation.

## Answer

EpicReads is hosted on a server in Finland, but users anywhere in the world can access it through the internet. When a user enters the website address, the IP address identifies the server where EpicReads is hosted. The user's request is broken into small pieces of data called packets, which travel across different networks using packet switching. Each packet can take the best available route before reaching the server in Finland.

TCP/IP provides the basic rules for delivering these packets reliably between the user's device and the EpicReads server. Once the request reaches the server, HTTP or HTTPS is used to request and receive the website's pages and information. HTTPS is the secure version and protects the communication from being easily read by others.

In short, TCP/IP moves the data, packet switching helps it travel efficiently, the IP address identifies the destination, and HTTP/HTTPS handles website communication.


# 🏗️ Task 3: Application Architecture & Stack

## Scenario

EpicReads bookstore has two application versions:

### Two-Tier Application

* Frontend
* Database

### Three-Tier Application

* Frontend
* Backend
* Database

## Your Task

* Draw simple diagrams (hand-drawn or tool-based such as draw.io)
* Label each layer clearly
* List at least two common technologies or tools used for each layer
* Submit a screenshot or photo clearly showing your own drawing

## Diagram Screenshot / Photo

Save your diagram image in the `screenshots` folder and update the file name below.

![alt text](<architecture diagram for 2 and 3 tier.png>)


Replace `task-3-diagram.png` with your actual diagram file name.

---

## Technologies Used

### Frontend

React.js
css

### Backend

Node.js
Javascript

### Database

Mysql
MongoDB

---

# 🌍 Task 4: Domain Name & DNS (Basic Concepts)

## Scenario

Your friend's bookstore **EpicReads** is currently accessible through:

```text
52.172.142.222:3000
```

He purchased the domain:

```text
epicreads.com
```

## Your Task

In **50–100 words**, explain in your own words:

1. What is DNS (Domain Name System)?
2. Which DNS record type should be used to connect the domain to the given IP, and why?

## Answer

DNS (Domain Name System) is like the internet’s phonebook. It translates easy-to-remember domain names, such as epicreads.com, into IP addresses that computers use to locate servers.

For EpicReads, an A record should be used because it connects a domain name to an IPv4 address. The A record would point epicreads.com to 52.172.142.222. However, DNS does not normally include the :3000 port number. The server or a reverse proxy would need to handle that port separately so users can simply visit epicreads.com instead of typing the IP address and port.


# 💻 Task 5: Visual Studio Code Setup (Hands-on)

## Your Task

Install Visual Studio Code (if not already installed).

Take a screenshot of your VS Code environment showing:

* Terminal open inside VS Code
* Running a basic command:

### Windows

```powershell
dir
```

### Linux / macOS

```bash
pwd
ls
```

* Your selected VS Code theme clearly visible

⚠️ **Important:** The screenshot must show your username or another identifiable detail to confirm it is your environment.

## Screenshot

Save your screenshot in the `screenshots` folder and update the file name below.

![alt text](<VS Code environment showing dir command on powershell.png>)


Replace `task-5-vscode.png` with your actual screenshot file name.

---

# 🔗 Task 6: Publish Your Assignment as a LinkedIn Post

## Objective

Publishing on LinkedIn helps you:

* Build your professional online presence
* Reinforce your learning
* Document your DevOps journey publicly

## Your Task

Summarize your answers from Tasks 1–5 into a LinkedIn post.

Clearly structure your post into the following sections:

* ChatGPT
* Internet & Networking
* App Architecture
* DNS
* VS Code Setup

Use the credit note that matches your track:

Add the following credit note at the end of your post **(If you are DMI Cohort 3 student)**:

> **P.S. This post is part of the DevOps Micro Internship (DMI) with Agentic AI — Cohort 3 — by Pravin Mishra. My graded progress is public: https://dmi.pravinmishra.com/s/YOUR-GITHUB-USERNAME.html · Start your DevOps journey: https://dmi.pravinmishra.com/?utm_source=student&utm_medium=ps-linkedin&utm_campaign=cohort3**

**Tag [Pravin Mishra](https://www.linkedin.com/in/pravin-mishra-aws-trainer/) in your LinkedIn post, then tag Lead Co-Mentor — [Anjana Muthunayake](https://www.linkedin.com/in/anjana-muthunayake/).**

Add the following credit note at the end of your post **(If you are DMI Self-paced track student)**:

> **P.S. This post is part of the DevOps Micro Internship (DMI) — Self-Paced Engineer Track — by Pravin Mishra. My graded progress is public: https://dmi.pravinmishra.com/s/YOUR-GITHUB-USERNAME.html · Start your DevOps journey: https://dmi.pravinmishra.com/?utm_source=student&utm_medium=ps-linkedin&utm_campaign=self-paced**

Add the following credit note at the end of your post **(If you are DMI Campus student)**:

> **P.S. This post is part of the DevOps Micro Internship (DMI) — Campus — by Pravin Mishra. My graded progress is public: https://dmi.pravinmishra.com/s/YOUR-GITHUB-USERNAME.html · Start your DevOps journey: https://dmi.pravinmishra.com/?utm_source=student&utm_medium=ps-linkedin&utm_campaign=campus**

**Tag [Pravin Mishra](https://www.linkedin.com/in/pravin-mishra-aws-trainer/) in your LinkedIn post, then tag Lead Co-Mentor — [Anjana Muthunayake](https://www.linkedin.com/in/anjana-muthunayake/).**

Hashtags:

#DMIByPravinMishra #AgenticAI #DevOps

Replace `https://github.com/Felimek28` with your GitHub username — that link is your public DMI progress page (your graded badge page).
---

## LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/felix-nwobodo-2a191856_learning-devops-cloudcomputing-ugcPost-7389282072856788993-juoT/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAAvh1JkBJ6D4mRJp1t4mfqeNh2YQjVD8ZhE

---

## LinkedIn Post Backup Copy

Paste the full text of your LinkedIn post here:

I recently got free access from renowned trainer Pravin Mishra to join DevOps for beginners training on Udemy.

Here, I have learned: 
Task 1️⃣ How to us ChatGPT as my learning assistant - One of my first lessons was understanding how to prompt ChatGPT effectively. By asking clear, specific, and well-structured questions, I can get detailed, and beginner-friendly explanations.

 Task 2️⃣ Internet & Networking – I learned what the internet is and how networking works. How devices connect and communicate globally. Concept of protocols in networking such as, IP, TCP, HTTPS etc were perfectly understood. These are set of rules for data exchange. Also packet switching, which ensures efficient and reliable data transfer was well understood. 

Task 3️⃣ Application Architecture & Stack , explaining the difference between: *Two-Tier Apps - Frontend which is directly connected to the Database. *Three-Tier Apps - Frontend, Backend, and Database separated into layers for scalability, security, and performance. I also discovered common tools for each layer, such as React.js, CSS(frontend), Node.js, Django, Javascript (backend), and MySQL/MongoDB (database). 

Task 4️⃣ Domain Name System (DNS), which is the Internet’s phonebook, this translates easy-to-remember domain names into IP addresses that computers use to find each other. DNS record types which are; A Records, AAAA Records, CNAME Record, etc. 

Task 5️⃣ VS Code Setup - I successfully set up Visual Studio Code for development, installed some extensions, and configured my workspace. 
Basic Linux commands such as pwd, dir, ls were demonstrated on my VS Code terminal.
Stay tuned as I will be updating my learning and practicing experience as I delve deeper into DevOps and Cloud Computing. All Thanks to Pravin Mishra.

P.S. This post is part of the FREE DevOps Micro Internship Cohort run by Pravin Mishra. You can start your DevOps journey for free from his YouTube Playlist https://lnkd.in/euf7MuQD


#learning
#devOps
#cloudcomputing


# Reflection – Week 0

### What did you find easy?

Using ChatGPT was easy to me

### What was difficult?

I struggled to install VS Code initially but later sorted it out

### What will you improve next week?

I will improve on the use of good prompt to find answer to my questions on chatGPT

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.


## 📌 Resources

- 🌐 **DMI Official Website:** https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 **University:** https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 **Discord Community:** https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 **Blog:** https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ **YouTube Playlist (DMI Cohort 3):** https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 **Pravin Mishra (LinkedIn):** https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 **CloudAdvisory (LinkedIn):** https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track*