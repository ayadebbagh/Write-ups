
**Challenge**  
The password for the next level is stored in the file **data.txt** and is the only line of text that occurs exactly once.

---

### Step 1: What `uniq` does  
`uniq` only compares **adjacent** lines. It collapses runs of identical adjacent lines into a single line:

```

input: a a a b b c  
uniq: a b c

````

Because `uniq` only looks at neighbors, it cannot by itself find lines that appear exactly once in the whole file if duplicates are scattered.

---

### Step 2: Make duplicates adjacent with `sort`  
To make all identical lines adjacent, sort the file first. That way `uniq` can operate on full runs of identical lines:

```bash
sort data.txt
````

After sorting, all identical strings are grouped together.

---

### Step 3: Use `uniq -u` to print only lines that occur once

`uniq -u` prints only lines that occur **exactly once** in the (already-sorted) stream. Combine `sort` and `uniq -u`:

```bash
sort data.txt | uniq -u
```

That prints the line(s) that appear only once in the entire file — in this challenge, that single line is the password.

---
### Example

Given `example.txt`:

```
apple
banana
apple
cherry
banana
date
```

Running:

```bash
sort example.txt | uniq -u
```

produces:

```
cherry
date
```

(These are the lines that occur exactly once in the whole file.)

---

## Command Log (for reference)

```bash
# preferred, no useless cat
sort data.txt | uniq -u

# alternative that shows counts (helpful to verify)
sort data.txt | uniq -c | sort -n

# if you prefer to use cat (works the same)
cat data.txt | sort | uniq -u
```

---

**Password for level 9:**
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM