---
layout: post
title: TIP - port scanning
categories: netcat
---

TLDR:

* Linux: `nc -vz ip port`
* Powershell: `tnc ip -port port`.

---

From the [nc man page]()

It may be useful to know which ports are open and running services on a target machine. The -z flag can be used to tell nc to report open ports, rather than initiate a connection. For example:

```
$ nc -z host.example.com 20-30
Connection to host.example.com 22 port [tcp/ssh] succeeded!
Connection to host.example.com 25 port [tcp/smtp] succeeded!
```

The port range was specified to limit the search to ports 20 - 30.
