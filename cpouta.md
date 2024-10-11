---
title: cPouta
layout: template
filename: cpouta.md
---

Guides for cPouta related stuff.

## Setting up ssh config for cPouta

Create .ssh directory under user directory

Add content:

```
host ANYNAME
	User ubuntu
	Port 22
	HostName IP-ADDRESS
	IdentityFile ~/.ssh/KEY.pem
	
host ANYOTHERNAME
	User ubuntu
	Port 22
	HostName IP-ADDRESS
	IdentityFile ~/.ssh/KEY.pem
	LocalForward 8080 127.0.0.1:8888  # Forward localhost port 8888 from remote machine to 8080 on local machine. Useful for Jupyter notebooks.
```
