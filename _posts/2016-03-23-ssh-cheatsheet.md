---
layout: post
title: SSH Cheatsheet
permalink: /ssh-cheatsheat
published: true
---

## SSH Config 
* Sometimes you have a set of servers that use a shared identity file. By using an ssh config file you can simplify how you access those boxs.  

```bash
touch ~/.ssh.confg
emacs !$

#Default
ControlMaster            auto
ControlPath              /tmp/ssh_mux_%h_%p_%r
ServerAliveInterval      60
ServerAliveCountMax      60

Host *
  Compression            yes
  CompressionLevel        9
  ServerAliveInterval    5
  ServerAliveCountMax    9999999
  IdentitiesOnly          yes

Host *.test *.qa
  User username
  HostName %h.hostname.tld
  IdentityFile "path/to/identity/file"
```
* Now you can connect up simply by doing
```bash
ssh mybox-1.test
ssh mybox-2.test
ssh mybox-1.qa
ssh mybox-2.qa
```
which will build out the host as mybox-1.test.hostname.tld, mybox-1.test.hostname.tld,mybox-1.qa.hostname.tld, mybox-1.qa.hostname.tld  


