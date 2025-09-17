**Challenge**  
The password for the next level is stored in a file called **-** located in the home directory.

---

### Step 1: Understanding the issue
Running:

```bash
cat -
````

does **not** display the file contents. In Unix, using `-` as an argument usually refers to **STDIN/STDOUT**, so `cat` reads from standard input instead of the file.

---

### Step 2: Correct ways to read the file

#### Option 1: Use the full path

```bash
cat ./-
```

#### Option 2: Input redirection

```bash
cat < -
```

Both methods tell `cat` explicitly to read the file named `-` instead of interpreting it as STDIN.

---
**Password for level 2:**
263JGJPfgU6LtdEvgfWU1XP5yac29mFx
