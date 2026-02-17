Here is your write-up organized into a clean, professional Markdown format. I have standardized the commands, separated the questions from the walkthroughs, and formatted the flags for easy reading.

---

# Linux Strength Training: CTF Walkthrough

## 1. Introduction

**Objective:** A guided room for beginners to learn and reinforce Linux command line skills.

* **Step 1:** Deploy the machine by clicking **Start Machine**.

---

## 2. Finding Your Way Around (The `find` command)

This section focuses on using the `find` command to locate files based on filename, size, user/group, dates, and content.

### Challenges

**1. What is the correct option for finding files based on group?**

> `-group`

**2. Find a file with the user named "Francis" and a size of 52 kilobytes in the directory `/home/francis/`.**

* **Command:**
```bash
find /home/francis -type f -user francis -size 52k

```



**3. SSH as Topson (password: topson). Go to `/home/topson/chatlogs` and find the file containing a specific keyword.**

* **Log in:** `ssh topson@10.67.155.190`
* **Search Command:**
```bash
grep -iRl 'keyword'

```


* **Result (Filename):**
> `2019-10-11`



**4. Using `less` to search within a file.**

* **Instruction:** Open the file using `less 2019-10-11`. Type `/keyword` and hit [ENTER] to search.
* **Question:** What are the characters subsequent to the word you found?
* **Answer:**
> `ttitor`



**5. Read the file named `ReadMeIfStuck.txt` and find the Flag.**

* **Step 1:** Read the hint file.
```bash
cat ReadMeIfStuck.txt

```


* **Step 2:** Locate the `additionalHINT` file.
```bash
find / -type f -name additionalHINT 2>/dev/null

```


* **Step 3:** The hint points to a directory named "telephone numbers". Find it:
```bash
find / -type d -name "telephone numbers" 2>/dev/null

```


* **Step 4:** Inside the directory, search for a file created between specific dates (2016-09-11 and 2016-09-13).
```bash
find /home/topson/workflows -type f -newermt 2016-09-11 ! -newermt 2016-09-13

```


* **Step 5:** Read the found file to get the flag.
> `Flag{81726350827fe53g}`



---

## 3. Working with Files

Recap of `mv`, `cp`, renaming, and creating files.

### Challenges

**1. Move all files from current directory to `/home/francis/logs`.**

* **Command:**
```bash
mv * /home/francis/logs

```



**2. Transfer `script.py` from local to remote machine (192.168.10.5).**

* **Command:**
```bash
scp /home/james/Desktop/script.py john@192.168.10.5:/home/john/scripts

```



**3. Rename a folder named `-logs` to `-newlogs`.**

* **Note:** Use `--` to stop command argument parsing for filenames starting with a dash.
* **Command:**
```bash
mv -- -logs -newlogs

```



**4. Copy the file named `encryption keys` to `/home/john/logs`.**

* **Note:** Use quotes for filenames with spaces.
* **Command:**
```bash
cp "encryption keys" /home/john/logs

```



**5. Find `readME_hint.txt` in Topson's directory and retrieve the second flag.**

* **Step 1:** Find the hint.
```bash
find / -type f -name readME_hint.txt 2>/dev/null

```


* **Step 2:** The hint suggests looking for a file ending in `MoveMe.txt`.
```bash
find / -type f -name "*MoveMe.txt" 2>/dev/null

```


* **Step 3:** Move the file `-MoveMe.txt` into the directory `'-march folder'`.
```bash
mv -- -MoveMe.txt '-march folder'/

```


* **Step 4:** Execute the script `/-runME.sh`.
```bash
./-runME.sh

```


* **Flag:**
> `Flag{234@i4s87u5hbn$3}`



---

## 4. Hashing

**Credentials found:** Password for the file is `secret123`.

### Challenges

**1. What is the hash type stored in `hashA.txt`?**

* **User:** sarah / **Pass:** rainbowtree1230x
* **Hash:** `f9d4049dd6a4dc35d40e5265954b2a46`
* **Type:**
> `md4`



**2. Crack `hashA.txt` using John the Ripper.**

* **Password:**
> `admin`



**3. What is the hash type stored in `hashB.txt`?**

* **Hash:** `b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3`
* **Type:**
> `SHA-1`



**4. Crack `hashC.txt` using a specific `.mnf` wordlist.**

* **Step 1:** Locate `ww.mnf` on the remote machine.
* **Step 2:** Transfer it to the local machine.
```bash
scp sarah@10.65.159.171:"/home/sarah/system\ AB/db/ww.mnf" ~

```


* **Step 3:** Crack the hash.
* **Password:**
> `unacvaolipatnuggi`



**5. Crack `hashB.txt` (SHA-1) using rockyou.txt.**

* **Command:**
```bash
john --format=raw-sha1 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

```


* **Password:**
> `letmein`



---

## 5. Decoding Base64

**1. What tool allows us to decode base64 strings?**

> `base64`

**2. Find `encoded.txt` and decode it to find the answer.**

* **Command:**
```bash
cat /home/sarah/"system AB"/managed/encoded.txt | base64 -d > res.txt

```


* **Result:** The decoded text points to a file `ent.txt`.
* **Hash Found:** `bfddc35c8f9c989545119988f79ccc77` (MD4).
* **Cracked Password:**
> `john`



---

## 6. Encryption/Decryption (GPG)

**1. Encrypt `history_logs.txt` using AES-128.**

* **Command:**
```bash
gpg --cipher-algo aes-128 --symmetric history_logs.txt

```



**2. Decrypt the file.**

* **Command:**
```bash
gpg history_logs.txt.gpg

```



**3. Find `layer4.txt` (Pass: bob) and locate the flag.**

* **Process:** This is a nested challenge (Matryoshka doll style). You must decrypt one file to get the instructions/password for the next.
* **Final Clue (Base64 decoded):** "1. Find a file called layer1.txt, its password is hacked."
* **Flag:**
> `Flag{B07$f854f5ghg4s37}`



---

## 7. Cracking GPG Files

**1. Crack `personal.txt.gpg` using `data.txt` wordlist (reversed).**

* **Strategy:** The instructions imply reversing the wordlist using `tac`.
* **Recovered Password:**
> `valamanezivonia`



**2. What is written in the decrypted file?**

* **Command:**
```bash
gpg --decrypt personal.txt.gpg

```


* **Content:**
> `getting stronger in linux`



---

## 8. Reading SQL Databases

**1. Find `employees.sql` and the flag inside the table.**

* **Credentials:** User: `sarah` / Pass: `password`.
* **Process:**
1. Log into MySQL.
2. `use employees;`
3. `show tables;`


* **Search Query:** Look for the flag format inside the `last_name` column.
```sql
select * from employees where last_name like '%{%';

```


* **Result (Flag):**
> *(Flag found in database output)*



---

## 9. Final Challenge

**Step 1: Get the first password.**

* Go to `/home/shared/chatlogs`.
* Grep for "SSH": `grep -iRl "SSH"`
* **Password Found:** `thegreatestpasswordever000`

**Step 2: Get the SQL Backup password.**

* Grep for "back-up": `grep -iRl "back-up"`
* Locate file in `/home/sameer/History LB/labmind/latestBuild/configBDB/`.
* Filter via Grep: `grep -r "^ebq" ...`
* **Password Found:** `ebqattle`

**Step 3: Get James' SSH Password.**

* Locate `backup.gpg` in `/home/shared/sql`.
* Decrypt it using the password from Step 2.
* Log into MySQL as Sameer.
* Query for James:
```sql
select * from employees where first_name like "james";

```


* **Password Found:** `vuimaxcullings`

**Step 4: Root Flag.**

* SSH as James: `ssh james@10.65.168.127`
* Switch to root: `sudo su`
* Read flag: `cat root.txt`
* **Final Flag:**
> `Flag{6$8$hyJSJ3KDJ3881}`



**Status: LINUX STRONGER!**

---

Would you like me to help you generate a specific `john` or `hashcat` command cheat sheet based on the hashing section of this room?
