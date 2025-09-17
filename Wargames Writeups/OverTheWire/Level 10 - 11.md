
**Challenge**  
The password for the next level is stored in **data. Txt**, which contains Base 64-encoded data.

---

### Step 1: What is Base 64?
Base 64 is an encoding scheme that represents binary data using 64 printable ASCII characters (`A–Z`, `a–z`, `0–9`, `+`, `/`) and `=` as padding. It’s commonly used to embed binary data in text formats (email, JSON, etc.). To recover the original content, the Base 64 data must be decoded back to binary or text.

---

### Step 2: Choosing the tool
The standard `base64` utility can decode Base 64-encoded input. Many systems also provide `base64 -d` or `base64 --decode`. Other tools (`openssl base64 -d`) can do the same, but `base64` is straightforward and available on most Linux systems.

---

### Step 3: Decoding the file
You can decode the file either by piping or by redirecting input. Redirecting avoids an unnecessary `cat`.

Examples:

```bash
# preferred (no useless cat)
base64 --decode < data.txt

# or equivalently
base64 -d < data.txt

# using a pipe
cat data.txt | base64 --decode
```

---
**Password for level 11:**
dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
