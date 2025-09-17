**Challenge**  
Logging into the game via SSH. The SSH server uses a **non-standard port** (not 22).

---

### Step 1: SSH with a custom port
To connect to a server on a port other than 22, use the `-p` option:

```bash
ssh -p [port_number] [username]@[remote_host]
````

- `-p [port_number]` specifies the port to connect to
    
- `[username]` is your Bandit level username
    
- `[remote_host]` is the server hostname


---

### Step 2: Login command for Bandit 0

For Bandit level 0:

```bash
ssh -p 2220 bandit0@bandit.labs.overthewire.org
```

This connects you to the Bandit game server on port `2220` using the `bandit0` account.
