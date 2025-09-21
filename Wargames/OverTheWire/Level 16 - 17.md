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

## Step 7: Save the private key and set correct permissions

I saved the key to a temporary file (example steps):

```bash
# create a work dir and save the key
mkdir -p /tmp/bandit17
cd /tmp/bandit17

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

## Step 8: SSH to the next level using the key

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

---

## Command Log (summary of the exact commands used)

```bash
# scan only the required port range
nmap -p 31000-32000 127.0.0.1

# test TLS on candidate ports
openssl s_client -connect localhost:<port>
# if KEYUPDATE or renegotiation breaks input, retry with:
openssl s_client -connect localhost:31790 -ign_eof

# save returned RSA key to a file
mkdir -p /tmp/bandit17 && cd /tmp/bandit17
cat > sshkey17.private   # paste key, then Ctrl-D

# fix permissions so ssh accepts the key
chmod 400 sshkey17.private

# connect using the key
ssh -i sshkey17.private -p 2220 bandit17@bandit.labs.overthewire.org

# retrieve next password
cat /etc/bandit_pass/bandit17
```

---

## Notes / troubleshooting

- `ss -l` lists many sockets; use `nmap` to focus on a port range.
    
- If `openssl s_client` prints `KEYUPDATE`, `RENEGOTIATING`, or similar messages and you cannot send input reliably, try `-ign_eof` (or consult the “CONNECTED COMMANDS” section in the manpage).
    
- Private keys must have restrictive permissions (e.g., `chmod 400`) or `ssh` will refuse to use them.
**Credentials for level 17:**
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----

To use this to connect to the next level, we need to create a file and save this key to it, we do that by creating a temp folder with `mktemp` and using the command `cat > sshkey17.private` with whatever you want to name the file. We can now use the file as a key to connect using `ssh -i sshkey17.private -p 2220 bandit17@bandit.labs.overthewire.org`. When doing this the first time, I got a `Permissions 0664 for 'sshkey17.private' are too open. It is required that your private key files are NOT accessible by others. This private key will be ignored. Load key "sshkey17.private": bad permissions` error. To fix this, we use `chmod 400 sshkey17.private` which grants only the owner or user of the file read permission while restricting everyone else entirely. When using the key again to connect over ssh, it works.

To avoid having to do all this again to reconnect, we can get the password to the level with `cat /etc/bandit_pass/bandit17` once we are in the bandit 17 level

**Password**: 
EReVavePLFHtFlFsjn3hyzMlvSuSAcRD