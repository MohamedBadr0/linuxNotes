## Linux:


### File Archive and Compression ( 05:48 ) :
- File Archive converts a directory hiraricy to a file.
- Compression ==> gunzip or gzip, bzip
- gzip faster than bzip, but bzip file lighter than gzip file

#### File Archive
```
Create archive : 
tar command for archive     ==> tape archive 
cd /var/
tar -cf log.tar log/        ==> c for create archive, f for file name
ls -dhl log*

tar -cvf log.tar log/       ==> v for verbose 
file log.tar                ==> file type

tar -tf log.tar             ==> see content of tar file
```

#### File unarchive 
``` 
tar -xvf log.tar -C /home/mohamed/Downloads/ 
tree /home/mohamed/Downloads/log/
```

#### Compression
```
cd /var/log
ls -l
ls -lh boot*

gzip filename               ==> compress the original file
gzip boot.log
gzip -k boot.log            ==> keep the original file
      
- archive and compression using gzip
tar -czvf log.tar.gz log/   
tar -czvf log.tgz log/      ==> z for compression using gzip
ls -lhd log*
file log.tar.gz

mv log.tar.gz log.tgz       ==> rename a file

- archive and compression using bzip
tar -cjvf log.tar.bz log/   ==> j or compression using bzip
```

#### Decompression
``` 
gzip -d boot.log.gz         ==> -d for decompressed file
gunzip filename 

- unarchive and decompression
tar -xjvf log.tar.bz -C /home/mohamed/Downloads/    ==> j bzip
tar -xzvf log.tar.bz -C /home/mohamed/Downloads/    ==> z gzip
```

### File Ownership and Permissions ( 06:04 ):
- User Owner and Group Owner
- premissions (Read r, Write w, Execute x).
- premissions (user:group:other)    >> (u:g:o)
- file mode (9 fields) >> rwxrwxrwx

#### File Ownership
```
cd ~
touch somefile
ls -lh
ls -lh | grep somefile

-- Create a new group
sudo groupadd adminn            
cat /etc/group                      ==> see all groups
cat /etc/group | grep adminn

cat /etc/passwd | grep user         ==> see primary group
cat /etc/passwd | grep mohmed   
    >> mohamed:x:1000:1000:Mohamed:/home/mohamed:/bin/bash

sudo usermod -g adminn mohamed      ==> change primary group to adminn group
    >> mohamed:x:1000:1001:Mohamed:/home/mohamed:/bin/bash
ls -lh

groups                              ==> adminn not in groups  
groups mohamed                      ==> admin in groups for this user
                                    ==> restart your pc
groups                              ==> now adminn is added

cd /tmp
mkdir dir
cd dir
touch file1
echo "some text" >> file1

-- Create new user called user1
sudo useradd user1 -m -s /bin/bash      ==> -m for home directory, -s change default shell from sh to bash
sudo passwd user1                       ==> create password for user1


-- Switch user
su - user1

-- Change ownership of a file
cd /tmp/dir
ls -lh
        >> -rw-r--r-- 1 mohamed adminn 10 Feb  9 07:00 file1
sudo chown user1:user1 file1           ==> change user and group of a file
        >> chown user:group file
sudo chown user1 file1                 ==> change user of a file

sudo chown :mohamed file1              ==> change group of a file
sudo chgrp adminn file1                ==> change group of a file
``` 

#### Permissions
- numeric representaion
- r ==> 4, w ==> 2, x ==> 1
- user:rw       4+2     =   6   {read + write}
- user:rwx      4+2+1   =   7   {read + write + execute } 
- sudo chmod u=rwx,g=rwx,o=rwx filePath
- u=rw,g=r,o-r  >>  user{read + write}, group{read only}, other{remove read}
- 744   >>  rwxr--r--
- 677   >>  rw-r--r--


```
groups user1        ==> see user1 groups

cd /tmp/dir
ls -lh      >>  -rw-r--r-- 1 mohamed adminn 10 Feb  9 07:00 file1
            >> others has read premission 
            >> switch to user1 and read file contents
su - user1
cat file1

-- change mode command (chmod) :
sudo chmod u=rw,g=r,o-r file1
    >>  -rw-r----- 1 mohamed adminn 10 Feb  9 07:00 file1

sudo chmod o+r file1
    >>  -rw-r--r-- 1 mohamed adminn 10 Feb  9 07:00 file1

sudo chmod ug+x file1       ==> add execute to user and group
    >>  -rwxr-xr-- 1 mohamed adminn 10 Feb  9 07:00 file1

sudo chmod ugo+x file1       ==> add execute to user, group and other
    >>  -rwxr-xr-x 1 mohamed adminn 10 Feb  9 07:00 file1

sudo chmod g-x file1        ==> remove execution from group
    >>  -rwxr--r-- 1 mohamed adminn 10 Feb  9 07:00 file1

-- change mode command (chmod) using numeric representation :
sudo chmod 644 file1
    >>  -rw-r--r-- 1 mohamed adminn 10 Feb  9 07:00 file1

sudo chmod 645 file1
    >>  -rw-r--r-x 1 mohamed adminn 10 Feb  9 07:00 file1


-- getfacl  (get file access control list) :
getfacl file1

cp file1 file2
chmod 640 file2
    >>  -rw-r----- 1 mohamed adminn 10 Feb 10 06:21 file2

-- only permit user1 to read file2
setfacl -m u:user1:r file2
    >>  -rw-r-----+ 1 mohamed adminn 10 Feb 10 06:21 file2
```

### Managing Linux Processes ( 06:34 ) :
- System processes
    - kernel is responsible to start process
- User processes
    - user is responsible to start process
- parent process systemd (process id=1, pid=1)
- pid=0 for kernel
- all service in background called deamon

```
-- ps command is snapshot not real time (ps - jobs):
ps                  ==> processes running in these terminal
ps -a               ==> processes running from user 
ps -e               ==> all processes running on the system
ps -l               ==> more details
ps -f

pstree
ps -fl              ==> add start time over -l result
ps -efl

-- top is a live command :
top                 ==> all processes running live - q to exit

sudo apt install htop -y
htop

-- Execute in background ( & - jobs )  : 

sleep 10            ==> 10 seconds  - Ctrl+c to exit
sleep 1m            ==> 1 minute

sleep 10 &          ==> sleep in background
    >>  [1] 11890 -- this is jopId or PID
ps    
jobs                ==> jobs running in background

sleep 20            ==> Ctrl+z for moving this process to background
ps

-- move command from background to foreground :
vi &
fg      ==> foreground

07:02
```

### Software Package Management ( 07:02 ) :
-- RPM naming convention
    - <package_name>-<version_num>-<release_num>.<distribution>.<arch>.rpm
-- DEB
    - - <package_name>_<version>_<arch>.deb

```
dpkg -l                         ==> all packages on your system

dpkg -p <backage_name>          
dpkg -p bash        

-- Download backage :
wget <url for package.deb>

dpkg -I <package>               ==> more information about backage
dpkg -I code_1.96.4-1736991114_amd64.deb

-- Install backage :
sudo dpkg -i code_1.96.4-1736991114_amd64.deb

dpkg -l                         ==>  see all packages
dpkg -p <package_name>          ==> ensure that the backage is installed
dpkg -p code

dpkg -L <package_name>
dpkg -L code                    ==> locations of backage files

dpkg -S <file_name>             ==> package that installed this file
which code
code                            ==> start program

-- Uninstall backage :
sudo dpkg -r <package_name>

-- apt is a tool fasilate package managment process :
cd /etc/apt
ls
cat sources.list                ==> see all sources of apt SPM  

sudo apt update                 == sudo apt-get update
apt list --upgradable
sudo apt upgrade
sudo apt full-upgrade

apt search terminal

sudo apt remove <package_name>

```


