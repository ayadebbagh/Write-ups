"The password for the next level is stored in a file called **-** located in the home directory"

Using 1 `cat -` does not work. Using - as an argument refers to STDIN/STDOUt. We can use the full pathname like `cat ./-` or `cat < -`

Password: 263JGJPfgU6LtdEvgfWU1XP5yac29mFx
