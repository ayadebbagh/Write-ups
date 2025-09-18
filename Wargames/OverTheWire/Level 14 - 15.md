**Challenge**  
The password for the next level can be retrieved by submitting the password of the current level to **port 30000 on localhost**.

---

### Step 1: Get the current password  
The current level's password is stored in a file that can be read directly:

```bash
cat /etc/bandit_pass/bandit14
````

Copy the output — this is the password you must submit to the service running on `localhost:30000`.

---

### Step 2: Choose a tool to send the password

The suggested tools include `nc` (netcat), which can read from and write to network connections. The basic usage is:

```bash
nc <host> <port>
```

`localhost` resolves to `127.0.0.1`, so the host/port you need is `127.0.0.1 30000`.

---

### Step 3: Submit the password (two options)

#### Option A — non-interactive (pipe the password)

Use `echo` and a pipe to send the password and receive the response in one command:

```bash
echo "<password_from_file>" | nc 127.0.0.1 30000
```

This will print the service response (the next level password) to stdout.

#### Option B — interactive

Connect with `nc` and type/paste the password manually, then press Enter:

```bash
nc 127.0.0.1 30000
# then type/paste the password and press Enter
```

Either method returns the password for the next level.

---
**Password for level 15:** 
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo