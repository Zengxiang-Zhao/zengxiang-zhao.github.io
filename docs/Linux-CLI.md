---
layout: default
title: Linux CLI
date:   2021-09-11 20:28:05 -0400
categories: Linux
nav_order: 100

---

---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---


## [The difference between double brackets and single brackets](https://www.google.com/search?q=double+brackets+vs+single+brackets+bash&rlz=1C1UEAD_enUS1114US1114&oq=two+brakets+vs+single+bracket&gs_lcrp=EgZjaHJvbWUqCAgBEAAYFhgeMgYIABBFGDkyCAgBEAAYFhgeMggIAhAAGBYYHjINCAMQABiGAxiABBiKBTINCAQQABiGAxiABBiKBTIKCAUQABiABBiiBDIHCAYQABjvBTIHCAcQABjvBTIKCAgQABiABBiiBNIBCTEzNTAzajBqN6gCCLACAfEFBbAK0ZpwWkHxBQWwCtGacFpB&sourceid=chrome&ie=UTF-8)

The following answer comes from the google AI:

> In Bash, both single brackets `[ ]` and double brackets `[[ ]]` are used for conditional expressions, but they have key differences:
>
> Single Brackets `[ ]`:
> 
> - **POSIX Compliant:** is a synonym for the test command and is part of the POSIX standard, ensuring wider compatibility across different shells.
> - **External Command:** is treated as an external command, which means it creates a new process.
> - **Word Splitting and Globbing:** Variables inside `[ ]` are subject to word splitting and filename expansion (globbing), requiring them to be quoted (e.g., `[ "$VAR" = "value" ]`) to prevent unexpected behavior with spaces or special characters.
>- **Comparison Operators:** Uses -eq, -ne, -lt, -gt, -le, -ge for numeric comparisons, and = or == for string comparisons (though = is more widely supported).
>
>  Double Brackets `[[ ]]`:
>
> `[[ ]]` is a built-in feature of Bash (and other shells like ksh and zsh), offering better performance as it avoids creating a new process.
>
> - **Enhanced Functionality:** Provides more advanced features, such as: No Word Splitting/Globbing: Variables within [[ ]] are not subject to word splitting or filename expansion, making quoting optional (e.g., [[ $VAR == value ]] works even with spaces in $VAR).
> - **Regular Expression Matching:** Supports `==` for pattern matching using shell globs (e.g., [[ $VAR == *.txt ]]). For full regular expressions, [[ $VAR =~ regex ]] is used.
> - **Logical Operators:** Allows for `&& (AND)`, `|| (OR)`, and `! (NOT)` within the expression, simplifying complex conditions.
> - **File Test Operators:** Offers enhanced file test operators.
>
> **Comparison Operators:**
>
> The double brackets `[[]]` uses `==, !=, <, >, <=, >=` for both string and numeric comparisons. Note that `<` and `>` perform lexicographical comparison for strings.
>
> Recommendation:
> 
>Generally, `[[ ]]` is preferred in Bash scripts due to its enhanced features, better performance, and safer handling of variables. However, if maximum portability across various POSIX-compliant shells is a strict requirement, `[ ]` should be used.


## [ WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! ](https://stackoverflow.com/questions/20840012/ssh-remote-host-identification-has-changed)

Sometimes you encounter the issue that you cannot control the remote server by public key. And there's a error message said that "Someone could be eavesdropping on you right now".
Right now you can just remove all host keys about the server


> Here is the simplest solution:
> 
> `ssh-keygen -R <host>`
> 
> For example,
> 
> ssh-keygen -R 192.168.3.10
>
>From the ssh-keygen man page:
>
>>-R hostname Removes all keys belonging to hostname from a known_hosts file. This option is useful to delete hashed hosts (see the -H option above).

## Convert the Ubuntu defautl editor from nano to vim

Using select-editor:
Run the command: `select-editor`.
This will present a list of available editors. Select the number corresponding to Vim.
This method updates the `~/.selected_editor` file, which is used by some applications. 

## Convert float number to interger

```sh
memorySpace="2.8"
memorySpaceInteger=$( printf "%.0f" $memorySpace )
if [ "$memorySpaceInteger" -ge 5 ]
then
break # or other expression
fi
```

## [Close session in Ubuntu linux](https://askubuntu.com/questions/12180/logging-out-other-users-from-the-command-line)
If you use the Remote Desktop to login your remote server and get the back screen. Then your session may need to be killed manually.

```bash
# step 1:
who -u

# kill the pid in the last column
kill PID
```

## [Install http in your server command line](https://httpie.io/docs/cli/linux)

You need to install the package `httpie`, then you can use `http` in the command line as shown below:

```bash
$ http POST :1234/login username=test password=test

HTTP/1.0 200 OK
Content-Length: 32
Content-Type: application/json
Date: Fri, 27 Sep 2024 14:30:38 GMT
Server: Werkzeug/2.0.3 Python/3.8.16

```

## [How to strip multiple spaces to one using sed?](https://unix.stackexchange.com/questions/19014/how-to-strip-multiple-spaces-to-one-using-sed)

```bash
... | sed 's/  */ /g' # two space with *
... | sed 's/ \{1,\}/ /g' # not clear this one
... | tr -s ' '
```

Note: 
1. `sed 's/  */ /g'` : repalce more than two spaces with one space in the whole line.
2. `tr -s " "` : Squeezing repeating characters: Replacing multiple consecutive occurrences of a character with a single instance using the -s option.

## [how to control remote servers without using password](https://www.linuxjournal.com/article/8600)

Sometimes, you'd like to fetch some data from different servers. e.g. check the space usage in 6 servers. If we login the 6 servers and using the command `df -H`, it would be a little tedious. When the number of servers comes to 1000, it seems like impossilbe to do it everyday. Now it comes to the topic: how could we control the remote servers?

We can use private key and publick key paires to let the local server(server-local) to control multiple remote servers(server-remote). 

```bash
# login the local server 
ssh-keygen -t rsa # to generate private key and publick key.

```

the publick key and private key will be saved in the ~/.ssh

You must keep the private key in safe and don't modify and move it. 

What you should do is to copy the publick key to the remove server, and create a `.ssh` folder in the remote server home dictionary. And create a file named `authorized_keys`. 

Copy the content of publck key to the authorized_keys. **You should not use the mouse copy and paste the content. You should use scp to transfer the publick key file and then use cat to append the content to the end of the file authorized_keys.**

`scp -r user@example.com:~/pathTo/id_rsa.pub id_rsa.pub/name.pub`

If there's already a authorized_keys file, you need to append the content of publick key file to the end of authorized_keys: 

`cat id_rsa.pub >> .ssh/authorized_keys`

After you finished these steps, you can control the remote server in the local server

```bash
# login the local server

ssh name@ip # login the remove server without password

ssh name@ip "command line"

ssh name@ip "df -H" # check the remote server disk usage

scp local_file name@ip:pathToSave # securetly copy files without password

```

Now you have a lot of space in the local server to do what you'd like to do. No matter it's backing up data in remote servers or check the disk usages.

[Here's a good tutorial about backing up data](https://alvinalexander.com/linux-unix/how-use-scp-without-password-backups-copy/)


## [Show Running process in Linux](https://www.cyberciti.biz/faq/how-to-check-running-process-in-linux-using-command-line/)

```bash
ps aux | head

USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND

```


## [`scp` Copy file from between to servers](https://linuxize.com/post/how-to-use-scp-command-to-securely-transfer-files/)

When you cannot mount a folder in a server, you can try to use `scp` to securely copy files from one server to another.

```bash
scp [OPTION] [user@]SRC_HOST:]file1 [user@]DEST_HOST:]file2

#To copy a file from a local to a remote system, run the following command:

scp file.txt remote_username@10.10.0.2:/remote/directory

#To copy a directory and all its files, invoke the scp command with the -r flag,
#which recursively copies the entire directory and its contents:

scp -r /local/directory remote_username@10.10.0.2:/remote/directory

```

scp provides a number of options that control every aspect of its behavior. The most widely used options are:

> `-P` - Specifies the remote host ssh port.
> 
> `-p` - Preserves file modification and access times.
> 
> `-q` - Use this option if you want to suppress the progress meter and non-error messages.
> 
> `-C` - This option forces scp to compress the data as it is sent to the destination machine.
> 
> `-r` - This option tells scp to copy directories recursively.

## Check the volume of folder in Linux

```bash
du -sh folderName

-h. Shows sizes in a human-readable format.
-s. Summarizes the total for each argument.
-a. Includes files as well as directories.

e.g

(base) ➜ du -sh streamlit-combine  
12M     streamlit-combine

du -csh /myFolder/*

# output
1.3T    /myFolder/input
4.0K    /myFolder/1
4.0K    /myFolder/2
8.1G    /myFolder/docker
4.0K    /myFolder/3
2.2G    /myFolder/tmp
3.6T    total
```

```bash
df -H Path_to_Folder

e.g.

df -H /myFolder
#output
Filesystem      Size  Used Avail Use% Mounted on
/dev/md42        16T  3.7T   12T  25% /myFolder

```

## [nohup ](https://www.digitalocean.com/community/tutorials/nohup-command-in-linux)

If you still want to keep the processes running even exiting the shell/terminal, then `nohup` is your choice.

Nohup, short for no hang up is a command in Linux systems that keep processes running even after exiting the shell or terminal

E.g. when you upload illumina whole run locally using bs command line:

```bash
nohup $bs upload run -n reupload-240312At0317 –t NovaSeq6000 ./240312_A01905_0076_AH7KH7DSX > ~/projects/illumina/UploadRun.out
```

Note:

1. -n : refer to the name you'd like to show up in basespace
2. -t : refer to the machine that you used for the sequence
3. ./240312_A01905_0076_AH7KH7DSX : specify the wohle run folder in the machine
4. > ~/projects/illumina/UploadRun.out : redirect the output to another file in case you need to check the log.


If you would like to run the command background, then append `&` at the end of the command as shown below:

```bash
nohup COMMAND > outputPath/filename.out &
```

You can use the `jobs` to list the running `nohup` command.


## [Backup in Linux](https://averagelinuxuser.com/automatically-backup-linux/)
If you build a database and you'd like to backup the database periodically, then you can use `rsync` and `crontab`

1. You should use the user account that has the `sudo` permission
2. Test [`rsync`](https://averagelinuxuser.com/backup-and-restore-your-linux-system-with-rsync/) : `rsync -aAXv --delete --dry-run --exclude=/dev/* --exclude=/proc/* --exclude=/sys/* --exclude=/tmp/* --exclude=/run/* --exclude=/mnt/* --exclude=/media/* --exclude="swapfile" --exclude="lost+found" --exclude=".cache" --exclude="Downloads" --exclude=".VirtualBoxVMs"--exclude=".ecryptfs" / /run/media/alu/ALU/` . The `--exclude` can recognize regex pattern, `--delete` means if you delete some files from the source, then this file will also be deleted from the target. `--dry-run`: just test not run in real case, you can use it test whether the files are what you want.
4. Enter command : `sudo crontab -e`, it will use the root permission to run the command, so you don't need to add `sudo` at the head of the command line.
5. Write down the `rsync` command based on the creteria to write down the command that you'd like to run periodically.
7. Save it.

If you'd like to run the command every minutes, then you need to write the time like : `* * * * *`

Sometimes, you do not have the sudo permission to use rsync command. Here's a solution to do that on the other way.
1. make the folder that you'd like the sudo user have the write permission: [setfacl -R -m u:username:rwx myfolder](https://askubuntu.com/questions/487527/give-specific-user-permission-to-write-to-a-folder-using-w-notation)
2. update the crontab file in the sudo user enviroment: `crontab -e`

**`The safe way to store you data is to backup your data using crontab and then using github to save your source code.`**

## Backup using bash script

Sometimes, the folder you'd like to backup is not permitted to cooy or you can just copy the structre of the folders. And there are not files in the folder. then try the following methods

1. Create a bash script file and write down the backup command rsync. e.g: `rsync -av --delete --exclude="__pycache__" --exclude=".pytest_cache" --exclude="node_modules" --exclude=".git" --exclude='temp' --exclude='tests'  /home/pathToSourceFolder  /pathToTargetFolder`
2. Open crontab file with sudo: `sudo crontab -e`
3. Write the following command to backup data periodically

```bash
# Backup  projects everyday at 6:00PM
* * * * * sh /pathToBashFolder/backupProjectsAt6PM.sh
```

*Example* 

/pathToBashFolder/backupProjectsAt6PM.sh
```bash
rsync -av --delete --exclude="__pycache__" --exclude=".pytest_cache" --exclude="node_modules" --exclude=".git" --exclude='temp' --exclude='tests'  /home/pathToSourceFolder  /pathToTargetFolder

# Here you can edit this file and add more backup projects
```

crontab file
```bash
# Backup  projects everyday at 6:00PM
* * * * * sh /pathToBashFolder/backupProjectsAt6PM.sh
```

*Note*

> A trailing slash on the source changes this behavior to avoid creating an additional directory level at the destination.  You can think of a
       trailing / on a source as meaning "copy the contents of this directory" as opposed to "copy the directory by name", but in  both  cases  the
       attributes  of the containing directory are transferred to the containing directory on the destination.  In other words, each of the follow‐
       ing commands copies the files in the same way, including their setting of the attributes of /dest/foo:

```bash
  rsync -av /src/foo /dest
  rsync -av /src/foo/ /dest/foo
```




## [Backup MongoDB](https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04)

In crontab

```bash
3 3 * * * mongodump --out /var/backups/mongobackups/$(date +'%Y-%m-%d')
```

In order to save only the last 7 days data to save memory

```bash
1 3 * * * find /var/backups/mongobackups/ -mtime +7 -exec rm -rf {} \;
```

## Periodically delete folders or files using crontab

e.g.

```bash

# Delete target folders weekly on Friday at 1:00AM that older than 60 days
0 1 * * 5  find /mnt/target/ -maxdepth 1 -mindepth 1 -type d -mtime +60 -exec rm -rf {} \;
```

Here you should be careful and use `-maxdepth 1 -mindepth 1` to limit the folders only in the target folder but not include the target folder. Otherwise you'll delete the parent folders. And `-maxdepth` and `-mindepth` should be placed at the front most options.

Note:

>[!IMPORTANT]
>`-maxdepth 1` : means the find can go to 1 level. 0 level means end at target, 1 means end in target folders
>
>`-mindepth 1` : means the find command start from level 1. 0 levle means starts from target, 1 means starts from folders in the target
>
>So `-maxdepth 1 -mindepth 1` only limits the folders in the target but except the target folder
>
>`-type d` : means only limit folders. If you'd like to limit files, replace d with f. Check other types by using `man find`
>
>`-mtime +60` : measn folders older than 60 days
>
>`-exec rm -rf {} \;` : delete the folders that satisfy the above files. you must place *`\;`* at end to end the execution command.


## [Customizing your terminal using OhMyZsh](https://gabrieltanner.org/blog/customizing-terminal-using-ohmyzsh)
此链接解释怎么在terminal 配置ohmyzsh shell.

如果是在Ubuntu中进行修改则需要注意以下几点：
  1. [安装OhMyZsh](https://ohmyz.sh/)，在ternimal输入 : `$ sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"`
  2. 更改 zsh-syntax-highlighting的背景颜色，不然在Dark solarized的背景下看不清：在 `~/.zshrc`中添加 `ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE="fg=yellow"`


## [vim 配置](https://blog.csdn.net/qingdujun/article/details/81411197)

[Another web source that shows the details of `.vimrc`](https://gist.github.com/shreeshga/1405763)
我的vim配置如下：create a .vimrc in the home directory if there's no such file.
```bash
set number
set nocompatible

" hight the syntax
syntax on

set showmode
set autoindent
set smartindent
set expandtab
set tabstop=4
set shiftwidth=4
set softtabstop=4

set showmatch
set hlsearch
set incsearch
set ignorecase

" 自动补全括号
inoremap ' ''<ESC>i
inoremap " ""<ESC>i
inoremap ( ()<ESC>i
inoremap [ []<ESC>i

```

## [How to add file header using vim](https://medium.com/@venumadhav888/how-to-configure-custom-headers-to-the-shell-script-files-automatically-a2d53aa4d0d2)

Using space to align the row instead of tab, otherwise it cannot search the pattern correctly.

```bash
" Add file header
au bufnewfile *.sh 0r ~/.vim/sh_header.temp
au bufnewfile *.py 0r ~/.vim/py_header.temp
autocmd bufnewfile *.sh,*.py exe "1," . 10 . "g/Script Name    :.*/s//Script Name    :".expand("%")
autocmd bufnewfile *.sh,*.py exe "1," . 10 . "g/Creation Date  :.*/s//Creation Date  :".strftime("%Y-%m-%d")
autocmd Bufwritepre,filewritepre  *.sh,*.py exe "1," . 11 . "g/Last Modified  :.*/s//Last Modified  :".strftime("%c")
```

## 如何在Linux系统中管理用户并赋予相应的权限

如果我们写了一些程序想让别人运行，但是并不像让他们对文件进行操作，例如查看，修改。只是让他们运行文件而已。

1. Create a "Group". If you use your account, then when you created your account, a group would also be created with the same name as your username. e.g "appUser"
2. Creat a new user `adduser --ingroup appUser testUser`
3. Use `ls -alh` to show the status of the file. e.g `-rwxr-xr-x 1 user group  21K Oct 21 14:54 utils.py`. Here `-rwxr-xr-x`:
  - if this is a dictionary, then the first letter will be `d`
  - The following three letters(rwx) means the permission of the owner: r(read - 4), w(write - 2), x(executable - 1)
  - The follwoing three letters(r-x) means the permission of the same group has only read and executable permission
  - The following three letters(r-x) means the permission of other users who don't belong to the group will have the permission : read and executable
4. [Change the permission of the folders and files](https://www.pluralsight.com/blog/it-ops/linux-file-permissions#:~:text=To%20change%20directory%20permissions%20for,only%20read%20permission%20for%20everyone.) : `chmod 711 folder` OR `chmod 755 scriptFile`
   - if the folder has the permission 'x' for group, then the group users could use `cd` to go into that folder
   - if the folder has the grouup has 'r' permission, then the group users could use `ls` to show the content in that folder]
   - if you'd like the user use some script, then you need to give the parent folders at least 'x' permission to the group. The parent folders should possess the same permission as the child folders at least.
   - e.g. `drwxr-xr-x` : the group users could use `cd` and `ls`
   - e.g. `drwx--xr-x` : the group users could only use `cd`, cannot use `ls`
5. put the executable scripts in an separate folder. 
6. Do some test to confirm your process is ok


## [Manage a group and user in linux](https://linuxize.com/post/how-to-create-groups-in-linux/)

- show groups : `cat /etc/group`
- show users : `cat /etc/passwd`
- add a group : `sudo groupadd appUser`
- [delete a group](https://linuxize.com/post/how-to-delete-group-in-linux/) : `sudo groupdel appUser`
- display who is a member of a group : `getent group group-name`
- [add a group to a user](https://www.pluralsight.com/blog/tutorials/linux-add-user-command?exp=3) : `sudo usermod -a -G appUser testuser`. You need to login in a user that has the sudo permission.
- [create a new user to a group](https://careerkarma.com/blog/linux-add-user-to-group/#:~:text=You%20can%20add%20a%20user,user%20and%20the%20user's%20username.) : `sudo useradd -g primary_group -G another_group -s /usr/bin/bash -m new_user`. Here `-m` means create a homde directory for new user; `-s` means using specific shell.
- [change passwd for new user](https://linuxize.com/post/how-to-create-users-in-linux-using-the-useradd-command/): `sudo passwd new_user`
- [delete a user](https://linuxize.com/post/how-to-delete-users-in-linux-using-the-userdel-command/) : `userdel username`; `userdel -r username`. Use the `-r (--remove)` option to force userdel to remove the user’s home directory and mail spool
- Create a new user in Linux : `sudo useradd -s /usr/bin/bash -m new_user`


## mkdir

1. `mkdir -p d1/d2/d3 `一次生成多个folder，即使d2,d3不存在，同样可以建立.


## tar

1. `tar  -xf v5.24.1.tar.gz --strip 1 "*/data" "*/environment.yaml"`

`-x` 表示解压， `-f` 表示文件，`--strip 1`表示不重建前1层目录。这一句表示从`v5.24.1.tar.gz` 中提取出`data, environment.yaml`文件，详细的关于tar的解释可见[What does --strip-components -C mean in tar?](https://unix.stackexchange.com/questions/535772/what-does-strip-components-c-mean-in-tar)

`-c`表示`Create a new archive containing the specified items.`，例如使用tar压缩文件时，则可以使用`tar -cf archive.tar a/b/c/FILE`，`a/b/c/FILE` 为想要压缩的文件，`archive.tar`为压缩后的文件名。

列出tar文件中的内容，可以使用`tar -tf archive.tar`

2. To compress a folder using gzip in Linux, you need to use the "tar" command alongside gzip, as the "gzip" command itself can only compress individual files; the typical command is "tar -zcvf archive_name.tar.gz directory_to_compress". 
  > Explanation:
  - "tar": This command is used to archive files and directories. 
  - "-z": This flag tells "tar" to use gzip compression when creating the archive. 
  - "cvf": These flags stand for "create" and "verbose" respectively. 
  - "archive_name.tar.gz": This is the name of the compressed archive file that will be created. 
  - "directory_to_compress": This is the path to the folder you want to compress. 

**Summary**
- `tar  -xf v5.24.1.tar.gz` : 解压文件
- `tar -cf archive.tar a/b/c/FILE` ： 压缩文件
- `tar -zcvf archive_name.tar.gz directory_to_compress` : compress multiple files to gzip which is a good compress format
- `tar -tf archive.tar` ： 列出tar中的文件
- `tar -xfm folder.tar.gz`: -m do not modify the time of the file.


## [find](https://opensource.com/article/18/4/how-use-find-linux)

找到满足一定要求的文件

```bash
find 文件路径 -type f -name 'pattern'
```

- find 后面的第一个参数是文件路径，想从哪一个文件夹中查找文件
- `-type`: 想要查找的类型, `f`: file ; `d`: directory
- `-name`: 想要查找的名称，使用引号括起来。例如想查找python 文件，以 py 结尾的文件。则可以使用"\*.py"

如果后缀中有大小写，那么可以使用`-iname`，表示case-insensitive。

```bash
find 文件路径 -type f -iname '*.jpg'
```

如果想查找多个后缀名称的文件，可以使用or 把多个要查找的文件pattern结合在一起。如下：

```bash
find 文件路径 -type f \( -name '*py' -o -name '*ipynb' \)
```

> 要把多个条件放在括号中。其中前后两端要留有空格，使用反斜杠进行标记。

- `-size`: 查找的文件打下

```bash
k       kilobytes (1024 bytes)
M       megabytes (1024 kilobytes)
G       gigabytes (1024 megabytes)
T       terabytes (1024 gigabytes)
P       petabytes (1024 terabytes)
```

例如查找当前文件夹下文件大小超过1M的文件

```bash
find . -size +1M

find . -size -1M
```
> `+1M`中的+号表明文件大于1M。如果是想查看小于1M的则可以使用`-` 号。

Find specific files with specifc requirements like depth, time, name 

`find "$sourcef"  -maxdepth 1 -mtime +30 -type d -iname 'LB*Run*' -printf "%P\n" | sort`

Note: 
1. `-mtime +30`: older than 30 days
2. `-type d`: find folder. if `-type f` then find files
3. `-iname 'LB*Run*'` : regex search the folder name contain LB and Run Case insensitive
4. `-printf "%P\n"`: each line result




## [awk 的使用](https://linuxize.com/post/awk-command/)

`awk` is a command to process the output of other command, and then extract the interested content.

```bash
cat failed_vcf.txt| awk -F '/' '$1 ~ /09ab/ {print $NF}' | xargs -d '\n' -I{} touch {}
```

1. `-F` define the delimer, here is `/`
2. `$NF` means the last collumn
3. `$1 ~ /09ab/` : regex that the first collum need to contain `09ab`

## download multiple illumina project data through `xargs`

```bash
cat download-list.txt | xargs -d '\n'  -I{} /home/user/Softwares/linux-amd64/icav2 projectdata download {}
```
1. `download-list.txt` contains the folder Id or file ID that you can find in the illumina ICA
2. [`xargs`](https://linuxhint.com/xargs_linux/) can be used to redirect the arguments to other command. Here `-d` define the delimer; `-I{}` define a variable named `{}` that can be used in other command 
3. `/home/user/Softwares/linux-amd64/icav2`: specify the absolute path to the command, if you just use the `icav2`, it cannot work. Even though you use alias and operate the command in bash terminal.

## How to find all files containing specific text (string) on Linux

```bash
grep -rnw '/path/to/somewhere/' -e 'pattern'
```

1. `-r` or `-R` is recursive,
2. `-n` is line number, it will show the line number in the output
3. `-w` stands for match the whole word. if no such parameter, then it may find the pattern within one word. For example, we want to search 'name' in all files. If we use `-w`, then only the world `name` satisfy the requirement. Without this parameter, `hello_name` also satisfy the requierement. 
4. `-l` (lower-case L) can be added to just give the file name of matching files. Don't output the details, just give you the file name that match the pattern
5. `-L` (upper-case L) can be added to just give the file name of `without matching` files. The opposite output files name of `-l`
6. `-i` : ignore case distingctions in patterns.
7. `-e` is the pattern used during the search

```bash
grep -rnwil './' -e 'id'
```


## [What is the difference between the Bash operators `[[` vs `[` vs `(` vs `((`?](https://unix.stackexchange.com/questions/306111/what-is-the-difference-between-the-bash-operators-vs-vs-vs)


## [How to parse bash script arguments](https://www.shellscript.sh/tips/getopt/index.html)

- use `getopt`
- [Resouce 2](https://stackabuse.com/how-to-parse-command-line-arguments-in-bash/)
- `the colon (:) to indicate that an argument expects a value, like "-d=river" rather than a simple "-d."`


```bash
#!/usr/bin/bash
MAG="\e[35m"
RED="31"
GREEN="32"
BOLDGREEN="\e[1;${GREEN}m"
ITALICRED="\e[3;${RED}m"
ERROR="\e[4;3;1;${RED}m"
ENDCOLOR="\e[0m"

WORKFLOW="/scripts/workflow_autoReport.py"
environment='docx'

help()
{
    echo -e "Usage: Render Report from template using tsv format OKR, VCF file and comment database 
            [-h, --help] Show this help message and exit
            [-i, --path_catalog] A csv foramt file that contains the path of VCF and OKR, which has two columns: path_tsv_OKR, path_vcf
            [-t , --name_template] The template Name.
            [-d , --path_database] The path of comment database.
            "
    exit 2
}

SHORT=i:,t:,d:,h
LONG=path_catalog,name_template,path_database,help
OPTS=$(getopt -a -n render_template --options $SHORT --longoptions $LONG -- "$@")

VALID_ARGUMENTS=$# # Returns the count of arguments that are in short or long options

if [ "$VALID_ARGUMENTS" -eq 0 ]; then
  help
fi

eval set -- "$OPTS" 

while :
do
  case "$1" in
    -i | --path_catalog )
      path_catalog="$2"
      shift 2
      ;;
    -t | --name_template )
      name_template="$2"
      shift 2
      ;;
    -d | --path_database)
      path_database="$2"
      shift 2
      ;;
    -h | --help)
      help
      ;;
    --)
      shift;
      break
      ;;
    *)
      echo -e "Unexpected option: $1"
      help
      ;;
  esac
done


[ -z "$path_catalog" ] && echo -e "${ERROR}Please provide the catlog file using the option -i${ENDCOLOR}" && help
[ -z "$name_template" ] && echo -e "${ERROR}Please provide the template name using the option -t${ENDCOLOR}" && help
[ -z "$path_database" ] && echo -e "${ERROR}Please provide the comment database path using the option -d${ENDCOLOR}" && help

echo "path_catalog is : ${path_catalog}, name_template is : $name_template, path_database is : $path_database"

#! go to the folder 

$WORKFLOW -i "$path_catalog" -t "$name_template" -d "$path_database"
```


