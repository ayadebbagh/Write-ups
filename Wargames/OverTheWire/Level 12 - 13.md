
**Challenge**:  
The password for the next level is stored in **data. Txt**, which is a hexdump of a file that has been repeatedly compressed. The instructions suggest working in a temporary directory under `/tmp`.

---

### Step 1: Understanding the file

A **hexdump** is a textual representation of binary data, displayed in hexadecimal (base-16). To recover the original file, the hexdump must be reversed back into binary.


After creating a working directory in `/tmp` and copying `data.txt` there, I used:

```bash
xxd -r hexdump.txt compressed.txt
```

This reversed the hexdump into its binary form.
![](Screenshot2025-09-16at8.56.43PM.png)

---

### Step 2: Identifying the file type

Next, I ran:

```bash
file compressed.txt
```

Which reported the file as **gzip compressed data**.

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
    
- Bzip 2 → `.bz2` → decompressed with `bzip2 -d`
    
- Gzip again
    
- Several layers of `tar` archives, which I extracted using `tar -xvf`
    

This process of renaming, identifying, and decompressing repeated multiple times.
![](Screenshot2025-09-16at8.57.57PM.png)
![](Screenshot2025-09-16at8.59.07PM.png)


---

### Step 4: Final result

Eventually, I extracted down to a file named `data8.bin`. Running `file data8.bin` revealed it was ASCII text. Viewing it gave the password:

```
FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```
![](Screenshot2025-09-16at8.59.39PM.png)

---

**Password for Level 13**:  
`FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn`
