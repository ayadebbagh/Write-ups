
**Challenge**  
The password for the next level is stored in the file **data.txt**, hidden among a few human-readable strings. The correct string is preceded by several `=` characters.

---

## Step 1: Inspecting the file
At first, I tried:

```bash
cat data.txt | grep '='
````

This gave the output:

```
grep: (standard input): binary file matches
```

This happens because `grep` treats the file as **binary** when it encounters non-text bytes.

---

## Step 2: Forcing grep to treat binary as text

Using the `-a` option makes `grep` search a binary file as if it were text:

```bash
cat data.txt | grep -a '='
```

This returned a lot of noisy and irrelevant output, since binary data often contains random `=` characters.

---

## Step 3: Using `strings`

A better approach is the `strings` command, which extracts only human-readable sequences of characters from a binary file. Running:

```bash
strings data.txt
```

gave a cleaner list of text-like content.

To filter for possible passwords (since they’re often alphanumeric), I combined `strings` with `grep`:

```bash
strings data.txt | grep "="
```

This showed lines with `=` in them.  
Among the results, one clearly stood out as the solution.

---
**Password for level 10:**
FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey