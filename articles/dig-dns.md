Sometimes you update your DNS zone/records, and wait for it propagates...

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

---

Though, the only drawback is, as you query the domain, your internet provider DNS server, will eventually cache the response...

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
