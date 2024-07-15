> Waiting is unbearable, debugging passes the time.

Sometimes you update your DNS zone/records, and wait for it propagates...

![image](https://github.com/user-attachments/assets/8e340f59-8304-4a52-a9fa-9dedb0b689ed)

But you can actually debug it with `dig`:

```sh
$ dig pmnd.rs

; <<>> DiG 9.10.6 <<>> pmnd.rs
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 31071
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;pmnd.rs.			IN	A

;; ANSWER SECTION:
pmnd.rs.		1066	IN	A	76.76.21.9
pmnd.rs.		1066	IN	A	76.76.21.61

;; Query time: 43 msec
;; SERVER: 2a01:cb01:820:971::24#53(2a01:cb01:820:971::24)
;; WHEN: Mon Jul 15 11:45:57 CEST 2024
;; MSG SIZE  rcvd: 57
```

This is great...

## DNS server cache -- how to avoid it

Though, the only drawback is, as you query the domain with `dig`, your internet provider DNS server, will eventually cache the response...

<img width="827" alt="image" src="https://github.com/user-attachments/assets/e74bc33e-1693-42f8-b6c4-d2a84f519780">

So install your own DNS server:

```sh
$ brew install dnsmasq
$ sudo code /opt/homebrew/etc/dnsmasq.conf
```

and change:

```
listen-address=127.0.0.1
cache-size=0
```

start or restart dnsmasq:

```
$ sudo brew services start dnsmasq
```

then point to it while `dig`ging:

```sh
$ dig @127.0.0.1 pmnd.rs

; <<>> DiG 9.10.6 <<>> @127.0.0.1 pmnd.rs
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 35947
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;pmnd.rs.			IN	A

;; ANSWER SECTION:
pmnd.rs.		1030	IN	A	76.76.21.9
pmnd.rs.		1030	IN	A	76.76.21.61

;; Query time: 8 msec
;; SERVER: 127.0.0.1#53(127.0.0.1)
;; WHEN: Mon Jul 15 11:46:33 CEST 2024
;; MSG SIZE  rcvd: 57
```

🙌🏻 the response is guaranteed to be fresh: see the `<<>> @127.0.0.1`!
