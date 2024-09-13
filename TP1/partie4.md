# Partie 4 : Autour de l'application

🌞 **Configurer de façon robuste le firewall**

```
firewall-cmd --permanent --new-policy myOutputPolicy

firewall-cmd --permanent --policy myOutputPolicy --add-ingress-zone HOST

firewall-cmd --permanent --policy myOutputPolicy --add-egress-zone ANY

firewall-cmd --permanent --policy myOutputPolicy --set-target DROP
```

🌞 **Prouver que la configuration est effective**

```
[antna@localhost ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: ssh
  ports: 8888/tcp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
  ```

```
[antna@localhost ~]$ ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
^C
--- 1.1.1.1 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2079ms
```

## 2. Protéger l'app contre le flood

🌞 **Installer fail2ban sur la machine**

```
[antna@localhost ~]$ sudo systemctl start fail2ban
[antna@localhost ~]$ sudo systemctl status fail2ban
● fail2ban.service - Fail2Ban Service
     Loaded: loaded (/usr/lib/systemd/system/fail2ban.service; disabled; preset: disabled)
     Active: active (running) since Tue 2024-09-10 11:21:06 EDT; 9s ago
       Docs: man:fail2ban(1)
    Process: 16388 ExecStartPre=/bin/mkdir -p /run/fail2ban (code=exited, status=0/SUCCESS)
   Main PID: 16389 (fail2ban-server)
      Tasks: 3 (limit: 11099)
     Memory: 14.2M
        CPU: 48ms
     CGroup: /system.slice/fail2ban.service
             └─16389 /usr/bin/python3 -s /usr/bin/fail2ban-server -xf start

Sep 10 11:21:06 localhost.localdomain systemd[1]: Starting Fail2Ban Service...
Sep 10 11:21:06 localhost.localdomain systemd[1]: Started Fail2Ban Service.
Sep 10 11:21:06 localhost.localdomain fail2ban-server[16389]: Server ready
```

🌞 **Ajouter une *jail* fail2ban**

- elle doit lire le fichier de log du service, que vous avez normalement placé dans `/var/log/`
- repérer la ligne de connexion d'un client
- blacklist à l'aide du firewall l'IP de ce client

Dans le fichier `/etc/fail2ban/jail.local` :
```
[efrei_server]
enabled = true
port    = 8888
filter  = efrei_server
logpath = /var/log/efreiapp/server.log
maxretry = 3
bantime = 3600
findtime = 600
```

Et dans le fichier ``/etc/fail2ban/filter.d/efrei_server.conf`
```
[Definition]
failregex = \[\d+\.\d+\] Received '.*' from \('<HOST>', \d+\)
ignoreregex =
```

🌞 **Vérifier que ça fonctionne !**

```
[antna@localhost ~]$ sudo fail2ban-client status efrei_server
Status for the jail: efrei_server
|- Filter
|  |- Currently failed: 1
|  |- Total failed:     38
|  `- File list:        /var/log/efreiapp/server.log
`- Actions
   |- Currently banned: 1
   |- Total banned:     1
   `- Banned IP list:   192.168.56.115
```

## 3. Empêcher le programme de faire des actions indésirables

🌞 **Ajouter une politique seccomp au fichier `.service`**

J'ai add ça au file `efrei_server.service`
```
# Syscall
SystemCallFilter=@default @network write openat newfstatat rt_sigaction close mprotect read pread64 futex brk dup bind socket listen setsockopt gettid getpid epoll_wait
SystemCallFilter=~clone fork execve
```

