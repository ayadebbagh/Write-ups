**Challenge**  
The password for the next level is stored in the **only human-readable file** in the **inhere** directory.

---

### Step 1: Identify file types
To determine the type of a file, use:

```bash
file <filename>
````

Checking the first few files:

```bash
file ./-file00
file ./-file01
file ./-file02
...
file ./-file06
```

All of these files return:

```
data
```

---

### Step 2: Find the human-readable file

When checking `-file07`:

```bash
file ./-file07
```

The output shows:

```
ASCII text
```

This indicates that `-file07` is the human-readable file containing the password.

---

### Step 3: Read the password

Use `cat` to display the contents:

```bash
cat ./-file07
```

Output:

```
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
```


---
**Password for level 5:**
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw