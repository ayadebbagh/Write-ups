"The password for the next level is stored in the file **data.txt**, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work."

First, let's understand what a hex dump is:
A hex dump is a textual display of binary data (from a file, memory, or storage device) presented in hexadecimal, a base-16 number system.

![[Screenshot 2025-09-16 at 1.11.40 PM.png]]

After making a directory in `/tmp` and copying `data.txt`, we can use `xxd -r hexdump.txt compressed.txt` to reverse the hexdump and have the compressed data in compressed.txt `. To get more information on the file, we can use ` file compressed.txt ` which tells us that it is a gzip compressed data file. When doing ` gzip -d compressed.txt `, we get a ` gzip: compressed.txt: unknown suffix -- ignored `. This tells me that the file needs to have the right extension to be able to use gzip.
We rename `compressed.txt`  to ` compressed.txt.gz ` and unzip it. We get a `compressed.txt`file back. ![[Screenshot 2025-09-16 at 7.58.57 PM.png]]
We can use file  again to get more information, and we learn that this new file is a bzip2 compressed data file. Doing the same thing again, we rename ` compressed.txt ` to ` compressed.txt.bz2 `, decompress it with ` bsip2 -d compressed.txt.bz2 ` which gives us  another gzip file so we repeart the decompressing a couple more time.
![[Screenshot 2025-09-16 at 8.00.33 PM.png]]
Until we get a `tar file`, which gives us a second and third`tar` file after doing the same process.

![[Screenshot 2025-09-16 at 8.02.24 PM.png]]

The last file `data8.bin` gives us an ASCIII file when we decompress it, and it has the password
![[Screenshot 2025-09-16 at 8.03.41 PM.png]] Password: FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn



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
