Sometimes you update your DNS zone/records, and wait for it propagates...

But you can actually debug it with `dig`:

```sh
$ dig pmnd.rs

; <<>> DiG 9.10.6 <<>> @127.0.0.1 pmnd.rs
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 27036
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;pmnd.rs.			IN	A

;; ANSWER SECTION:
pmnd.rs.		1800	IN	A	76.76.21.123
pmnd.rs.		1800	IN	A	76.76.21.98

;; Query time: 150 msec
;; SERVER: 127.0.0.1#53(127.0.0.1)
;; WHEN: Mon Jul 15 11:33:43 CEST 2024
;; MSG SIZE  rcvd: 68
```

The only drawback is, as you query the domain, your internet provider DNS server, will eventually cache the response...

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
```

🙌🏻 the response is guaranteed to be fresh!
