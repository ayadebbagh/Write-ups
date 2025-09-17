**Challenge**  
The password for the next level is stored in `/etc/bandit_pass/bandit14` and can **only** be read by user `bandit14`. For this level you are given a **private SSH key** instead of the password.  
**Note:** `localhost` refers to the machine you are working on.

---

### Step 1: Inspect the working directory
Listing files in the current directory reveals a private SSH key (for example `sshkey.private`). This key must be used to authenticate as `bandit14`.

---

### Step 2: SSH using the private key
Use the `-i` option to supply the private key to `ssh`, and `-p` to specify the game port:

```bash
ssh -i sshkey.private -p 2220 bandit14@bandit.labs.overthewire.org
````

This logs you in as `bandit14` using key-based authentication.

---

### Step 3: Read the password file

Once connected as `bandit14`, display the password:

```bash
cat /etc/bandit_pass/bandit14
```

The output is the password for the next level.

---

**Password for level 14:** 
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS