

**Challenge**  
The password for the next level is stored in **data. Txt**, where all lowercase (`a–z`) and uppercase (`A–Z`) letters have been rotated by 13 positions (ROT 13).

---

### Step 1: Recognizing ROT 13
ROT 13 is a simple substitution cipher that shifts each letter by 13 positions in the alphabet:

- `A ↔ N`, `B ↔ O`, ..., `M ↔ Z`
- `a ↔ n`, `b ↔ o`, ..., `m ↔ z`

ROT 13 is symmetrical: applying it twice returns the original text.

---

###  Step 2: Choosing a tool
There are several tools that can perform ROT 13 (for example `perl`, `python`, or `openssl` tricks), but the standard UNIX utility `tr` is simple and well-suited for character-by-character translations.

`tr` maps characters from the first set (argument 1) to corresponding characters in the second set (argument 2), position-by-position.

---

## Step 3#: Constructing the `tr` ranges
- The source set: `'A-Za-z'` expands to `A..Z` then `a..z`.
- The target set should be the alphabet shifted by 13: `'N-ZA-Mn-za-m'`, which expands to `N..Z A..M n..z a..m`.

So the mapping becomes:

A B C ... M N O ... Z a b c ... m n o ... z  
| | | |  
N O P ... Z A B ... M n o p ... z a b ... m

---

## Step 4: Decrypting the file
Run `tr` to translate characters and print the result. You can either pipe `cat` into `tr` or redirect the file into `tr` directly.

Examples:

```bash
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```
---

Running the translation on the challenge file produced the password:

**Password for level 12**: 
`7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4`