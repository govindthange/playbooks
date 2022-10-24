# How to find a process/service listening on a particular port?

```
govind@thinkpad:~/projects/de-chess$ sudo fuser 3000/tcp
3000/tcp:            19353 19359
govind@thinkpad:~/projects/de-chess$ ps aux | grep 19353
root       19353  0.0  0.0 1224304 3788 ?        SNl  08:56   0:00 /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 3000 -container-ip 172.19.0.4 -container-port 3000
govind    267423  0.0  0.0  18808  2472 pts/0    S<+  15:36   0:00 grep --color=auto 19353
```
