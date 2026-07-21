**NOTE** >> All passwords for the current bandit users  found in the file 
```
/etc/bandit_pass/banditx
```
- You can only read the password for ur current account , for someone else's account or level u need to have a SUID/setuid binary.

## LEVEL 1 
- Only challenge was how to read a file named '-', solution was
```
cat ./-
```
* * *
## LEVEL 2 
**DASHED FILENAMES**
- In linux , (-) is used to identify an option or a flag.
- So when a file is named with a dash for example, "**-file.txt**" linux will think that ***-f, -i, -l, -e*** are all flags and will ignore ***.txt*** and throw out an error.
- Therefore when a file starts with one or two dashes , there are two methods to bypass this ;
**METHOD 1: ./ (CURRENT DIRECTORY)**
  - This changes the string from an option to a location.
  - Example:
```
cat ./-file.txt
```

**METHOD 2: -- (End of options marker)**
- This tells linux to stop looking for options and everything that comes after them is a literal file name.
- Example:
```
rm -- -file.txt
```

**NOTE//** -If your file has both spaces and dashes however , you must combine your fixes with quotes, eg with file "--im home--":
```
cat ./"--im home--"
rm -- "--im home--"
```

**NOTE//** - if its just spaces in the filename just use the quotes (" ").
* * *
## LEVEL 4 - 5 
- Searching for human-readable files say , ASCII text or text files
```
find . -type f -exec file {} \; | grep "ASCII text"

or

find . -type f -exec file {} \; | grep "text"
```

- Also , if u are not sure if the file is in text or binary :
```
strings filename
```
- Say a file is not executable, you say:
 ```
find . -type f ! -executable
```
- Size of file we have kilobytes **k**, bytes **c** and megabytes **M**
  
* * *
## LEVEL 7 - 8
- Sorting and fitering out file lines.
- Using **sort, uniq, |, head etc**
- Examples:
**FINDING LINE THAT APPEARS ONCE IN A FILE**
```
sort file.txt | uniq -c | sort -n | head -1
```
- **sort** - sorts the lines in alphabetic or numerical order.
- **sort -n** - sorts the lines numerically in ascending order.
- **sort -rn** - sorts the lines numerically in descending order.
- **uniq -c** - counts how many times each line appears consecutively and prints the count infront of line.
- **uniq -u** - prints only the line that occurs once.
- **head -1** - prints the top line.
- So for example:
**FINDING THE 3 MOST REPEATED LINES**
```
sort file.txt | uniq -c | sort -rn | head -3
```
* * *
## LEVEL 9 - 10 
- Find human readable strings in a file:
```
strings file.txt
```

* * *
## LEVEL 10 - 11
**BASE64**
- Is a command that encodes or decodes data into a text-safe format.
- Uses **A - Z , a - z, 0 - 9, + , /, =**
- So to find a line in a file with base64:
```
grep -E "^[A-Za-z0-9+/=]+$" file.txt
```
0r
```
grep "==" file.txt or grep "=" file.txt
```
- This is becoz most base64 lines are padded(end) with **=** or **==**.
**DECODING BASE64 DATA**
```
echo "base64line" | base64 -d
```

* * *
## LEVEL 11 - 12
## ROT13
- Its a simple cipher that hides text by shifting/rotating them by 13 positions.
- Example: **A - N, b - O, C - P**.
- Also : **N - A , B - O , P - C**
- So to encrypt data:
```
echo "hello" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```
- Decrypting is also the exact same way.
- **tr** is a command used to transform/translate characters  **'set1'** with ones from **'set2'**.
```
tr 'set1' 'set2'
```
eg:
```
echo "hello" | tr 'a-z' 'A-Z'
```
output
```
HELLO
```
- So to get a line from a file where ROT13 has been used:
```
cat file.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

* * *
## LEVEL 12 - 13
## HEX DUMP
- Is a file represented in hexadecimal format.
- Binaries are hard to read , hex is compact
- Example:
```
hello = 48 65 6c 6c 6f
```
- As a file :
```
xxd file.txt

#output

00000000: 4865 6c6c 6f20 776f 726c 640a Hello world.

```
- **00000000** - offset(position in file).
- **4865 6c6c** - hex data.
- **Hello** - decoded text(ASCII).

**NOTE! >>** - hex is not a form of encryption , its a form of representing data.
- Tools to create hex dumps:
```
xxd file.txt
or
hexdump -C file.txt
or
od -x file.txt #octal dump but can still do hex
```
- Converting from HEX back to text:
```
xxd -r file.hex > file.bin

or

xxd -r -p file.hex > file.bin
```
- **-p** ensures that the output is plain hex (no offsets).

**REAL LIFE SCENARIO**
```
echo "hello" > test.txt
xxd test.txt
xxd -r test.hex
```

## USES
1. File signatures eg PNG
2. Viewing binary files eg images
3. HIdden data like passwords , flags in hex.
* * *
## COMPRESSING FILES
- **gzip** ,**bzip2**, **xz**, **7zip**and **zip** are the common used compression tools.
- **gzip** is faster, but **bzip2** has better compression.

**GZIP**
```
gzip file.txt  #zipping 

gunzip file.txt.gz  #unzipping

```
- To keep a original file while compressing it :
```
gzip -k file.txt
```

**BZIP2**
```
bzip2 file.txt #zipping

bunzip2 file.txt.bz2 #unzipping
```
- Same as **gzip** when u want to keep the original file.

**XZ**
```
xz file.txt #zipping

unxz file.txt.xz  #unzipping
```

**ZIP**
- Can store multiple files into 1 zip file.
- Combines archive and compression.
```
zip archive.zip file1 file2  #zipping

unzip archive.zip  #unzipping
```

**7ZIP**
```
7zip a archive.7z folder/  #creating

7zip x file.7z #extracting
```

* * *
## TAR
- Stands for **Tape Archive**.
- Used to :
   - Combine multiple files into one archive file.
   - preserve directory structure, permissions, timestamps etc.
- File has **.tar**.

1. **CREATING A TAR ARCHIVE**
```
tar -cf file.txt video.mp3 image.png
```
 - Now all the file above are bundled into **archive.tar**.
 - Extracting **archive.tar**.
```
tar -xf archive.tar
```
**FLAGS**
- **-c** - create
- **-f** - file
- **-x** - extract
- **-v** - verbose/show files
- **-t** - list content

- Examples:
```
tar -cvf files.tar myfolder/  #create an archive

tar -xvf files.tar  #extract files

tar -tvf files.tar  #lists contents without extracting
```

* * *
## LEVEL 13 - 14
## SCP (Secure Copy Protocol)
- It is used to securely copy files from between computers with **ssh**.
```
scp <file> <username>@<IP address or hostname>:<Destination>
```

- Example:
```
scp notes.txt user@192.168.1.5:/home/user/
```

- From another computer to urs:
```
- scp user@102.168.1.5:home/user/file.txt .
```
- Copies the file into the current directory (.)
```
scp -r myfolder user@102.168.1.5:home/user/
```
- **-r (recursive)** - means to copy the whole folder and any subfolders.
```
scp -i ssh.key user@192.168.1.5:etc/userpass_pass/user .
```
- **-i** flag is used to tell the server to use the key given to authenticate the command.

**CONNECTING TO A SERVER THROUGH SSH WITH A PRIVATE KEY**
```
ssh -i <privatekey> <username>@<IP address or hostname> <port>
```
- Example:
```
ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```

**NOTE>>** - the ssh key was getting malformed when i manually pasted it into my machine so i used:
```
ssh -p 2220 bandit13@bandit.labs.overthewire.org "cat sshkey.private" > sshkey.private
```

- To confirm key integrity use:
```
ssh-keygen -y -f <privatekey>
```
**-y** - read key and print it public 
**-f** - specify file 
* * *
## BANDIT 14 - 15
## SENDINF INFORMATION TO A SERVER USING PORTS
1. **NETCAT**
- Is a networking tool used to :
   - Send/recieve data over a network.
   - Connect to ports
   - Test services
   - Debug network connections
   - Transfer files
- Example , **connecting to a server:**
```
nc host port #eg

nc google.com 80
```
- This allows a computer to connect to and listen on port 80 on a google.com browser.
- **Talking to a web server:**
```
nc example.com 80  #then type

GET /HTTP/1.1
Host: example.com
```
- Press enter twice and u'll receive a raw HTTP response.
-**Sending data to a server:**
```
echo "data" | nc hostname port
```

- Scanning for open/listening ports:
```
nc -z hostname portrange  #eg

nc -z localhost 1-10000
```
- **-z** scans for listening ports(services waiting for a connection).
- **v** (verbose) - lists all  ports.


- **Listening mode**
```
nc -lvp port #eg

nc -lvp 5555
```
- **-l** - listen
- **-v** - verbose
- **-p** -port
- **Connecting to listener**
```
nc LOCAL_IP port  #eg

nc 192.168.1.80 5555
```

**SCENARIO - TRANSFER OF FILES**
**LISTENER/RECEIVER**
```
nc -lvp 5555 > file.txt
```

**SENDER**
```
nc 192.168.1.80 5555 < file.txt
```

2. **NCAT**
- Similar to **nc** but more secure as u can connect with **SSL** for more secure transfers/
```
ncat --ssl hostname port #connect with SSL

ncat --ssl -lvp port  #listening with  SSL
```

3. **TELNET**
- A protocol for file transfer but is insecure as data is in plaintext form.
```
telnet host port # type message then ENTER
```
- Connecting is similar to **nc**.
- 
## PORTS
1. **22** - SSH
2. **80** - HTTP
3. **443** - HTTPS
4. **25** - SMTP


## OPENSSL
- Used to send data and communicate with a server securely using SSL/TLS.
- **To connect:**
```
openssl s_client -connect hostname:port
```
- To send data secure and stay quiet

```
echo "hello" | openssl s_client -connect hostname:port -quiet
```

* * *
## BANDIT 16-17
- How to look for ports that support ssl:
1. **USING NMAP**
```
nmap --script ssl-cert hostname

#or

nmap --script ssl-cert -p portrange

#eg

nmap --script ssl-cert -p 1-10000
```
* * *
## BANDIT1 17
- Only problem was finding a modified line in a file.
- If u have the new and old version of the file u can:
```
diff oldfile.txt newfile.txt

#result
<"what is used to be"

>"what it is now"
```
- Used to see the modified/changed line that is different from the old file.
* * *
## BANDIT19-20
- Challenge was using a setuid/suid binary.
- A setuid/suid binary allows for a program to be executed with the file's owner priviledges.
- For example the solution with the suid being **bandit20-do** was:
```
./bandit20-do cat /etc/bandit_pass/bandit20
```
- **./bandit20-do** - this is how you execute a suid binary without any arguments.
- Now  above we see:
```
./bandit20-do <command>
```
- This translates to ***execute this command with bandit20's priviledges***
- We first find out who owns the SUID by:
```
./bandit20-do whoami
```

- To know if a file is a SUID:
```
-rwsr-rw-rw-- #the s is small meaning that it is executable by users

-rwSr-rw-rw-- #the S is in capital means that it is NOT executable by normal users
```

* * *
## BANDIT 20 -21
- So its all about **CLIENT - SERVER** communications,
- So basically:
   1. A port is listening (Server)
   2. You connect to the listening port (Client)
   3. You send data and the port responds.
- So in this level i had to create my own listening port & store the current password in it:
```
echo "password" | nc -lvp 1234 &
```
- **&** - is used to make the port run in the background (as a daemon).
- Then we used the SUID binary as we were shown:
```
./SUIDbinary <listeningportnumber>
```
**NOTE** - In linux ports below **1024** are priviledged port and only root can bind them, so create a port that is higher than that.
