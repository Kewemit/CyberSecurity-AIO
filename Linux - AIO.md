## Basic commands
### Tab Key (Autofill)
If you start typing the beginning of a **command, file, directory, etc** and hit the ``Tab`` key, it will **autocomplete** based on what it finds in the directory you are searching as long as you don’t have any other files that start with those letters.

For example if you were trying to run the command ``chrome``, you can type ``chr`` **and** press ``Tab`` and it will **autocomplete** ``chrome``.
### echo
Let’s start with a simple command, ``echo``. The ``echo`` command just **prints out the text arguments to the display**.

**Input:**
```bash
$ echo Hello World
```
**Output:**
![[Pasted image 20241223182635.png]]
> [!note]
> Remember to not include the `$` symbol.
### clear 
This command will clear the display of the terminal.
**Input:**
```bash
$ clear
```
### date
The `date` command simply outputs the current date and time.
**Input:**
```bash
$ date
```
**Output:**
![[Pasted image 20241223183103.png]]
### whoami
### Navigating
`whoami` displays the current user within the terminal and outputs it in a similar way to echo.
**Input:**
```bash
$ whoami
```
**Output:**
![[Pasted image 20241223183451.png]] 
### pwd (Print Working Directory)
**Everything in Linux is a file**, for now keep that in mind. Every file is organized in a **hierarchical directory tree**. 

The first directory in the filesystem is aptly named the **root** directory. The **root** directory has many folders and files which you can store more folders and files, etc. Here is an example of what the directory tree looks like:
```
/
|-- bin
|   |-- file1
|   |-- file2
|-- etc
|   |-- file3
|   `-- directory1
|       |-- file4
|       `-- file5
|-- home
|-- var
```
The location of these files and directories are referred to as **paths**. If you had a folder named ``home`` with a folder inside of it named ``pete`` and another folder in that folder called ``Movies``, that path would look like this: ``/home/pete/Movies``.
	
The ``pwd`` command  shows you **which directory you are in**. It shows the location starting from the root directory. 
**Input:**
```bash
$ pwd
```
**Output:**
![[Pasted image 20241223184714.png]]
### cd (Change Directory)
In Linux, we’ll need to navigate our way using paths. There are two different ways to specify a path, with absolute and relative paths.
- **Absolute path**: This is the path from the root directory. The root directory is commonly shown as a slash. Every time your path starts with ``/`` it means you are **starting from the root** directory. For example, ``/home/pete/Desktop``.
- **Relative path**: This is the path from **where you are currently in filesystem**. If I was in location ``/home/pete/Documents`` and wanted to get to a directory inside ``Documents`` called ``taxes``, I don’t have to specify the whole path from root like ``/home/pete/Documents/taxes``, I can just go to ``taxes/`` instead.

Now we just need something to help us change to the directory we want to. Luckily, we have ``cd``.
```bash
$ cd /home/kewemit/Pictures
```
So now I've changed my directory location to ``/home/pete/Pictures``.

Now inside this directory, I have a folder inside called ``Hawaii``, I can navigate to that folder with:
```bash
$ cd Hawaii
```
Notice how I just used the name of the folder? It’s because I was already in ``/home/pete/Pictures``.

It can get pretty tiring navigating with absolute and relative paths all the time, luckily there are some shortcuts to help you out:
- . (current directory). This is the directory you are currently in.
- .. (parent directory). Takes you to the directory above your current.
- ~ (home directory). This directory defaults to your “home directory”. Such as /home/pete.
- - (previous directory). This will take you to the previous directory you were just at.
-   (without any flags). Returns the user to the home directory.
```bash
$ cd .
  
$ cd ..
  
$ cd ~
  
$ cd -

$ cd
```
### ls (List Directories)
The ``ls`` command will **list directories and files** in the **current** directory by default, however you can specify which path you want to list the directories of.
```bash
$ ls
# Or
$ ls /home/kewemit
```

Also note that **not** all files in a directory will be visible. Filenames that start with ``.`` are **hidden**, you can view them however with the ``ls`` command and pass the ``-a`` flag to it (**a for all**).
```bash
$ ls -a
```

There is also one more useful ls flag, ``-l`` for **long**. This will show you **detailed information**, starting from the left: ``file permissions``, ``number of links``, ``owner name``, ``owner group``, ``file size``, ``timestamp of last modification``, and ``file/directory name``:
```bash
$ ls -l
```

```bash
kewemit@cheatsheet:~$ ls -l
  
total 80
drwxr-x--- 7 kewemit mygroup   4096 Nov 20 16:37 Desktop
drwxr-x--- 2 kewemit mygroup   4096 Oct 19 10:46  Documents
drwxr-x--- 4 kewemit mygroup   4096 Nov 20 09:30 Downloads
drwxr-x--- 2 kewemit mygroup   4096 Oct  7 13:13   Music
drwxr-x--- 2 kewemit mygroup   4096 Sep 21 14:02 Pictures
drwxr-x--- 2 kewemit mygroup   4096 Jul 27 12:41   Public
drwxr-x--- 2 kewemit mygroup   4096 Jul 27 12:41   Templates
drwxr-x--- 2 kewemit mygroup   4096 Jul 27 12:41   Videos
```
Commands have things called **flags** (or arguments or options, whatever you want to call it) to add more functionality. See how we added ``-a`` and ``-l``,  you can add them both together with ``-la``. 

Most of the time the flag order doesn’t really matter so you can also do ``ls -al`` and it would still work. It's just mostly up to your preference.
```bash
$ ls -la
```

```bash
kewemit@cheatsheet:~$ ls -la
  
total 80
drwxr-xr-x 2 kewemit mygroup   4096 Dec 23 21:53 .
drwxrwxrwt 7 kewemit mygroup   1056768 Dec 23 23:28 ..
drwxr-x--- 7 kewemit mygroup   4096 Nov 20 16:37 .YouCanSeeMyHiddenFile
drwxr-x--- 7 kewemit mygroup   4096 Nov 20 16:37 Desktop
drwxr-x--- 2 kewemit mygroup   4096 Oct 19 10:46 Documents
drwxr-x--- 4 kewemit mygroup   4096 Nov 20 09:30 Downloads
drwxr-x--- 2 kewemit mygroup   4096 Oct  7 13:13 Music
drwxr-x--- 2 kewemit mygroup   4096 Sep 21 14:02 Pictures
drwxr-x--- 2 kewemit mygroup   4096 Jul 27 12:41 Public
drwxr-x--- 2 kewemit mygroup   4096 Jul 27 12:41 Templates
drwxr-x--- 2 kewemit mygroup   4096 Jul 27 12:41 Videos
```
### touch (Create Files)
A very simple way to create files is to use the ``touch`` command. ``Touch`` allows you to the create new empty files.
```bash
$ touch myfilename
```
If we look with `ls -l`:
```bash
total 4
-rw-r--r-- 1 kewemit mygroup  0 Dec 23 23:34 myfilename
```

``Touch`` is also used to change **timestamps** on existing files and directories.  Do an ``ls -l`` on a file and note the timestamp, then touch that file and it will update the timestamp.

There are many other ways to create files that involve other things like **redirection** and **text editors**.
### file (See Filetype)
Before when we used the ``touch`` command, did you notice that the **filename** didn’t conform to standard naming like in **Windows**? Normally you would expect a file called ``banana.jpeg`` and expect a ``.JPEG`` picture file.

In **Linux**, filenames aren’t required to represent the contents of the file. You can create a file called ``funny.gif`` that isn’t actually a **GIF**.

To find out what kind of file a file is, you can use the ``file`` command. It will show you a description of the file’s contents:
```bash
$ file mydirectory
```
If this was a directory, it would output:
```bash
mydirectory: directory
```
### cat (read file contents)
A simple command for reading contents is the ``cat`` command, short for **concatenate**, it not only displays file contents but it can **combine multiple files** and show you the output of them.
```bash
# To read two files
$ cat myfile1 myfile2

# To read one file
cat myfile1
```
``cat`` is not great for viewing large files and it’s only meant for short content. 
### less (read large file contents)
If you are viewing text files **larger** than a simple output, ``less`` is more. The text is displayed in a paged manner, so you can navigate through a text file page by page.

Go ahead and look at the contents of a file with ``less``. Once you’re in the ``less`` command, you can actually use other keyboard commands to navigate in the file.
```bash
$ less /home/kewemit/Documents/text1
```
Navigate through ``less``:
- ``q`` - Used to quit out of less and go back to your shell.
- ``Page up``, ``Page down``, ``Up`` and ``Down`` - Navigate using the arrow keys and page keys.
- ``g`` - Moves to beginning of the text file.
- ``G`` - Moves to the end of the text file.
- ``/search`` - You can search for specific text inside the text document. Prefacing the words you want to search with ``/``
- ``h`` - If you need help about how to use ``less`` while you’re in ``less``, use help.
### history (See previously ran commands)
You can see the **history** of the commands you've written. This is quite useful when you want to **find and run a command you used previously** without actually typing it again.
```bash
$ history
```
Want to run the same command you did before, just hit the up arrow. Want to run the previous command without typing it again? Use ``!!``.
If you typed ``cat file1`` and want to run it again, you can actually just go ``!!`` and it will run the last command you ran.

Another **history** shortcut is ``ctrl-R``, this is the **reverse search command**, if you hit ``ctrl-R`` and you start typing parts of the command you want, it will show you matches and you can just navigate through them by hitting the ``ctrl-R`` key again. Once you found the command you want to use again, just hit ``Enter``.

### cp (Copy)
Much like **copy and pasting** files in other operating systems, the **shell** gives us an even simpler way of doing that:
```bash
$ cp myfile /home/kewemit/Documents/cooldocs
```
``mycoolfile`` is the file you want to **copy** and ``/home/pete/Documents/cooldocs`` is where you are copying the file to. This would result in the file being in:
```bash
/home/kewemit/Documents/cooldocs/myfile
```

You can copy **multiple** files and directories as well as use **wildcards** (`*`). You can use wildcards in every command for more flexibility.
- ``*`` the wildcard of wildcards, it's used to represent all single characters or any string.
- ``?`` used to represent one character
- ``[]`` used to represent any character within the brackets
```bash
$ cp *.jpg /home/pete/Pictures
```
This will copy all files with the ``.jpg`` extension in your current directory to the ``Pictures`` directory.

A useful command is to use the ``-r`` flag, this will **recursively** copy directories **and** their contents, including subdirectories and files. Without the `-r` flag, `cp` will **not** copy directories and will throw an error if you attempt to copy one.

Try to do a ``cp`` on a **directory** that contains a couple of files to your ``Documents`` directory. Didn’t work did it? Well that’s because you’ll need to copy over the files and directories inside as well with ``-r`` command.
```bash
$ cp -r MyDirectory/ /home/kewemit/Documents
```

One thing to note, if you copy a file over to a directory that has the same filename, **the file will be overwritten with whatever you are copying over**. This is no bueno if you have a file that you don’t want to get accidentally overwritten. You can use the ``-i`` flag (**interactive**) to prompt you before overwriting a file.
```bash
$ cp -i myfile /home/kewemit/Pictures
```
### mv (Move)
Used for **moving files** and also **renaming** them. 

You can **rename files** like this:
```bash
$ mv oldfile newfile
```

**Move** a file to a different directory:
```bash
$ mv file2 /home/pete/Documents
```

And **move more than one** file:
```bash
$ mv file_1 file_2 /somedirectory
```

You can **rename directories** as well:
```bash
$ mv directory1 directory2
```

Like ``cp``, if you ``mv`` a file or directory **it will overwrite anything in the same directory**. So you can use the ``-i`` flag to prompt you before overwriting anything.
```bash
mv -i directory1 directory2
```

Let’s say you did want to ``mv`` a file to overwrite the previous one. You can also make a **backup** of that file and it will just rename the old version with a ``~``.
```bash
$ mv -b directory1 directory2
```

### mkdir (Make Directory)
The ``mkdir`` command (**Make Directory**) will **create a directory** if it doesn’t already exist. You can even make **multiple directories** at the same time.
```bash
$ mkdir mydirectory1 mydirectory2
```
This would make 2 folders with the names above

You can also create subdirectories at the same time with the ``-p`` (parent flag).
```bash
$ mkdir -p mymaindir1/mysubdir/mysubsubdir
```
### rm (Remove)
To remove files you can use the ``rm`` command. The ``rm`` (remove) command is used to delete files and directories.
```bash
$ rm file1
```
Take caution when using ``rm``, once you remove a file, they are gone for good, so be careful.
Fortunately there are some safety measures put into place. **Write-protected files** will prompt you for confirmation. If a directory is write-protected it will also not be easily removed.

Now if you don’t care about that, you can remove a bunch of files:
```bash
$ rm -f file1
```
``-f`` or **force** option tells ``rm`` to remove all files, whether they are write protected or not, without prompting the user.

```bash
$ rm -i file
```
Adding the ``-i`` flag, will give you a **prompt** on if you want to remove the files or directories.

```bash
$ rm -r directory
```
You can’t just ``rm`` a directory by default, you’ll need to add the ``-r`` flag (**recursive**) to remove all the files and any subdirectories it may have.
You can also remove a directory with the ``rmdir`` command.
```bash
$ rmdir directory
```
### find (find files)
We can find any file from our system with the ``find`` command:
```bash
$ find /home -name puppies.jpg
```
With ``find`` you’ll have to specify the directory where the file you’re searching for is, in this case we are trying to find a file by the name of ``puppies.jpg``.

You can specify what type of file you are trying to find.
```bash
$ find /home -type d -name MyFolder
```
You can see that I set the **type** of file I’m trying to find as (``d``) for ``directory`` and I’m still searching by the name of ``MyFolder``.

### help (Get options for commands)
Linux has some great built-in tools to help you how to use a command or check what flags are available for a command. One tool, ``help``, is a built-in bash command that provides **help** for other bash commands (``echo``, ``logout``, ``pwd``, etc).

This will give you a **description** and the **options** you can use for ``echo``.
```bash
$ help logout
```
This would output:
```bash
logout: logout [n]
    Exit a login shell.
    
    Exits a login shell with exit status N.  Returns an error if not executed
    in a login shell.
```

For other executable programs, it’s convention to have an option called ``--help``.
```bash
$ logout --help
```
Note: Not all developers who ship out executables will conform to this standard

### man (Manual)
You can see the **manuals** for a command with the ``man`` command:
```bash
$ man ls
```
Man pages are manuals. They provide **documentation** about commands and other aspects of the system.

### whatis (See what command does)
The ``whatis`` command provides a brief description of command line programs:
```bash
$ whatis cat
```
The description gets sourced from the **manual page** of each command. If you ran ``whatis cat``, you’d see there is a small blurb with a short description.
### alias (Shorten commands)
Sometimes typing commands can get really repetitive, or if you need to type a long command many times, it’s best to have an ``alias`` you can use for that. To create an ``alias`` for a command you simply specify an **alias name** and set it to the command:
```bash
$ alias foobar='ls -la'
```
Now instead of typing ``ls -la``, you can type ``foobar`` and it will execute that command. Keep in mind that **this command won't save your alias after reboot**, so you'll need to add a permanent alias in:
```bash
~/.bashrc
```
or similar files if you want to have it persist after reboot.

You can remove aliases with the ``unalias`` command:
```bash
$ unalias foobar
```

### exit (Exit the shell)
To exit from the shell, you can use the ``exit`` command:
```bash
$ exit
```
Or the ``logout`` command:
```bash
$ logout
```




### Basic text navigation & manipulation
### stdout (Standard Out ">")
Our next subject is I/O (input/output) streams. Let's run the following command and we'll discuss how this works:
```bash
$ echo Hello World > peanuts.txt
```
Check the directory where you ran that command and you should see a file called ``peanuts.txt``, and inside that file is the text "Hello World". 

First let's break down the first part:
```bash
$ echo Hello World
```
We know this prints out "Hello World" to the screen, but how? Processes use ``I/O`` streams to **receive input and return output.** By default the ``echo`` command takes the **input** (**standard input** or **stdin**) from the keyboard and returns the **output** (**standard output** or **stdout**) to the screen. So that's why when you type ``echo Hello World`` in your shell, you get "Hello World" on the screen. However, ``I/O redirection`` allows us to change this behaviour.

The next part of the command:
```bash
>
```
The ``>`` is a **redirection operator** that allows us the change where **standard output** goes. It allows us to **send the output** of ``echo Hello World`` to a file instead of the screen. If the file does **not** already exist it will create it for us. However, if it does exist it will **overwrite** it (Though, you can add a shell flag to prevent this).

And that's basically how **stdout redirection** works!

Well let's say I **didn't** want to overwrite my ``peanuts.txt``, luckily there is a redirection operator ``>>`` for that:
```bash
$ echo Hello World >> peanuts.txt
```
This will append "Hello World" to the end of the ``peanuts.txt`` file, and if the file doesn't exist it will create it.

### stdin (Standard In)
There are also different **standard input** (**stdin**) streams we can use as well. We know that we have **stdin** from devices like the **keyboard**, but we can use files, output from other processes and the terminal as well, let's see an example

Let's use the ``peanuts.txt`` file for this example, currently, it has the text "Hello World" in it:
```bash
$ cat < peanuts.txt > banana.txt
```
Just like we had ``>`` for **stdout redirection**, we can use ``<`` for **stdin redirection**. So in this case the "Hello World" would be inside the ``banana.txt`` file now.
```bash
$ cat banana.txt

Hello World
```

Normally in the ``cat`` command, you send a file to it and that file becomes the **stdin**, in this case, we redirected ``peanuts.txt`` to be our **stdin**. Then the output of ``cat peanuts.txt`` which would be "Hello World" gets redirected to another file called ``banana.txt``.
### stderr (Standard Error)

Let's try to list the contents of a directory that doesn't exist on your system and redirect the output to the ``peanuts.txt`` file:
```bash
$ ls /fake/directory > peanuts.txt
```
What should be returned is:
```bash
ls: cannot access /fake/directory: No such file or directory
```
Now you're probably thinking, shouldn't that message have been sent to the file? There is actually another ``I/O`` stream in play here called **standard error** (**stderr**). By default, ``stderr`` sends its output to the screen as well, it's a completely different stream than **stdout**. So you'll need to redirect its output a different way.

Unfortunately the redirector is not as nice as using ``<`` or ``>`` but it's pretty close. We will have to use _file descriptors_. A **file descriptor** is a **non-negative number** that is used to access a file or stream. We will go in depth about this later, but for now know that the file descriptor for **stdin**, **stdout** and **stderr** is ``0``, ``1``, and ``2``.

So now if we want to redirect our **stderr** to the file we can do this:
```bash
$ ls /fake/directory 2> peanuts.txt
```
Now, you should see the **stderr** messages in ``peanuts.txt``.

Now what if I wanted to see both **stderr** and **stdout** in the ``peanuts.txt`` file? It's possible to do this with **file descriptors** as well:
```bash
$ ls /fake/directory > peanuts.txt 2>&1
```
This sends the results of ``ls /fake/directory`` to the ``peanuts.txt`` file and then it redirects **stderr** to the **stdout** via ``2>&1``. The order of operations here matters, ``2>&1`` sends **stderr** to whatever **stdout** is pointing to. In this case **stdout** is pointing to a file, so ``2>&1`` also sends **stderr** to a file. So if you open up the ``peanuts.txt`` file you should see both **stderr** and **stdout**. Though, in our case, the above command only outputs **stderr**.

There is a shorter way to redirect both **stdout** and **stderr** to a file:
```bash
$ ls /fake/directory &> peanuts.txt
```
Now what if I **don't** want any of that cruft and want to get rid of **stderr** messages completely? Well you can also redirect output to a special file call ``/dev/null`` and it will discard any input:
```bash
$ ls /fake/directory 2> /dev/null
```

### pipe and tee (Multiple commands)
Let's get into some plumbing now. Let's try a command:
```bash
$ ls -la /etc
```
This should return something like this:
```bash
total 1350
drwxr-xr-x  1 root     root      4096 Dec 24 00:09 .
drwxr-xr-x  1 root     root      4096 Nov 22 19:38 ..
-rw-r--r--  1 root     root      3386 Jun 27  2023 adduser.conf
-rw-r--r--  1 root     root        16 Aug 18 14:51 adjtime
drwxr-xr-x  1 root     root      4096 Aug 18 14:51 alsa
drwxr-xr-x  1 root     root     20480 Nov 29 15:11 alternatives
drwxr-xr-x  1 root     root      4096 Nov  8 17:12 apache2
...SNIP....
```
As we can see above, it's a long list of items. Instead of **redirecting** this output, wouldn't it be nicer if we could see the output in another command like ``less``:
```
$ ls -la /etc | less
```
The pipe operator ``|``, allows us to get the **stdout** of a command and make that the **stdin** to another process. We took the **stdout** of ``ls -la /etc`` and then _piped_ it to the ``less`` command. 

If you wanted to write the **output** of the command to two different streams? That's possible with the ``tee`` command:
```bash
ls | tee peanuts.txt
```
You should see the output of ``ls`` on the terminal, and the same info will be stored on the peanuts.txt file.
### env (Environment)
Run the following command:
```bash
$ echo $HOME
```
You should see the **path** to your **home** directory(i.e. ``/home/kewemit``).

What about this command:
```bash
$ echo $USER
```
You should now see your username (i.e. ``kewemit``)

This info is coming from your **environment variables**. You can view these by typing:
```bash
$ env
```
This outputs a whole lot of information about the **environment variables** you currently have set. These **variables** contain useful information that the **shell** and **other processes** can use.
Below is a short example:
```bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/bin  
PWD=/home/user
USER=kewemit
```

One important variable is ``PATH``. You can access these variables by sticking a ``$`` in front of the variable name:
```bash
$ echo $PATH
  
/usr/local/sbin:/usr/local/bin:/usr/sbin:/bin
```
This returns a list of paths separated by a colon (``:``) that your system searches when it runs a command. 
Let's say you **manually download** and install a package and put it in to a **non standard directory** and want to run that command, you type ``$ newcommand`` and the prompt says "**command not found**". What is happening is that ``$PATH`` variable doesn't check that directory for this **binary** so it's throwing an error.

Let's say you had tons of **binaries** you wanted to run out of that directory, you can just modify the ``PATH`` variable to include that directory in your ``PATH`` environment variable.
### cut (Cut Text)
Let's create a file that we'll be working with. Copy and paste the following command, once you do that add a ``TAB`` in between **lazy** and **dog** (hold down ``Ctrl-v + TAB``):
```bash
$ echo 'The quick brown; fox jumps over the lazy  dog' > sample.txt
```

First command we'll be learning about is the ``cut`` command. It **extracts portions of text from a file**.
To extract contents **by a list of characters**:
```bash
$ cut -c 5 sample.txt
```
This outputs the **5th character in each line** of the file. In this case it is "``q``", **note that the space also counts as a character**. 

To extract the contents **by a field** (_everything separated by a TAB_):
```bash
$ cut -f 2 sample.txt
```
The ``-f`` or **field flag** cuts text based off of **fields**, by default it uses **TABs** as delimiters, so everything separated by a **TAB** is considered a **field**. You should see "``dog``" as your output.

You can combine the **field flag** with the **delimiter flag** to extract the contents by a custom delimiter:
```bash
$ cut -f 1 -d ";" sample.txt
```
This will change the **TAB** delimiter to a "``;``" delimiter and since we are cutting the first field, the result should be "``The quick brown``".
### paste (Merge Lines Together)
The ``paste`` command is similar to the ``cat`` command, it merges lines together in a file. Let's make a new file with the following contents:
```bash
sample2.txt
The
quick
brown
fox
```

Let's combine all these lines into one line: 
```bash
$ paste -s sample2.txt

sample2.txt     The     quick   brown   fox
```
The default delimiter for ``paste`` is **TAB**, so now there is one line with **TABs** separating each word.
Let's change this delimiter (``-d``) to something a little more readable:
```bash
$ paste -d ' ' -s sample2.txt

sample2.txt The quick brown fox
```
Now everything is on one line delimited by spaces like above.
### head (First 10 Lines)
 Run the command ``cat /var/log/syslog``. You should see **pages upon pages** of text. We can use head to see just a few lines. The ``head`` command will show you the **first 10 lines** in a file.
```bash
$ head /var/log/syslog
```

If you wanted to see the first **15** lines, you can do this:
```bash
$ head -n 15 /var/log/syslog
```
The ``-n`` flag stands for **number of lines**.
### tail (Last 10 Lines)
The ``tail`` command lets you see the last 10 lines of a file:
```bash
$ tail /var/log/syslog
```

You can change the **number of lines** you want to see:
```bash
$ tail -n 10 /var/log/syslog
```

Another great option you can use is the ``-f`` (**follow**) flag, this will follow the file as it grows:
```bash
$ tail -f /var/log/syslog
```
Your **syslog** file will be continually changing while you interact with your system and using ``tail -f ``you can see everything that is getting added to that file.
### expand and unexpand (Change TAB to Space)
Normally **TABs** would usually show a noticeable difference but some text files don't show that well enough. Having **TABs** in a text file **may not be the desired spacing** you want. To change your **TABs** to **spaces**, use the ``expand`` command:
```bash
$ expand sample.txt
```
The command above will print output with each TAB converted into a group of spaces. To save this output in a file, use output redirection like below:
```bash
$ expand sample.txt > result.txt
```

Opposite to **expand**, we can convert back each group of **spaces** to a **TAB** with the ``unexpand`` command:
```bash
$ unexpand -a result.txt
```
### join and split
The join command allows you to **join** multiple files together by a common field:
Let's say I had two files that I wanted to **join** together:
```bash
file1.txt
1 John
2 Jane
3 Mary

file2.txt
1 Doe
2 Doe
3 Sue
```
I could then use **Join** like so:
```bash
$ join file1.txt file2.txt
  
1 John Doe
2 Jane Doe
3 Mary Sue
```
The files are joined together by the **first field** by default and the **fields have to be identical**, if they are not you can **sort** them, so in this case the files are joined via **1, 2, 3**.

How would we join the following files?
```bash
file1.txt
John 1
Jane 2
Mary 3

file2.txt
1 Doe
2 Doe
3 Sue
```

To **join** this file you need to **specify which fields** you are joining, in this case we want **field 2** on ``file1.txt`` and **field 1** on ``file2.txt``. (In this case the ``file1.txt``  has the numbers on the **second filed**), so the command would look like this:
```bash
$ join -1 2 -2 1 file1.txt file2.txt
  
1 John Doe
2 Jane Doe
3 Mary Sue
```
Here, ``-1`` refers to ``file1.txt`` and ``-2`` refers to ``file2.txt``. Pretty neat. You can also ``split`` a file up into different files with the ``split`` command:
```bash
$ split myfile
```
This will **split** it into different files, by default it will split them once they reach a 1000 line limit. The files are named ``x**``.
### sort
The ``sort`` command is useful for sorting lines.

Imagine we have a file like this:
```bash
file1.txt
  
dog
cow
cat
elephant
bird
```
We can then sort it in alphabetical order via:
```bash
$ sort file1.txt
  
bird
cat
cow
dog
elephant
```

You can also do a **reverse sort**:
```bash
$ sort -r file1.txt
  
elephant
dog
cow
cat
bird
```

Or also sort via **numerical value**:
```bash
$ sort -n file1.txt
  
1
2
3
```
### tr (Translate)
The ``tr`` (**translate**) command allows you to translate a set of characters into another set of characters.

Let's try an example of translating **all lower case characters to uppercase** characters:
```bash
$ tr a-z A-Z

hello
HELLO
```
As we can see we made the ranges of ``a-z`` into ``A-Z`` and all text got uppercased.

### uniq (Unique / Delete duplicates)
The ``uniq`` (**unique**) command is another useful tool for parsing text.

Let's say you had a file with lots of **duplicates**:
```bash
reading.txt

book
book
paper
paper
article
article
magazine
```
And you wanted to **remove the duplicates**, well you can use the ``uniq`` command:
```bash
$ uniq reading.txt
  
book
paper
article
magazine
```

Let's get the count of how many occurrences of a line:
```bash
$ uniq -c reading.txt
# Here the numbers signify the amount of that word in the file.
2 book
2 paper
2 article
1 magazine
```

Let's just get unique values (Value that appears only once):
```bash
$ uniq -u reading.txt

magazine
```

Let's just get duplicate values (Values that appear more than once):
```bash
$ uniq -d reading.txt

book
paper
article
```
> [!info] **Note** : ``uniq`` does not detect duplicate lines unless they are adjacent.

**For example:**
Let's say you had a file with duplicates which are **not adjacent**:
```bash
reading.txt
  
book --
paper | # As you can see they aren't next to each other
book --
paper
article
magazine
article
```

```bash
$ uniq reading.txt

book --
paper | # As you can see they aren't next to each other
book --
paper
article
magazine
article
```
See, how the result is the same as the original file? To overcome this, we can use ``sort`` in combination with ``uniq``:
```bash
$ sort reading.txt | uniq
  
article
book
magazine
paper
```



### wc and nl (Word Count) 
The ``wc`` (**word count**) command shows the **total count of words** in a file:
```bash
$ wc /etc/passwd
  
 96     265    5925 /etc/passwd
```
It display the ``number of lines``, ``number of words`` and ``number of bytes``.

To just see just the count of a certain field, use the ``-l`` (lines), ``-w`` (words), or ``-c`` (bytes) respectively:
```bash
wc -l /etc/passwd
  
96
```

Another command you can use to check the count of lines on a file is the ``nl`` (**number lines**) command:
Imagine we have a file like this:
```bash
file1.txt
  
i
like
turtles
```
We can then run the ``nl`` command like:
```bash
$ nl file1.txt

1. i
2. like
3. turtles
```



### grep
The ``grep`` command allows you to search files for characters **that match a certain pattern**. If you wanted to know **whether a file existed in a certain directory** or **if a string was found in a file**, you could use ``grep``.

Let's use a ``sample.txt`` file as an example:
First run this:
```bash
$ echo 'The quick brown; fox jumps over the lazy dog' > sample.txt
```
Then you can do the ``grep`` command:
```bash
$ grep fox sample.txt

The quick brown; <fox> jumps over the lazy dog
```
You should see that ``grep`` found **fox** in the ``sample.txt`` file.

You can also ``grep`` patterns that are case insensitive with the ``-i`` flag:
```bash
$ grep -i somepattern somefile

# So for example
$ grep -i Fox sample.txt

The quick brown; <fox> jumps over the lazy dog
- - - - -
# Whereas
$ grep Fox sample.txt

# Returns nothing
```

To get even more flexible with grep you can combine it with other commands with ``|`` (For more info see: [[#pipe and tee (Multiple commands)]]).
```bash
$ env | grep -i User

# Would return something like
USER=kewemit
XDG_RUNTIME_DIR=/run/user/1000
XDG_SESSION_CLASS=user
```

You can even use **regular expressions** in your pattern:
```bash
$ ls /somedir | grep '.txt$' 
# The $ here signifies that the .txt must be at the end of the file

# Should return all files ending with .txt in somedir.
```

## Vim & Emacs
### regex (Regular Expressions)
Regular expressions are a powerful tool to do pattern based selection. It uses special notations similar to those we've encountered already such as the ``*`` wildcard. Though here we'll go through a couple of the **most common** regex. If you've forgotten, you can also combine regex with ``grep`` like: ``grep [regular expression here] [file]``



Well use this phrase as our test string for the first 2 in the table:
```bash
sally sells seashells 
by the seashore
```
Here's a simple table:

| Symbol | Purpose                          | Example     | Matches                                                  |
| ------ | -------------------------------- | ----------- | -------------------------------------------------------- |
| ``^``  | Matches the **start of a line**  | `^by`       | `by the seashore` (line starts with `by`)                |
| ``$``  | Matches the **end of a line**    | `seashore$` | `by the seashore` (line ends with `seashore`)            |
| ``.``  | Matches **any single character** | `b.`        | `by`, `ba`, `b1` (any two-char string starting with `b`) |
| `[]`   | Matches **one character in set** | `d[iou]g`   | `dig`, `dog`, `dug` (matches `i`, `o`, or `u`)           |
#### 1. Beginning of a line with ``^``
The ``^`` symbol matches the **start of the line**.
So below it will match lines where the word `by` appears at the very beginning of the line:
```bash
^by
would match the line "by the seashore"
```
#### 2. End of a line with ``$``
The ``$`` sign matches the **end of the line**. 
So below it will match **lines** where the word `seashore` appears at the very end of the line:
```
```bash
seashore$
would match the line "by the seashore"
```
#### 3. Matching any single character with ``.``
The **dot (`.`)** matches **any single character** except for a newline (`\n`).
So below it will match any **two-character string** that starts with `b` and has one more character (like `by`, `ba`, `b1`, etc.).
```bash
#If the input was
b.
# And the file had these:
by 
bat 
bag
boo
```
Then, because each match has exactly one character after `b`: 
```bash
# Output
by 
ba 
bo
```
#### 4. Bracket notation with ``[]``
This can be a little tricky, **brackets** (``[]``) allow us to specify **characters found within the bracket**:
```bash
d[iou]g

would match: dig, dog, dug 

- dig (because i is in the brackets)
- dog (because o is in the brackets)
- dug (because u is in the brackets)
```

The previous anchor tag ``^`` when used in a **bracket** means **anything except the characters within the bracket**:
```bash
d[^i]g

would return: dug, dog !!BUT NOT dig!!
```

**Brackets** can also use ranges to increase the amount of characters you want to use:
```bash
d[a-d]g

would match patterns like: dag, dbg, dcg and ddg
```

> [!info] Be careful though as brackets are case sensitive
```bash
d[A-C]g
  
would match: dAg, dBg and dCg !!BUT NOT dag, dbg and dcg!!
```

### Vim (Vi Improved)
#### Vim Cheatsheet

| Category           | Command           | Description                                                         |
| ------------------ | ----------------- | ------------------------------------------------------------------- |
| General            | `vim <file>`      | Open a file in Vim.                                                 |
|                    | `:w`              | Save the current file                                               |
|                    | `:q`              | Quit Vim                                                            |
|                    | `:wq`             | Save (write) and Quit                                               |
|                    | `:q!`             | Quit without saving                                                 |
|                    | `:e <file>`       | Open a different file                                               |
|                    | `:help <command>` | Show help for a command                                             |
|                    | `u` or `:u`       | undo the last change                                                |
|                    | `Ctrl + r`        | Redo the undone change.                                             |
|                    | `:set number`     | Show line numbers.                                                  |
|                    | `:set nonumber`   | Hide line numbers.                                                  |
| Navigation         | `h`               | Move left.                                                          |
|                    | `j`               | Move down.                                                          |
|                    | `k`               | Move up.                                                            |
|                    | `l`               | Move right.                                                         |
|                    | `0` (_zero_)      | Move to the beginning of the file.                                  |
|                    | `^`               | Move to the first non-whitespace (space, tab, etc) character        |
|                    | `$`               | Move to the end of the line.                                        |
|                    | `gg`              | Go to the beginning of the file.                                    |
|                    | `G`               | Go to the end of the file.                                          |
|                    | `<number>G`       | Go to the specified line number.                                    |
|                    | `Ctrl + d`        | Scroll down half a screen.                                          |
|                    | `Ctrl + u`        | Scroll up half a screen.                                            |
|                    | `Ctrl + f`        | Scroll **down a full screen**.                                      |
|                    | `Ctrl + b`        | Scroll **up a full screen**.                                        |
|                    | `w`               | Jump to the **start of the next word**.                             |
|                    | `e`               | Jump to the **end of the current/next word**.                       |
|                    | `b`               | Jump to the **start of the previous word**.                         |
|                    | `%`               | Jump to the **matching bracket or parenthesis**.                    |
| **Editing**        | `i`               | Enter ``insert mode`` **before the cursor**.                        |
|                    | `a`               | Enter ``insert mode`` **after the cursor**.                         |
|                    | `o`               | Insert a new line below the current line and enter ``insert mode``. |
|                    | `O`               | Insert a new line above the current line and enter insert mode.     |
|                    | `x`               | Delete the character in the cursor.                                 |
|                    | `X`               | Delete the character before the cursor.                             |
|                    | `dd`              | Delete the current line.                                            |
|                    | `D`               | Delete from the cursor to the end of the line.                      |
|                    | `cw`              | Change the current word (delete and enter insert mode).             |
|                    | `cc`              | Change the entire line.                                             |
|                    | `r<char>`         | Replace the character under the cursor with `<char>`.               |
|                    | `yy`              | Copy (yank) the current line.                                       |
|                    | `p`               | Paste after the cursor.                                             |
|                    | `P`               | Paste before the cursor.                                            |
| **Search**         | `/text`           | Search for `text` **forward** in the file.                          |
|                    | `?text`           | Search for `text` **backward** in the file.                         |
|                    | `n`               |                                                                     |
|                    | `N`               |                                                                     |
|                    | `:%s/old/new/g`   |                                                                     |
|                    | `:%s/old/new/gc`  |                                                                     |
| **Visual Mode**    | `v`               |                                                                     |
|                    | `V`               |                                                                     |
|                    | `Ctrl + v`        | Enter visual block mode to select a block of text.                  |
|                    | `y`               | Yank (copy) the selected text.                                      |
|                    | `d`               | Delete the selected text.                                           |
|                    | `>`               | Indent the selected text to the right.                              |
|                    | `<`               | Indent the selected text to the left.                               |
| **Windows & Tabs** | `:split`          | Split the window horizontally.                                      |
|                    | `:vsplit`         | Split the window vertically.                                        |
|                    | `Ctrl + w + w`    | Switch between windows.                                             |
|                    | `:tabnew`         | Open a new tab.                                                     |
|                    | `:tabn`           | Go to the next tab.                                                 |
|                    | `:tabp`           | Go to the previous tab.                                             |
|                    | `:tabclose`       | Close the current tab.                                              |




#### How To Use 
To use vim just type: ``vim``
####  Search Patterns
In ``vim`` if you want to open a file, just type: ``:e filenamehere``. If you want to browse a directory, then type: ``:Ex directory or empty``.

To search for an expression just type the ``/`` key and then your search result while you are in a ``vim`` session. Once you hit ``enter``, you can press "``n``" to go forward or "``N``" to go backward in your search results.
```vim
My pretty file is very pretty.

in vim: /pretty

will find the "pretty" words in the text file.
```

The ``?`` search command will search the text file backwards, so in the previous example, the last pretty would come up first:
```vim
My pretty file is very pretty.

in vim: ?pretty

will find the "pretty" words in the text file.
```
####  Navigation
Now you may notice, the mouse is nowhere is use here. To navigate a text document in ``vim``, use the following keys:
- ``h`` or the ``left arrow`` - will move you left one character
- ``k`` or the ``up arrow`` - will move you up one line
- ``j`` or the ``down arrow`` - will move you down one line
- ``l`` or the ``right arrow`` - will move you right one character
####  Appending Text
If you tried to **type something** you wouldn't be able to. That's because you are in ``command mode``. This can get pretty confusing especially if you just want to open a file and enter text. The ``
``command mode`` is used for when you enter commands like ``h, j, k, l`` etc. To insert text you'll need to enter ``insert mode`` first:
- ``i`` - insert text before the cursor
- ``O`` - insert text on the previous line
- ``o`` - insert text on the next line
- ``a`` - append text after the cursor
- ``A`` - append text at the end of the line
When you type any of these **insertion modes**, you'll see that ``vim`` has entered ``insert mode`` at the bottom of the shell. To exit ``insert mode`` and go back to ``command mode``, just hit the ``ESC`` key.
####  Editing
Now that we have a couple of lines written, let's **edit** it a bit more and **remove** some cruft.
- ``x`` - used to cut the selected text also used for deleting characters
- ``dd`` - used to delete the current line
- ``y`` - yank or copy whatever is selected
- ``yy`` - yank or copy the current line
- ``p`` - paste the copied text before the cursor
#### Saving and Exiting
Now that we are done editing it's time to **save and quit** out of ``vim``:
- ``:w`` - writes or saves the file
- ``:q`` - quit out of vim
- ``:wq`` - write and then quit
- ``:q!`` - quit out of vim without saving the file
- ``ZZ`` - equivalent of ``:wq``, but one character faster
- ``u`` - undo your last action
- ``Ctrl-r`` - redo your last action
You may think ``ZZ`` is unnecessary , but you'll see that your fingers may tend to lean towards this rather than ``:wq``.


### Emacs
For users who want an **extremely powerful text editor**. You can do all your **code editing**, **file manipulation**, etc all within ``emacs``. 

If you want a powerful editor that is **extremely extensible**, by extensible, I mean you can write up scripts for ``emacs`` that extend its functionality.

To start it, just type: ``emacs``. You should be greeted with the default welcome **buffer**. **Buffers** in emacs is what your text resides in. So if you open up a file, a **buffer** is used to store that file's content.
#### Emacs Manipulate Files
In a lot of ``Emacs`` documentation, you will see the syntax ``C-[letter]``. This just means hit the ``Ctrl-letter``,  but we'll call ``Ctrl`` with ``C``. 

If you see syntax such as ``M-[letter]``, that means use the ``Meta`` key, most commonly the ``Alt`` key.
##### Saving files
```bash
C-x C-s - Save a file

C-x C-w - Save file as

C-x s - Save all
```
The save file options will prompt you if you want to save each file.

##### Opening a file
```bash
C-x C-f
```
This will prompt you to type a **filename** to open. If you do not have a file that already exists, it will create a new file. You can load up a directory as well.
#### Emacs Buffer Navigation
To move around **buffers** (files you're visiting) use the following commands:
##### Switch buffers
```bash
C-x b - switch buffer
  
C-x right arrow - right-cycle through buffer
  
C-x left arrow - left-cycle through buffer
```
##### Close the buffer
```bash
C-x k
```
This will close the file.
##### Split the current buffer
```bash
C-x 2
```
This allows you see multiple buffers on one screen. To move between these buffers use: ``C-x o``
##### Set a single buffer as the current screen
```bash
C-x 1
```

#### Emacs Editing
##### Text Navigation
```bash
C-up arrow : move up one paragraph

C-down arrow: move down one paragraph

C-left arrow: move one word left
  
C-right arrow: move one word right
  
M-> : move to the end of the buffer
```
##### Cutting and Pasting
To ``cut`` (**kill**) or ``paste`` (**yank**) in ``Emacs`` you'll need to be able to select text first. To select text, move your cursor to where you want to cut or paste and hit:
```bash
C-space key
```

then you can use the navigation keys to select the text you want. Now you can do the ``cut`` and ``paste`` like so:
```bash
C-w : cut
  
C-y : yank
```

#### Emacs Exiting and Help
To close out of ``emacs``:
```bash
C-x C-c
```
If you have any open buffers, it will ask you to **save** it before closing out of ``emacs``.

**To get help:**
```bash
C-h C-h 
```

**To undo what you've done:**
```bash
C-x u
```


## User Management
In any operating system, users and groups form the foundation for managing **access and permissions**. Each process operates under the identity of its owner, whether that's **Jane** or **Bob**. File ownership and access are controlled through these permissions, ensuring that users like **Jane** can't access **Bob's documents** without proper authorization.
### Users and Groups
#### Home Directories
Every user is assigned a **home directory** to store their files. This directory is usually located at `/home/username`, though its location can vary.
#### User IDs (UIDs) and Group IDs (GIDs)
Operating systems use **user IDs (UIDs)** to identify each user. While **usernames** provide a human-friendly way to recognize users, the system relies on **UIDs** for identification. 

Similarly, **groups** are collections of users managed by their **group ID (GID)**, allowing for shared permissions across multiple users. Groups simplify permission management by applying **consistent access rules to all members**.
#### System Users and Daemons
In addition to human users, operating systems often include **system users**. These are typically **daemons** (_background processes_), that keep the system running smoothly. These system users don't have login privileges but are essential for tasks like managing network services or scheduled jobs.
#### The Root User and Superuser Access
One critical user in Linux systems is **root**, also known as the **superuser**. The root user has unrestricted access to all files, processes, and system settings. This power makes root indispensable for system administration but also poses significant risks. Operating as root constantly can lead to accidental deletion of critical system files or unintended changes.

To mitigate this risk, users can temporarily execute commands with root privileges using the `sudo` command (short for "``superuser do``"). This allows a **regular user** with proper authorization to perform administrative tasks without being logged in as root.

Go ahead and try to view a protected file like ``/etc/shadow``:
```bash
$ cat /etc/shadow

cat: /etc/shadow: Permission denied
```
Notice how you get a permission denied error. You can look at the permissions with:
```bash
$ ls -la /etc/shadow
#      `-- -l for long and -a for all files
-rw-r----- 1 root shadow 1134 Dec 1 11:45 /etc/shadow
```
We haven't gone through permissions yet, but what's happening here is that **root** is the owner of the file and you'll need **root access** or be part of the shadow group to read the contents.

Now run the command with ``sudo``:
```bash
$ sudo cat /etc/shadow
```
And now you're able to see the contents. This is because you essentially ran the ``cat`` command as **root**. 
### root (su)
We've looked at one way to get superuser access using the ``sudo`` command. You can also run commands as the superuser with the ``su`` command. Here ``su`` means "``substitute users``" and opens a **root** shell if no username is specified. You can use this command to substitute to any user as long as you know the password.

Using this method, it's much easier to make a critical mistakes running everything in root, you also won't have records of the commands you use to change system configurations, etc. **Basically, if you need to run commands as the superuser, just stick to sudo.**

Now you might be asking, **how do you know who has access to do sudo?** 
Well, the system doesn't let your average **Joe** run commands as the superuser, so how does it know?.. There's a ``/etc/sudoers`` file, which lists users who can run ``sudo``. You can edit this file with the **visudo** command (_remember you need_ ``sudo`` _to run_ ``visudo``).
### /etc/passwd (UIDs)
Remember how the system uses **user IDs** (``UIDs``) to identify a user. To find out what users are mapped to what ``ID``, look at the ``/etc/passwd`` file:
```bash
$ cat /etc/passwd
```
This file shows you users and detailed information about them. For example, the first line in this file most likely looks like this:
```bash
root:x:0:0:root:/root:/bin/bash
```
Each line displays user information for one user. There are many fields separated by colons that tell you additional information about the user, here's a breakdown of them all:
1. ``Username`` - In this case it's ``root``

2. ``User's password`` - the password is not really stored in this file, it's usually stored in the ``/etc/shadow`` file. We'll talk about ``/etc/shadow`` in the next section, but know that it contains **encrypted user passwords**. You can see many different symbols that are in this field, if you see an "``x``" that means the password is stored in the ``/etc/shadow`` file, a "``*``" means the user doesn't have login access and if there is a **blank field** that means the user doesn't have a password.

3. ``The user ID`` - as you can see root has the ``UID`` of 0

4. ``The group ID``

5. ``GECOS field`` - This is used to generally leave **comments about the user** or account such as their **real name** or **phone number**, it is comma delimited.

6. ``User's home directory``

7. ``User's shell`` - you'll probably see a lot of user's defaulting to bash for their shell

Here's an example using the above breakdown on a **kali live system**:
```zsh
kali:x:1000:1000:Kali Live user,,,:/home/kali:/usr/bin/zsh

Let us break this down into sections:

kali: -- 1. 'The username'
x: -- 2. 'The password is stored in /etc/shadow'
1000: -- 3. 'The user ID. In this case 1000'
1000: -- 4. 'The group ID. In this case also 1000' 
Kali Live user,,,: -- 5. 'Comment about the user'
/home/kali: -- 6. 'The home directory of the user'
/usr/bin/zsh -- 7. 'The shell of the user'
```

Normally you would expect you see just human users. Remember that users are really only on the system to run processes with different permissions. 
Sometimes we want to run processes with **pre-determined permissions**. For example, the **daemon user** is used for **daemon processes**.

You can edit the ``/etc/passwd`` file if you want to **add users** and **modify information** using the ``vipw`` tool, however things like these are best left to the tools we will discuss in a later lesson such as ``useradd`` and ``userdel``.
### /etc/shadow (Passwords)
The ``/etc/shadow`` file is used to store information about **user authentication**. It requires ``superuser`` read permissions:
```bash
$ sudo cat /etc/shadow

root:MyEPTEa$6Nonsense:15000:0:99999:7:::
```
You'll notice that the contents look very similar to ``/etc/passwd``, however in the password field you'll see an **encrypted password**. The fields are separated by colons as followed:
1. ``Username``
2. ``Encrypted password``
3. ``Date of last password changed`` - expressed as the number of days since ``Jan 1, 1970``. If there is a **0** that means the user should change their password the next time they login
4. ``Minimum password age`` - Days that a user will have to wait before being able to change their password again
5. ``Maximum password age`` - Maximum number of days before a user has to change their password
6. ``Password warning period`` - Number of days before a password is going to expire
7. `` Password inactivity period`` - Number of days after a password has expired to allow login with their password
8. ``Account expiration date`` - date that user will not be able to login
9. ``Reserved field for future use``

In most distros today, user authentication doesn't rely on just the ``/etc/shadow`` file, there are other mechanisms in place such as ``PAM`` (**Pluggable Authentication Modules**) that replace authentication.
### /etc/group (Set group permissions)
Another file that is used in user management is the ``/etc/group`` file. This file allows for **different groups** with **different permissions**:
```bash
$ cat /etc/group

root:*:0:pete
```
The ``/etc/group`` fields are as follows:
1. ``Group name``
2. ``Group password`` - there isn't a need to set a group password, using an elevated privilege like sudo is standard. A "``*``" will be put in place as the default value.
3. ``Group ID`` (**GID**)
4. ``List of users`` - you can manually specify users you want in a specific group
### User Management Tools (useradd/userdel/passwd)
Most **enterprise environments** are using **management systems** to manage users, accounts and passwords. However, on a single machine computer there are useful commands to run to manage users.
#### Adding Users
You can use the ``adduser`` or the ``useradd`` command. 

The ``adduser`` command contains more helpful features such as **making a home directory and more**:
```bash
$ adduser -h  

adduser [--uid id] [--firstuid id] [--lastuid id]
        [--gid id] [--firstgid id] [--lastgid id] [--ingroup group]
        [--add-extra-groups] [--shell shell]
        [--comment comment] [--home dir] [--no-create-home]
        [--allow-all-names] [--allow-bad-names]
        [--disabled-password] [--disabled-login]
        [--conf file] [--quiet] [--verbose] [--debug]
        user
    Add a normal user
...SNIP....
```
There are configuration files for adding new users that can be customized depending on what you want to allocate to a default user.

```bash
$ sudo useradd bob
```
You'll see that the above command creates an entry in ``/etc/passwd`` for **bob**, sets up **default groups** and adds an entry to the ``/etc/shadow`` file.
#### Removing Users
To remove a user, you can use the ``userdel`` command:
```bash
$ sudo userdel bob
```
This does its best to **undo** the file changes by ``useradd``.
#### Changing Password
```bash
$ passwd bob
```
This will allow you to change the password of **yourself**. If you want to change **another users** password you need elevated access.

## TBA LATER
* Organize the commands
* Permissions, Processes, Packages, etc