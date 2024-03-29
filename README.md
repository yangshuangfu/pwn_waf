# AWD PWN WAF

## Intro

CTF AWD  WAF FOR PWN

Author: I0gan

QQ: 418894113

## Fix Log
    2021-05-13: Thancks b0ldfrev commit the IO blocking problem for FORWARD mode 


## RUN_CATCH / RUN_I0GAN MODE WAF Principle

Execve the target elf file by creating a child process, and then the parent process uses ptrace to monitor the syscall  of the child process. If the standard IO is used, the data  is read and recorded in the log. If the syscall is dangerous, it is also recorded in the log



## RUN_FORWARD MODE WAF Principle

Capture traffic from standard I / O and forward it to the target service.



## Code Tree

```
src
├── logger.c
├── logger.h
├── test_pwn.c
├── waf.c
└── waf.h
```



##  How to use

### 0x01 config

In makefile

```makefile
# configure
LOG_PATH    := /tmp/.i0gan # Set your own log path
ARCH        := 64
SERVER_IP   := 127.0.0.1 # RUN_FORWARD used
SERVER_PORT := 9090      # RUN_FORWARD used
```

You can set your own base configure information.



### 0x02 Compile

#### Compile all

```
make # or make all
```

You will get `catch`,`i0gan`,`redir` files

The `catch` is RUN_CATCH MODE waf program

The `i0gan` is RUN_I0GAN MODE waf program

The `forward` is RUN_FORWARD MODE waf program

#### Compile RUN_CATCH mode

```
make catch
```

#### Compile RUN_I0GAN mode

```
make i0gan
```

Compile RUN_FORWARD mode

```
make forward
```



### 0x03

After compiling all successfully, you can run `./catch`  or ` ./i0gan`  directly to test
Store the interactive log file in the directory `/tmp/.i0gan/` , The name of file format is  `time + hex microseconds + .i0gan`



### 0x04

Upload the compiled `catch/i0gan/forward`  program file to the `/tmp` directory. The `pwn service paths` stored in different events may be different. Take `/pwn/pwn` as an example. If you don't know where it is, `cat /etc/xinetd.d/pwn` service can view its own service path.  Copy the monitored service program to `LOG_PATH` directory, and replace the service program with WAF program

```
mkdir /tmp/.i0gan          # Create a directory .i0gan in /tmp
chmod 777 /tmp/.i0gan      # Modify permissions
cp /pwn/pwn /tmp/.i0gan    # Copy service binary program to /tmp/.i0gan dirctory
cp /tmp/catch /pwn/pwn # Replace your service binary program
```

If the attacker attacks, the corresponding attack log file will be generated in the directory `/tmp/.i0gan/`. Each attack will generate a file, which can be directly analyzed after being attacked



## RUN_CATCH MODE Test

### Interaction

```
┌[logan☮arch]-(~/disk2/github/i0gan_waf)-[git://main ✗]-
└> ./catch 
Test puts:
Test write�
Test read:
AAAABBBB
Test gets:

CCCCDDDD
Test system:

sh-5.0$ ls
catch  forward  i0gan  makefile  README.md  src  test_pwn  waf.pro  waf.pro.user
sh-5.0$ exit
exit
```



### Log

```
// Date: 2020-12-22 00:24:08
// Mode: RUN_CATCH
// CTF AWD I0GAN WAF
// Powered By I0gan

<-------------------- write ----------------->
Test puts:
Test write�
Test read:

w_0 = "\x54\x65\x73\x74\x20\x70\x75\x74\x73\x3a\x0a\x54\x65\x73\x74\x20\x77\x72\x69\x74\x65\x00\x01\x02\x03\xff\x0a\x54\x65\x73\x74\x20\x72\x65\x61\x64\x3a\x0a"

<-------------------- read ------------------>
AAAABBBB

r_0 = "\x41\x41\x41\x41\x42\x42\x42\x42\x0a"

<-------------------- write ----------------->
Test gets:


w_1 = "\x54\x65\x73\x74\x20\x67\x65\x74\x73\x3a\x0a\x0a"

<-------------------- read ------------------>
CCCCDDDD

r_1 = "\x43\x43\x43\x43\x44\x44\x44\x44\x0a"

<-------------------- write ----------------->
Test system:


<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
<-------------- dangerous syscall------------>
w_2 = "\x54\x65\x73\x74\x20\x73\x79\x73\x74\x65\x6d\x3a\x0a\x0a"
```

If there is a string of words "-------------- dangerous syscall------------", basically your server has been hacked!





## RUN_I0GAN MODE Test

### Interaction

```
┌[logan☮arch]-(~/disk2/github/i0gan_waf)-[git://main ✗]-
└> ./i0gan 
Test puts:
Test write�
Test read:
aaajaksdjf   
Test gets:

asjdfjadsfasd
Test system:

ls
jasjjd
^C
sh: initialize_job_control: no job control in background: Bad file descriptor 
```

### Log

```
// Date: 2020-12-22 00:25:55
// Mode: RUN_I0GAN
// CTF AWD I0GAN WAF
// Powered By I0gan

<-------------------- write ----------------->
Test puts:
Test write�
Test read:

w_0 = "\x54\x65\x73\x74\x20\x70\x75\x74\x73\x3a\x0a\x54\x65\x73\x74\x20\x77\x72\x69\x74\x65\x00\x01\x02\x03\xff\x0a\x54\x65\x73\x74\x20\x72\x65\x61\x64\x3a\x0a"

<-------------------- read ------------------>
aaajaksdjf

r_0 = "\x61\x61\x61\x6a\x61\x6b\x73\x64\x6a\x66\x0a"

<-------------------- write ----------------->
Test gets:


w_1 = "\x54\x65\x73\x74\x20\x67\x65\x74\x73\x3a\x0a\x0a"

<-------------------- read ------------------>
asjdfjadsfasd

r_1 = "\x61\x73\x6a\x64\x66\x6a\x61\x64\x73\x66\x61\x73\x64\x0a"

<-------------------- write ----------------->
Test system:


<-------------- dangerous syscall------------>
w_2 = "\x54\x65\x73\x74\x20\x73\x79\x73\x74\x65\x6d\x3a\x0a\x0a"
```



### RUN_FORWARD MODE Test

### Interaction

```
┌[logan☮arch]-(~/disk2/github/i0gan_waf)-[git://main ✗]-
└> ./forward 
asdfjadsf
HTTP/1.1 400 Bad Request
Content-Type: text/plain; charset=utf-8
Connection: close

400 Bad Request
```

### Log

```
// Date: 2020-12-22 00:27:18
// Mode: RUN_FORWARD
// CTF AWD I0GAN WAF
// Powered By I0gan

<-------------------- read ------------------>
asdfjadsf

r_0 = "\x61\x73\x64\x66\x6a\x61\x64\x73\x66\x0a"

<-------------------- write ----------------->
HTTP/1.1 400 Bad Request
Content-Type: text/plain; charset=utf-8
Connection: close

400 Bad Request
w_0 = "\x48\x54\x54\x50\x2f\x31\x2e\x31\x20\x34\x30\x30\x20\x42\x61\x64\x20\x52\x65\x71\x75\x65\x73\x74\x0d\x0a\x43\x6f\x6e\x74\x65\x6e\x74\x2d\x54\x79\x70\x65\x3a\x20\x74\x65\x78\x74\x2f\x70\x6c\x61\x69\x6e\x3b\x20\x63\x68\x61\x72\x73\x65\x74\x3d\x75\x74\x66\x2d\x38\x0d\x0a\x43\x6f\x6e\x6e\x65\x63\x74\x69\x6f\x6e\x3a\x20\x63\x6c\x6f\x73\x65\x0d\x0a\x0d\x0a\x34\x30\x30\x20\x42\x61\x64\x20\x52\x65\x71\x75\x65\x73\x74"
```

