# Kioptrix 1.2

LINK: https://www.vulnhub.com/entry/kioptrix-level-12-3%2C24/

Type: Web application

# Enumeration
 Target Ip: 172.20.10.7
 
 Using Nmap to scan the target:
 ![VirtualBox_LINUX SERVER_23_04_2024_20_24_59](https://github.com/Fernandez99fc/cybersec/assets/172477285/25555fe8-cd02-4c36-b1ab-1f425c176d5a)
Two open ports found are ssh(port 22) running version openssh 4.7p1 and http(port80) running apache httpd 2.2.8 as the service version.

I have tried logging into the ssh server,but it didn't work. I'll have to try visiting the apache server running on http(port 80) in my browser. Firstly, I'll add the target ip to the /etc/hosts file to be resolved into the domain being hosted on the server. I named it koiptrix3.com
![VirtualBox_LINUX SERVER_23_04_2024_20_44_06](https://github.com/Fernandez99fc/cybersec/assets/172477285/bdcc1795-c4bb-48b8-87a7-da92aa588a66)
After reading the blog page and the documentation, I realized the page is being hosted on http://kioptrix3.com. and it has a page for gallery in kioptrix3.com/galley.

After visiting kioptrix3.com/gallery, I found nothing useful there.

![VirtualBox_LINUX SERVER_23_04_2024_20_53_42](https://github.com/Fernandez99fc/cybersec/assets/172477285/2a67190c-0016-4de6-8bae-0428e32e46dd)

But there's a login page right beside the blog.
![VirtualBox_LINUX SERVER_23_04_2024_20_56_08](https://github.com/Fernandez99fc/cybersec/assets/172477285/3e2c400b-059e-4864-ab4e-add22e895400)

Noticed the page is powered by lotuscms which is a type of contents management system that is used to create and manage contents on a website. We can check if lotuscms has any known vulnerability with metasploit. 

![VirtualBox_LINUX SERVER_23_04_2024_21_02_09](https://github.com/Fernandez99fc/cybersec/assets/172477285/6b7cb7e5-ed11-42e4-b083-4e68cacb8da0)

Found an exploit "lotus cms 3.0 eval remote code execute". Using show options command to view available options for the exploit. I Set rhost to target ip, changed URI and changed the payload to generic/shell_bin_tcp.
![VirtualBox_LINUX SERVER_23_04_2024_21_09_50](https://github.com/Fernandez99fc/cybersec/assets/172477285/e932de46-35bf-4304-b05f-7f78631818e2)
This exploit will execute a remote code in the lotuscms and create a bind shell from the cms.
![VirtualBox_LINUX SERVER_23_04_2024_21_12_58](https://github.com/Fernandez99fc/cybersec/assets/172477285/8a967676-870b-40ca-9d2f-2a2fc56e4a8b)

After gaining a shell, I ran "python -c import pty; pty.spawn("bash")" to expand the interactive shell.
![VirtualBox_LINUX SERVER_23_04_2024_21_46_45](https://github.com/Fernandez99fc/cybersec/assets/172477285/3a557098-efb0-4241-ab07-c29865f08b7e)
After searching through the system, I found some user accounts in the /home directory. Then searching through each of them, I was able to find a gconfig.php file in the www kioptrix.com/gallery home directory. Gconfig.php is a file used for storing data such as usernames and passwords in mysql database.

We could see some credentials of the root user being exposed. We will try to login with that.

![VirtualBox_LINUX SERVER_23_04_2024_21_56_18](https://github.com/Fernandez99fc/cybersec/assets/172477285/c7f78b09-7a09-448a-ab41-3b2469c6537d)

By using gobuster to perform a directory bursting, we could find phpmyadmin webpage login. 
![VirtualBox_LINUX SERVER_23_04_2024_22_02_40](https://github.com/Fernandez99fc/cybersec/assets/172477285/2be90e6d-2884-4139-9672-9cc799432c56)
   We would use the credentials found in the gconfig.php file to login to the phpmyadmin webpage, since phpmyadmin is a graphical user interface(GUI) software used to manage mysql database.
![VirtualBox_LINUX SERVER_23_04_2024_22_00_41](https://github.com/Fernandez99fc/cybersec/assets/172477285/beebfea4-f860-4c4e-b054-22a6b0f6713f)
After login, we now have access to myphpadmin and we can maintain access to view user credentials and other interesting stuffs.

![VirtualBox_LINUX SERVER_23_04_2024_22_06_35](https://github.com/Fernandez99fc/cybersec/assets/172477285/e2fc3928-6e47-4451-a5ab-027a4e38643d)
By searching the database, we can see some dev/user account credentials by navigating to gallery>dev_accounts>browser and we could see some user credentials for loneferret and dreg. 
![VirtualBox_LINUX SERVER_23_04_2024_22_09_24](https://github.com/Fernandez99fc/cybersec/assets/172477285/657e290e-41a0-4eb9-aa8b-20d3836bb6e6)

The name loneferret sounds familiar? When we read the blog page, we saw a welcome message of a new lead programmer. We could have administrative access if you can get hold of his account. I'm trying that first!

We have the hash of the users, now we need to know the plaintext of the hash to get the password. I'll be using "hash-identifier" to identify the type of hash first. 
Type hash-identifier and paste the hash.
![VirtualBox_LINUX SERVER_23_04_2024_22_35_41](https://github.com/Fernandez99fc/cybersec/assets/172477285/ee4872de-43c1-4f6a-ae60-a06c6f14436d)
Possible hashes are MD5 and domain-cached credentials... Since we know it's most likely to be a Md5 hash, we will try to decode the hash and I'll be using johnTheRipper.

# Note: 
Hashes can't be reversed from ciphertext, because a hash is a one-way function/algorithm, The only way a hash can be reversed is if it matches a known hash, therefore, they have the same password.  Using johnTheRipper, we can specify a wordlist, but by default john has a wordlist. John compares the hash of the passwords in that wordlists to the hash we want to crack, if the hash matches the hash, then we get the password. 

Create a file and store the hash in it, created a file called passwords. Then use john to crack with the command "john --format=raw-md5 <file name>"
--format - Specifies the hash format
![VirtualBox_LINUX SERVER_23_04_2024_22_37_15](https://github.com/Fernandez99fc/cybersec/assets/172477285/b58a3713-d8bb-401d-92ed-2eb2d128b960)

We found a possible hash and reveals the password is "starwars"
![VirtualBox_LINUX SERVER_23_04_2024_22_37_15](https://github.com/Fernandez99fc/cybersec/assets/172477285/0aaabbbd-3136-46dc-b258-9c14f733499e)

We have a ssh service running on the target, we will try to login with the username and the password recovered(Username=loneferret, password=starwars).
![VirtualBox_LINUX SERVER_23_04_2024_22_48_07](https://github.com/Fernandez99fc/cybersec/assets/172477285/d85751fd-51a2-47ed-8a0c-865357bc1b16)

-o To specify options

HostKeyAlgorithm- To specify key algorithm accepted by the server.

We are successfully logged in, using "ls" command to list files and directories in the user's home directory, we found a file named "CompanyPolicy.readme". Using "cat" to read the file,we could see the content of the file, telling the employee that it's company policy to use their newly installed software for creating,editing and viewing files, and he shoudl use the "sudo ht" command.
![VirtualBox_LINUX SERVER_23_04_2024_22_48_07](https://github.com/Fernandez99fc/cybersec/assets/172477285/15137e15-1133-457d-8999-641610000bbf)
We will run the command "sudo ht". But we ran into an error "error openinng terminal: xterm-256 color". We can check if we have the permission to run the command as sudo(root) by typing "sudo -l". But we do. 
![VirtualBox_LINUX SERVER_23_04_2024_22_50_28](https://github.com/Fernandez99fc/cybersec/assets/172477285/3fb8134b-cb00-4074-ab5b-a70d14b0ef75)
We can resolve this by typing "export TERM=xterm". Then retry the command again.
![VirtualBox_LINUX SERVER_23_04_2024_22_56_33](https://github.com/Fernandez99fc/cybersec/assets/172477285/ffb9b015-9fcc-452a-a7d2-f043e660b4a7)
![VirtualBox_LINUX SERVER_23_04_2024_22_56_01](https://github.com/Fernandez99fc/cybersec/assets/172477285/fe439651-7c7d-48b5-a073-254c41736804)

It's working now. The goal is to escalate privileges to the root account. Since we have sudo right to use the editing software which we can use to modify any file. There various ways we can grant ourselves sudo rights, but we can choose to modify the /etc/sudoers file. /etc/sudoers file can be used to grant privileges as the super user(root) to run any command to perform any action on the system.
By pressing CTRL+F we can see the menu and open the /etc/sudoers file. 
![VirtualBox_LINUX SERVER_23_04_2024_22_59_08](https://github.com/Fernandez99fc/cybersec/assets/172477285/0431e99b-1a80-46f5-8830-cce134a6ce11)
![VirtualBox_LINUX SERVER_23_04_2024_23_00_12](https://github.com/Fernandez99fc/cybersec/assets/172477285/b7bc5a1b-abac-44bb-a8e6-20d64412cfdf)
![VirtualBox_LINUX SERVER_23_04_2024_23_00_34](https://github.com/Fernandez99fc/cybersec/assets/172477285/73908515-e06d-493e-bc39-55c416baaeff)
Now in the file, we will grant Loneferret same permission as root in the file. Clear the line and assign new permissions.
![VirtualBox_LINUX SERVER_23_04_2024_23_03_54](https://github.com/Fernandez99fc/cybersec/assets/172477285/c4663c18-b372-435b-98f9-17c2ebb0e358)
From here, I'll save using CTRL+F to save the file again and exit. 
typing sudo su to switch user to root and we now have access to the root account.
![VirtualBox_LINUX SERVER_23_04_2024_23_10_31](https://github.com/Fernandez99fc/cybersec/assets/172477285/d960d99c-eaa3-4118-9cb2-71306cbdfb92)







