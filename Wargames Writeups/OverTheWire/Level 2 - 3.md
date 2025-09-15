The password for the next level is stored in a file called `--spaces in this filename--` located in the home directory

Tried doing `cat '--spaces in this filename--'` but that did not work and gave `cat: unrecognized option '--spaces in this filename--'`. Cat is parsing the entirety of '--spaces in this filename--' and thinks the "--" are arguments, we need to use the method from [[Level 1 - 2]] and use the full path `cat ./'--spaces in this filename--'` or `cat -- '--spaces in this filename--'` to tell the program to stop parsing. Using `cat < '--spaces in this filename--'` like in the previous challenge works as well.

Password: MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx