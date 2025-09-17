"The password for the next level is stored in the file **data.txt**, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work."

First, let's understand what a hex dump is:
A hex dump is a textual display of binary data (from a file, memory, or storage device) presented in hexadecimal, a base-16 number system.

![[Screenshot 2025-09-16 at 1.11.40 PM.png]]

After making a directory in `/tmp` and copying `data.txt`, we can use `xxd -r hexdump.txt compressed.txt` to reverse the hexdump and have the compressed data in compressed.txt `. To get more information on the file, we can use ` file compressed.txt ` which tells us that it is a gzip compressed data file. When doing ` gzip -d compressed.txt `, we get a ` gzip: compressed.txt: unknown suffix -- ignored `. This tells me that the file needs to have the right extension to be able to use gzip.
We rename `compressed.txt`  to ` compressed.txt.gz ` and unzip it. We get a `compressed.txt`file back. ![[Screenshot 2025-09-16 at 7.58.57 PM.png]]
We can use file  again to get more information, and we learn that this new file is a bzip2 compressed data file. Doing the same thing again, we rename ` compressed.txt ` to ` compressed.txt.bz2 `, decompress it with ` bsip2 -d compressed.txt.bz2 ` which gives us  another gzip file so we repeart the decompr
![[Screenshot 2025-09-16 at 8.00.33 PM.png]]
Password: FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn