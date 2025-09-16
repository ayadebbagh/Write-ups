"The password for the next level is stored in the file **data.txt**, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work."

First, let's understand what a hex dump is:
A hex dump is a textual display of binary data (from a file, memory, or storage device) presented in hexadecimal, a base-16 number system.

![[Screenshot 2025-09-16 at 1.11.40 PM.png]]

After making a directory in `/tmp` and copying `data.txt`, we can use `xxd -r data2.txt data3.txt` to reverse the hexdump and have the compressed data in `data3.txt`. To get more information on the file, we can use `file data3.txt` which tells us that it is a gzip compressed data file. When doing `gzip -d data3.txt`, we get a `gzip: data3.txt: unknown suffix -- ignored`. This tells me that the file needs to have the right extension to be able to use gzip
We rename `data3.txt` to `data3.txt.gz` and unzip it. We get a `data3.txt` file back. We can use `file` again to get more information, and we learn that this new file is a bzip2 compressed data file.


Password: FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn