"The password for the next level is stored in a file somewhere under the **inhere** directory and has all of the following properties:

- human-readable
- 1033 bytes in size
- not executable"

The first thing I thought of doing was using `find` with the exact size, if that gave more than one result, i could make the search more specific. Doing `find . -size 1033c` gave only one file so the search is done. If we had to make the search more specific, we could do `find . -size 1033c ! -executable`, which specifies that the file is not an executable, or `find . -size 1033c ! -executable -exec file {} +`

Password: HWasnPhtq9AVKe0dmk45nxy20cvUa6EG