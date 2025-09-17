**Challenge**  
The password for the next level is stored in a file called `--spaces in this filename--` located in the home directory.

---

### Step 1: Attempting to read the file
Trying the straightforward command:

```bash
cat '--spaces in this filename--'
````

results in:

```
cat: unrecognized option '--spaces in this filename--'
```

This happens because `cat` interprets the leading `--` as an **option flag**, not part of the filename.

---

### Step 2: Correct ways to read the file

#### Option 1: Use the full path with `./`

```bash
cat ./--spaces\ in\ this\ filename--
```

or equivalently:

```bash
cat ./'--spaces in this filename--'
```

#### Option 2: Use `--` to stop option parsing

The `--` signals to `cat` that the following arguments are filenames, not options:

```bash
cat -- '--spaces in this filename--'
```

#### Option 3: Input redirection (alternative)

```bash
cat < '--spaces in this filename--'
```

All of these methods allow you to read the file contents without confusing `cat` with options.

---
**Password for level 3:**
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx