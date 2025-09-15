"The password for the next level is stored in the file **data.txt** in one of the few human-readable strings, preceded by several ‘=’ characters."

Started off with `cat data.txt | grep '='` this returned `grep: (standard input): binary file matches`. Upon reading more on how grep works, I tried adding the `-a` options which searches binary files as text. This returned a lot of text that did not look relevant. I ended up using the `strings` command which only looks at human-readable text and used grep pn that `strings data.txt | grep "w"` which gave me some options including the solution

Password: FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey