## Basic Bash Scripting
Borrowed from [ryanstutorials.net/bash-scripting-tutorial](http://ryanstutorials.net/bash-scripting-tutorial/bash-script.php).

#### What is a Bash script
A Bash script is a plain text file which contains a series of commands. These commands are a mixture of commands we would normally type ourselves on the command line (such as ls or cp for example) and commands we could type on the command line but generally wouldn't (you'll discover these over the next few pages). An important point to remember though is:

**Anything you can run normally on the command line can be put into a script and it will do exactly the same thing. Similarly, anything you can put into a script can also be run normally on the command line and it will do exactly the same thing.**

#### What do they look like
A most basic bash-script **myscript.sh**
```
#!/bin/bash

# this is a comment
echo Hello World!
```

#### How do we run them
Running a Bash script is fairly easy. Another term you may come across is executing the script (which means the same thing). Before we can execute a script it must have the execute permission set (for safety reasons this permission is generally not set by default). If you forget to grant this permission before running the script you'll just get an error message telling you as such and no harm will be done.

```
user@bash: ./myscript.sh
bash: ./myscript.sh: Permission denied
user@bash: ls -l myscript.sh
-rw-r--r-- 18 user users 4096 Feb 17 09:12 myscript.sh
user@bash: chmod 755 myscript.sh
user@bash: ls -l myscript.sh
-rwxr-xr-x 18 user users 4096 Feb 17 09:12 myscript.sh
user@bash: ./myscript.sh
Hello World!
user@bash:
```
The shorthand 755 is often used for scripts as it allows you the owner to write or modify the script and for everyone to execute the script.

#### Variables
* Command line arguments: $1, $2, ...
The first, second, etc command line arguments to the script.
```
#!/bin/bash
# A simple copy script
cp $1 $2
```
When executed, will take in two parameters and will copy file1 one to directory2:
```
user@bash: ./myscript.sh file1 directory2
```
* Assign values to variables: variable=value
To set a value for a variable. Remember, no spaces on either side of =
```
#!/bin/bash
# A simple variable example
myvariable=Hello
anothervar=Fred
echo $myvariable $anothervar
```
When executed:
```
user@bash: ./myscript.sh
Hello Fred
```
* Command substitution: variable=$( command )
Command substitution allows us to take the output of a command or program (what would normally be printed to the screen) and save it as the value of a variable. To do this we place it within brackets, preceded by a $ sign.
```
user@bash: myvar=$( ls /etc | wc -l )
user@bash: echo There are $myvar entries in the directory /etc
```
* Exporting Variables
If we want the variable to be available to the second script then we need to export the variable.
**script1.sh**
```
#!/bin/bash
# demonstrate variable scope 1.
var1=blah
var2=foo
# Let's verify their current value
echo $0 :: var1 : $var1, var2 : $var2
export var1
./script2.sh
# Let's see what they are now
echo $0 :: var1 : $var1, var2 : $var2
```
**script2.sh**
```
#!/bin/bash
# demonstrate variable scope 2
# Let's verify their current value
echo $0 :: var1 : $var1, var2 : $var2
# Let's change their values
var1=flop
var2=bleh
```
When executed:
```
user@bash: ./script1.sh
script1.sh :: var1 : blah, var2 : foo
script2.sh :: var1 : blah, var2 :
script1.sh :: var1 : blah, var2 : foo
user@bash:
```
* Ask for user input with ```read```
```
#!/bin/bash
# Ask the user for their name
echo Hello, who am I talking to?
read varname
echo It\'s nice to meet you $varname
```
When executed:
```
user@bash: ./introduction.sh
Hello, who am I talking to?
Ryan
It's nice to meet you Ryan
user@bash:
```

* If Else Statements
```
#!/bin/bash
# Basic if statement
if [ $1 -gt 100 ]
then
    echo Hey that's a large number.
    pwd
fi
date
```
When executed:
```
user@bash: ./if_example.sh 15
Tue 29 Nov 17:58:20 2016
user@bash: ./if_example.sh 150
Hey that's a large number.
/home/user/bin
Tue 29 Nov 17:58:20 2016
user@bash:
```

### Bash scripting tutorials
[https://linuxconfig.org/bash-scripting-tutorial](https://linuxconfig.org/bash-scripting-tutorial)
[Advanced Bash-Scripting Guide](http://www.tldp.org/LDP/abs/html/)
