**Challenge**  
The password for the next level is stored in a **hidden file** in the **inhere** directory.

---

### Step 1: List hidden files
Hidden files in Linux start with a dot (`.`). To view them, use:

```bash
ls -a
````

This reveals a hidden file named:

```
...Hidden-From-You
```

---

### Step 2: Display the file contents

Because the filename contains dashes, enclose it in quotes or escape special characters when using `cat`:

```bash
cat ./...Hidden-From-You
```

This outputs the password.

---
**Password for level 4:**
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
