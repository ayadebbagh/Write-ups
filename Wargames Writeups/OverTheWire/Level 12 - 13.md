
**Challenge**:  
The password for the next level is stored in **data.txt**, which is a hexdump of a file that has been repeatedly compressed. The instructions suggest working in a temporary directory under `/tmp`.

---

### Step 1: Understanding the file

A **hexdump** is a textual representation of binary data, displayed in hexadecimal (base-16). To recover the original file, the hexdump must be reversed back into binary.
![[Screenshot 2025-09-16 at 1.11.40 PM.png]]

After creating a working directory in `/tmp` and copying `data.txt` there, I used:

```bash
xxd -r hexdump.txt compressed.txt
```

This reversed the hexdump into its binary form.


---

### Step 2: Identifying the file type

Next, I ran:

```bash
file compressed.txt
```

which reported the file as **gzip compressed data**.

When attempting:

```bash
gzip -d compressed.txt
```

I got:

```
gzip: compressed.txt: unknown suffix -- ignored
```

This happens because `gzip` expects the file to have a proper extension. Renaming the file to `compressed.bin.gz` allowed decompression.


---

### Step 3: Chained decompressions

Each time I decompressed the file, the result turned out to be compressed again — but with a different format. I repeatedly checked the type with `file` and renamed the file accordingly before decompressing:

- Gzip → `.gz` → decompressed with `gzip -d`
    
- Bzip2 → `.bz2` → decompressed with `bzip2 -d`
    
- Gzip again
    
- Several layers of `tar` archives, which I extracted using `tar -xvf`
    

This process of renaming, identifying, and decompressing repeated multiple times.

![[Screenshot 2025-09-16 at 7.58.57 PM.png]]
![[Screenshot 2025-09-16 at 8.00.33 PM.png]]
![[Screenshot 2025-09-16 at 8.02.24 PM.png]]


---

### Step 4: Final result

Eventually, I extracted down to a file named `data8.bin`. Running `file data8.bin` revealed it was ASCII text. Viewing it gave the password:

```
FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```
![[Screenshot 2025-09-16 at 8.03.41 PM.png]]

---

**Password for Level 13**:  
`FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`
