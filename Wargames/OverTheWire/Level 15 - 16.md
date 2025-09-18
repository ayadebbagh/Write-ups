**Challenge**  
The password for the next level can be retrieved by submitting the password of the current level to **port 30001 on localhost** using **SSL/TLS** encryption.

---
### Step 1: Connect with an SSL/TLS client

The `openssl` tool provides a simple TLS client via `s_client`. Use it to connect to the host and port:

```bash
openssl s_client -connect 127.0.0.1:30001
```

This opens a TLS-encrypted connection to the service.

---

### Step 2: Submit the password

Once `openssl s_client` connects, type or paste the current password and press `Enter`. The service will respond with the password for the next level.

---
**Password for level 16:**
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx