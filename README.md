# Vulnerable-machine-Vulnhub---Bluemoon

## Objective

The goal of this challenge was to gain root access to the Bluemoon machine by identifying vulnerabilities and escalating privileges.


## 1. Finding Target IP

The first step was to identify the target machine on the network.

<img width="624" height="199" alt="image" src="https://github.com/user-attachments/assets/6bde82ee-0b7a-4fdd-945e-bbd7c682f25a" />

This revealed the IP address of the Bluemoon machine: 192.168.56.103.


## 2. Nmap Scan

A network scan was performed using Nmap to identify open ports and services.

<img width="624" height="334" alt="image" src="https://github.com/user-attachments/assets/1189ca86-a0a0-47f8-8eae-104b63eb8cf4" />

| Port | Service | Version |
| --- | --- | --- |
| 21 |  FTP | 	vsftpd 3.0.3|
| 22 | SSH  |  OpenSSH 7.9p1|
| 80 | HTTP | 	Apache 2.4.3|


## 3. Website Enumeration

Gobuster was used to enumerate directories on the web server using the DirBuster medium wordlist.

<img width="624" height="420" alt="image" src="https://github.com/user-attachments/assets/38a86de4-a039-4443-aee5-30a8ebf09877" />

Gobuster Results

The scan discovered a hidden directory, /hidden_text, which returned HTTP 200:


## 4. Game Begins

Upon visiting the page in Firefox, a message indicating the start of the challenge was displayed.

<img width="945" height="762" alt="image" src="https://github.com/user-attachments/assets/d0e1f5f5-269c-45c1-b508-e57db8cb3a15" />


This confirmed that the web application is the primary entry point and further analysis was required.


## 5. FTP Anonymous login test

Port scanning discovered open services on the target machine (`192.168.56.103`)

<img width="698" height="187" alt="image" src="https://github.com/user-attachments/assets/07acb926-4746-47a9-8c73-266b398a1379" />

Investigate the website further

<img width="971" height="445" alt="image" src="https://github.com/user-attachments/assets/76c83d68-3d6f-4eb3-9302-548690b355cc" />


## 6. Source Code Inspection

The page source code was inspected to uncover hidden information.

<img width="948" height="406" alt="image" src="https://github.com/user-attachments/assets/391bd72a-6721-4783-b34e-bfd682e95a1b" />

This revealed useful hints that guided further exploitation.


## 7. Bluemoon Page

Further navigation led to the Bluemoon page.

<img width="1917" height="912" alt="image" src="https://github.com/user-attachments/assets/95d17876-b7b5-48a3-a083-27e6ab661f3d" />

This page provided additional context for the challenge.


## 8. Password List Analysis

The information.txt file was opened using:

<img width="966" height="145" alt="image" src="https://github.com/user-attachments/assets/87ff95a5-aa99-4d58-9312-27630d6c9d91" />

Next, the password list was displayed using:

<img width="367" height="563" alt="image" src="https://github.com/user-attachments/assets/85cae7b1-4c31-4e86-8442-26c98b662f6f" />

information.txt Content p_lists.txt Content

The file contained several possible passwords for the Robin account.

## 9. SSH Password Testing Using Hydra

Hydra was used to test the password candidates against the SSH service of the Bluemoon target.

<img width="1672" height="178" alt="image" src="https://github.com/user-attachments/assets/eaa6cb4e-53e6-4f94-9af3-6f0659713cf8" />

-l robin specifies the username.

-P p_lists.txt specifies the password list.

-t 4 limits the number of parallel tasks.

-ssh://192.168.56.104 specifies the target SSH service.

Hydra successfully identified a valid password for the Robin account.


## 10. Initial Access Through SSH

SSH was used to connect to the target using the credentials discovered by Hydra.

<img width="783" height="268" alt="image" src="https://github.com/user-attachments/assets/3ea5f59a-dbb1-4daf-a4c1-e7e3a58e03a8" />

After successful authentication, the current username was verified using:

<img width="475" height="51" alt="image" src="https://github.com/user-attachments/assets/5e1a9f83-5bec-4e42-bec0-52a785294479" />

The output confirmed that the current user was robin.


## 11. First User Flag

The first user flag was retrieved from Robin's home directory.

<img width="547" height="120" alt="image" src="https://github.com/user-attachments/assets/78aa7148-4aad-4e75-9e14-119e2eb2c885" />


## 12. Privilege Escalation – Robin to Jerry

The sudo -l command was used to check Robin's permitted sudo commands.

<img width="920" height="127" alt="image" src="https://github.com/user-attachments/assets/ccf7677a-13ed-4313-9de7-21841f4e18da" />

The output showed that Robin could execute the following script as Jerry without entering a sudo password:

The script was inspected using:

<img width="680" height="230" alt="image" src="https://github.com/user-attachments/assets/5f561f12-4373-4c18-b90f-707d452f9809" />

The script contained the following command:

<img width="450" height="40" alt="image" src="https://github.com/user-attachments/assets/84d9b6f8-1e80-42ff-9bff-acac25e6531b" />

This was a security weakness because user-supplied feedback was executed directly as a shell command.

The script was executed as Jerry using:

<img width="632" height="83" alt="image" src="https://github.com/user-attachments/assets/2f09ee5c-45cb-4bf2-b04d-3653cc7302ed" />

<img width="535" height="130" alt="image" src="https://github.com/user-attachments/assets/555fdbba-33ca-409d-b904-f4a3eb53fe4d" />

The new shell was verified using:

<img width="682" height="216" alt="image" src="https://github.com/user-attachments/assets/1b23c781-ba17-4f51-a74d-dd9049626b16" />

The output confirmed the current user as jerry. The insecure feedback script allowed privilege escalation from Robin to Jerry.


## 13. Second User Flag

The second flag was retrieved from Jerry's home directory.

<img width="617" height="182" alt="image" src="https://github.com/user-attachments/assets/161837ff-2b28-4c61-98b3-ffe92d715c41" />

Second User Flag

The user's group membership was also checked using:

<img width="577" height="90" alt="image" src="https://github.com/user-attachments/assets/d5f780fe-456e-4e8a-8672-0978c51ba0c5" />

The output showed that Jerry belonged to the docker group, a possible privilege escalation path.


## 14. Privilege Escalation – Jerry to Root

Since Jerry belonged to the Docker group, a Docker container was started with the host's root filesystem mounted inside it.

<img width="513" height="45" alt="image" src="https://github.com/user-attachments/assets/f1a5d5fb-9771-4134-a11e-0dd0b5296daf" />

This command mounted the host filesystem at /mnt inside the container and started a shell using the mounted filesystem as its root directory.

Root access was verified using:

<img width="272" height="52" alt="image" src="https://github.com/user-attachments/assets/dd5675db-6060-416a-a42d-123fae27c481" />

The output confirmed root. Root privileges were successfully obtained through Docker access.


## Final Root Flag

The final root flag was retrieved using:

<img width="482" height="332" alt="image" src="https://github.com/user-attachments/assets/2057226e-eff8-4938-8296-0d40fa786b58" />

Final Root Flag

The machine displayed a congratulations message confirming successful root access.

## Summary of Flags Obtained:

| Account | Results | 
| --- | --- | 
| Robin |  First user flag obtained | 	
| Jerry | Second user flag obtained |  
| Root  | Final root flag obtained  | 	
