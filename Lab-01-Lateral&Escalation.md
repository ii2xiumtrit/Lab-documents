<h1 align="center">
  Lab 01 | Lateral movement & Basic Privilege Escalation.
</h1>
<small align="center">This documentation will be formatted into a formal pentest report, to practice and also to look back and see see where I can improve.</small>

<h3>
  <strong>Lab Report: </strong> 01 | A Misconfiguration in Bean's directory.
</h3>

<h3>
  I. Executive Summary.
</h3>
<p>-Objective: Use bean's misconfiguration to gain excessive control or obtain confidential information.</p>
<p>-Result: <i>Success</i>, Obtained confidential information. </p>

<h3>II. Risk Rating.</h3>
<p>Risk severity: High. </p>

<h3>III. Impact.</h3>
<p>May gain super confidential information & may potentially compromise the system if configuration for Bean's are overlooked.</p>

<h3>IV. Technical Findings & Walkthrough</h3>
<p>- Findings: /home/bean/.ssh/id_rsa file SUID binary is world readable. (707) sudo privilege for nmap in Bean's account, leads to obtaining the root content and may potential even access greater information. </p><br>
  
```` 
john@TrainingGrounds:/home/Bean$ ls -la
total 24
drwx---rwx 3 Bean Bean 4096 Nov  2 21:11 .
drwxr-xr-x 5 root root 4096 Nov  2 20:48 ..
-rw-r--r-- 1 Bean Bean  220 Nov  2 20:48 .bash_logout
-rw-r--r-- 1 Bean Bean 3771 Nov  2 20:48 .bashrc
-rw-r--r-- 1 Bean Bean  807 Nov  2 20:48 .profile
drwx---rwx 2 Bean Bean 4096 Nov  2 20:54 .ssh
````

Step 1: Obtaining the id_rsa through an python http server or copy and pasting the content to the attacker's computer.
step 2: Using ssh2john.py to convert it to hash.<br>
`````test````` <br>
step 3: doing a offline dictionary attack on the hash using rockyou wordlist.

