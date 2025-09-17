**Challenge**  
The password for the next level is stored **somewhere on the server** and has all of the following properties:

- owned by user `bandit7`  
- owned by group `bandit6`  
- exactly 33 bytes in size

---

### Step 1: Understand the requirements
We need to search the entire filesystem for a file that matches three attributes:

- `-user bandit7` — owned by user `bandit7`  
- `-group bandit6` — owned by group `bandit6`  
- `-size 33c` — exactly 33 **bytes** (`c` stands for bytes in `find`)

Searching from `/` will look across the whole server.

---

### Step 2: Basic `find` usage
A straightforward `find` command to match those attributes is:

```bash
find / -user bandit7 -group bandit6 -size 33c
````

However, running `find / ...` as a normal user will try to descend into directories you don't have permission to read and will print many `Permission denied` errors to stderr.

---

### Step 3: Suppress permission errors

To hide the noisy `Permission denied` messages, redirect stderr to `/dev/null`:

```bash
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

This prints only matching paths (if any) and hides error messages.

---

### Step 4: View the password

Once `find` returns the matching file path, you can display its contents with `cat`:

```bash
# example: if find prints /path/to/targetfile
cat /path/to/targetfile
```


---

**Password for level 7:**
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj