**Challenge**  
The password for the next level is stored in a file somewhere under the **inhere** directory and has all of the following properties:

- human-readable  
- 1033 bytes in size  
- not executable  

---

### Step 1: Search by file size
The first step is to use `find` to locate files of the exact size. Starting with:

```bash
find . -size 1033c
````

In this case, it returned only **one file**, so the search is complete.

---

### Step 2: Narrowing the search (if needed)

If multiple files had matched, we could have refined the search by specifying additional properties:

- Exclude executables:
    

```bash
find . -size 1033c ! -executable
```

- Check if the file is human-readable using `file`:
    

```bash
find . -size 1033c ! -executable -exec file {} +
```

This runs the `file` command on all matching files to confirm their type.

---
**Password for level 6:**
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG