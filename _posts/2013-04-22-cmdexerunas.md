Title: Run `cmd.exe` as another user
Tags: Windows
Author: Marcelo Moreira
Summary: How to get a command line in windows as another user

Sometimes you need to run command line commands as another user.

I just ran into this the other day, and even thought it is a windows trick, I thought it was worth documenting.

I used it to migrate our AD objects from one domain into another. I used [dsget](http://technet.microsoft.com/en-us/library/cc755162(v=ws.10).aspx) and [dsquery](http://technet.microsoft.com/en-us/library/cc732952(v=ws.10).aspx) extensively for that and needed to issue them several times, and I did not want to type the username and password several times for it.

To accomplish this, just type from another cmd.exe window:

    runas /user:u2 "cmd.exe"
