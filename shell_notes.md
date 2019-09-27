# Shell notes
## Execute scripts
1. Define interpreter by shebang in the begining of the scripts: !# /bin/sh. Need to grant the execute permission to the script.
2. Shell will fork a thread to execute the script. Using (), does not need x permission, and this is executed in a sub-thread which does not affect main thread, e.g. 
```bash
(cd; ls - l)
```

## Basic syntax
1. Environment variable: can be passed to child process from parent process. printenv to check env var.
2. Local variable: only affect current shell process, not affect parent process. Use command export can set local var to env var, e.g. 
```bash
export VARNAME=value
```
Use unset command to remove env var or local var:
```bash
unset VARNAME
```
Get value of a var
```bash
${VARNAME}
$VARNAME  # if no ambiguity
```
3. Globbing: \*, ?, \[\]
**File**:
```bash
ls /dev/ttyS*
```
**Command**: \`\`, the command inside \`\` will be computed and return a value first
```bash
DATA=`date`
```
**Arithematic**: $(()) or $[]
```bash
echo $((45+3))
echo $[2+11]
```
**apostrophe**: '', indicate string.
**Double quotes**: "", indicate string. 防止通配符扩展， 但允许变量扩展，这点与单引号的处理方式不同。
4. Input and output of shell:
**echo**: display txt or var, or pass a string into file.
```bash
echo "test"
echo "test" >> aa.txt  # append test to file aa.txt
```
5. **Pipeline** |
6. **tee**: output the result to stdout and output a copy of result to a file.
```bash
dk -k | awk '{print $1}' | grep -v 'file system' | tee a.txt
```

## Regex
1. grep / egrep: 
- match char: ". [] - ^ [[:xxx:]]", ^ inside t [] means not match a specific char; 
- number of matches: "? + * {N} {N,} {,M} {N,M}"
- match position: "^ $ \< \> \b \B", \< match start of a word e.g. \<th; \> match end of a word, e.g. p\> can match 'leap'; \b match start or end of a word, e.g. \bat\b; \B match NOT start and end of a word, e.g. \Bat\B.
- other: "\ () |", \ escapes, \(\) 将regex的一部分括起来表示一个单元，可以对整个单元使用数量限定, e.g. ([0-9]{1,3}\.){3}[0-9]{1,3} match IP

## grep
e.g.
```bash
grep ‘test’ d*
grep ‘test’ aa bb cc
grep ‘[a-z]\{5\}’ aa
grep ‘w\(es\)t.*\1′ aa  #如果west被匹配，则es就被存储到内存中，并标记为1，然后搜索任意个字符(.*)
grep -i pattern files #不区分大小写地搜索。默认情况区分大小写，
grep -l pattern files #只列出匹配的文件名，
grep -L pattern files #列出不匹配的文件名，
grep -w pattern files #只匹配整个单词，而不是字符串的一部分(如匹配’magic’，而不是’magical’)，
grep pattern1 | pattern2 files #显示匹配 pattern1 或 pattern2 的行，
grep pattern1 files | grep pattern2 #显示既匹配 pattern1 又匹配 pattern2 的行。
grep -n pattern files  #显示行号信息
grep -c pattern files  #查找总行数
```

## find
To search files
Usage: find pathname -options [-print -exec -ok ...]. 
\-print: output res to stdout, 
\-exec: find命令对匹配的文件执行该参数所给出的shell命令, e.g. 
```bash
find . -type f -exec ls -l {} \;
find logs -type f -mtime +5 -exec rm {} \;
```
\-ok: 和-exec的作用相同，只不过以一种更为安全的模式来执行该参数所给出的shell命令，在执行每一个命令之前，都会给出提示，让用户来确定是否执行。
examples:
```bash
find ~ -name "*.txt" -print
find . -name "*.txt" -print
find . -perm 755 -print
find /apps -path "/apps/bin" -prune -o -print  # ignore "/apps/bin"
find /home \( -path /home/itcast/f1 -o -path /home/itcast/f2 \) -prune -o -print
find ~ -user itcast -print
find /home -nouser -print
find /apps -group itcast -print
find / -nogroup -print
find / -mtime -5 -print
find /var/adm -mtime +3 -print
find /etc -type d -print
find . -size +1000000c -print
find . -name "*.XC" -mount -print  # 在当前的文件系统中查找文件（不进入其他文件系统）
find . -perm -7 -print | xargs chmod o-w  # xargs is similar to -exec, but process the result by batch/chunck
```

## sed
Stream Editor. 把前一个程序的输出引入sed的输入，经过一系列编辑命令转换为另一种格式输出。
```bash
# Usage
sed option 'script' file1 file2 ...
sed option -f scriptfile file1 file2 ...
# common option
/pattern/p  # 打印匹配pattern的行
/pattern/d  # 删除匹配pattern的行
s/pattern1/pattern2/   # 查找符合pattern的行，将该行第一个匹配pattern1的字符串替换为pattern2
s/pattern1/pattern2/g  # 查找符合pattern的行，将该行所有匹配pattern1的字符串替换为pattern2
# examples
sed "2,5d" testfile
sed 's/bc/-&-/' testfile
```

## awk
Can process a file by col or by row. 

## Linux core commands
![Linux_core_commands](http://www.brendangregg.com/perf_events/perf_events_map.png)
Text process:
    wc: word count
    tr: convert or del char
    sort: sort [option] file
    uniq: rm duplications

## Linux directories
- /bin, /usr/bin: executable binary files, e.g. ls、tar、mv、cat
- /boot: files used in boot the sys, e.g. linux kernel: /boot/vmlinuz, sys boot management: /boot/grub
- /dev: store device files, access the files inside is accessing a device.
- /etc: sys config files. **Don't store executable files in this dir.** Important config files: /etc/inittab、/etc/fstab、/etc/init.d、/etc/X11、/etc/sysconfig、/etc/xinetd.d
- /lib, /usr/lib, /usr/local/lib: func catlog using by the sys. 
- /lost+fount: store missing fragments when sys error
- /mnt, /media: default mount location of disc
- /otp: dir for installing additional software for the host
- /proc：此目录的数据都在内存中，如系统核心，外部设备，网络状态，由于数据都存放于内存中，所以不占用磁盘空间，比较重要的目录有 /proc/cpuinfo、/proc/interrupts、/proc/dma、/proc/ioports、/proc/net/* 
- /sbin,/usr/sbin,/usr/local/sbin: store commands that only sys admin can use
- /tmp: temp files when a program is executing. **Import data should not be store in this folder**
- /usr: import dirs: /usr/bin, /usr/share, /usr/lib: funcs cannot execute directly but will be used when a program is running, /usr/local: software update packages.
- /var: store frequently changing files. /var/log，/var/log/message, etc.

## Useful and unfamiliar commands
### Files and disk management
**ln**: create link
```bash
ln src_file target_file
```
**tar**: archive. options: -[cvtxf], -f should be the last param
```bash
tar -cvf test.tar *
tar -xvf test.tar
tar -zcvf test.tar.gz *
tar -zxvf test.tar.gz
tar -zxvf test.tar.gz -C ../
tar -jcvf test.tar.gz *
tar -jxvf test.tar.gz  # biz2 compression
```
**gzip**: usage: gizp -[dr] file
**zip, unzip**
**which**

### User management
- who
- exit
- useradd: -[mg], create dir/assign group
- passwd
- userdel
- groupadd, groupdel
- usermod
- 新创建的用户，默认不能sudo, 需要
```bash
sudo usermod -a -G adm username
sudo usermod -a -G sudo username
```
- chmod
```bash
chmod u/g/o/a +/-/= rwx
chmod u=rwx,g=rx,o=r filename
chmod 751 filename
```
- chown, chgrp

### Sys management
- ps: check process. -[auxwr]
- kill: usage: kill [-signal] pid
- df, du: disk usage
- ifconfig