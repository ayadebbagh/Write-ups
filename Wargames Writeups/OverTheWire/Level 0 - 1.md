````markdown
# Bandit Level → Next Level Write-Up (File `readme`)

**Challenge**  
The password for the next level is stored in a file called **readme** located in the home directory.

---

## Step 1: Verify the file exists
Use `ls` to check if the file is present in the current directory:

```bash
ls
````

Output includes:

```
readme
```

---

## Step 2: Display the file contents

Use `cat` to read the file:

```bash
cat readme
```

Output:

```
ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
```

---

## ✅ Password for next level

```
ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
```

---

## Notes / Tips

- `cat` is the simplest way to read a small text file.
    
- Always verify the file exists in the directory with `ls` before trying to read it.

Password: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If