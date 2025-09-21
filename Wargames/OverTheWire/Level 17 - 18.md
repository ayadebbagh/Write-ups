**Challenge**  
There are two files in the home directory: `passwords.old` and `passwords.new`. The password for the next level is in `passwords.new` and is the **only line** that was changed between the two files.

---

### Step 1: Use `diff` to find differences  
The `diff` command shows line-by-line differences between two files. Run:

```bash
diff passwords.new passwords.old
````

`diff` reports the change in this form:

```
42c42
< x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
> CgmS55GVlEKTgx8xpW8HuWnHlBKP924b
```

---

### Step 2: Interpret the `diff` output

- `42c42` means **line 42** in `passwords.new` was **changed** compared to line 42 in `passwords.old`.
    
- Lines beginning with `<` are from `passwords.new`.
    
- Lines beginning with `>` are from `passwords.old`.
    

So `x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO` (the `<` line) is the new line and therefore the password.

-## Command Log (for reference)

```bash
# show the difference between the files
diff passwords.new passwords.old

# if you prefer a side-by-side view
diff --side-by-side passwords.new passwords.old
```

---
**Password for level 18:**
x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO