
**Challenge**  
The password for the next level is stored in the file **data.txt**, next to the word **millionth**.

---

### Step 1: Inspecting the file  
The only file provided is `data.txt`. Since the password is explicitly said to be next to a keyword (`millionth`), the quickest approach is to search for that word.

---

### Step 2: Using `grep`  
To search for the keyword inside the file:

```bash
grep millionth data.txt
````

or equivalently (though slightly redundant):

```bash
cat data.txt | grep millionth
```

This command scans the file for the word **millionth** and returns the full line containing it.

---

### Step 3: Result

The output reveals the line containing the keyword and the password:

```
millionth    dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```


---

### Command Log (for reference)

```bash
# preferred (no useless cat)
grep millionth data.txt

# alternative (works but redundant)
cat data.txt | grep millionth
```

---

Password: dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc