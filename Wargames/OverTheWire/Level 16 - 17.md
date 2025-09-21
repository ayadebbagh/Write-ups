**Challenge**  
The credentials for the next level can be retrieved by submitting the current level password to **a port on localhost in the range 31000–32000**.  
Task:  
1. Find which ports in that range have a server listening.  
2. Determine which of those speak SSL/TLS and which don’t.  
3. Submit the password to the one service that will return the next credentials (the others echo what you send).

> Helpful note: if you see “DONE”, “RENEGOTIATING” or “KEYUPDATE”, read the “CONNECTED COMMANDS” section of the openssl s_client manpage.

---

### Step 1: Initial (failed) attempt — `ss -l`
I first tried:

```bash
ss -l
````

This lists all listening sockets, but it produced too many ports to inspect manually and wasn't convenient for restricting to the challenge range.

---

### Step 2: Use `nmap` to scan the specific range

To focus only on the relevant ports, I used `nmap` against localhost for the specified range:

```bash
nmap -p 31000-32000 127.0.0.1
```

This returned a small set of open ports (5 in my run), which I could test individually instead of checking every listening socket on the system.

---

### Step 3: Test SSL/TLS with `openssl s_client`

For each open port reported by `nmap`, I tried connecting with `openssl` to see if it speaks TLS:

```bash
openssl s_client -connect localhost:<port>
```

I did this on three candidate ports first, but they did not return the credentials — they simply echoed input or rejected it.

---

### Step 4: Hitting the right port (and encountering `KEYUPDATE`)

I tried:

```bash
openssl s_client -connect localhost:31790
```

When I pasted the current password into the `openssl` session, I repeatedly got a `KEYUPDATE` message followed by a “wrong password” response. The repeated `KEYUPDATE` made the session behave oddly and not accept my input as intended.

---

### Step 5: Diagnose the `KEYUPDATE` issue and fix

After researching, I learned that my input was triggering a TLS **key update** because of the way `openssl s_client` interacts with typed input in some versions and because the password begins with the letter `k`. The `KEYUPDATE` output meant the session was renegotiating keys and closing/altering the input stream before the service could receive the password.

The fix was to add the `-ign_eof` option to `openssl s_client`, which prevents it from treating EOF/keyboard input specially and allows the exact password to be transmitted cleanly:

```bash
openssl s_client -connect localhost:31790 -ign_eof
```

With `-ign_eof`, pasting the password worked — the service accepted it and returned the credentials.

---

### Step 6: Received private RSA key (credentials for level 17)

The service returned a private RSA key block (truncated here for brevity in this write-up); the full key was printed to the terminal:

```
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
...
-----END RSA PRIVATE KEY-----
```

---

### Step 7: Save the private key and set correct permissions

I saved the key to a temporary file (example steps):

```bash
# create a work dir and save the key
mktemp -d
cd /tmp/tmp.dLR7iSkWuq

# paste the key into a file (Ctrl-D to finish)
cat > sshkey17.private
# (paste the key block, then Ctrl-D)
```

On the first SSH attempt I got:

```
Permissions 0664 for 'sshkey17.private' are too open. ... This private key will be ignored. Load key "sshkey17.private": bad permissions
```

To fix the permissions, I restricted access:

```bash
chmod 400 sshkey17.private
```

This ensures only the owner (me) can read the key, as required by `ssh`.

---

### Step 8: SSH to the next level using the key

Now connect using the saved key:

```bash
ssh -i sshkey17.private -p 2220 bandit17@bandit.labs.overthewire.org
```

This logs you in as `bandit17`. Once connected, retrieve the password file:

```bash
cat /etc/bandit_pass/bandit17
```

---

## Final result — Password for level 17

```
EReVavePLFHtFlFsjn3hyzMlvSuSAcRD
```
