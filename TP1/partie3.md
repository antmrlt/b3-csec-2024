# III. MAKE SERVICES GREAT AGAIN

## 1. Restart automatique

Bon pour ça, facile, on va juste faire en sorte que si le programme coupe, il soit relancé automatiquement.

🌞 **Ajoutez une clause dans le fichier `efrei_server.service` pour le restart automatique**

```conf
[Unit]
Description=Super serveur EFREI

[Service]
Type=daemon
ExecStart=/usr/local/bin/efrei_app
Environment=LISTEN_ADDRESS=192.168.56.114

User=antna
Group=antna

Restart=always
```

🌞 **Testez que ça fonctionne**

```
[antna@localhost ~]$ ps -e | grep efrei_app
  13435 ?        00:00:00 efrei_app
```

```
[antna@localhost ~]$ kill 13435
```

```
[antna@localhost ~]$ ps -e | grep efrei_app
  13462 ?        00:00:00 efrei_app
```

## 2. Utilisateur applicatif

Lorsqu'un programme s'exécute sur une machine (peu importe l'OS ou le contexte), le programme est **toujours** exécuté sous l'identité d'un utilisateur.  
Ainsi, pendant son exécution, le programme aura les droits de cet utilisateur.  

> Par exemple, un programme lancé en tant que `toto` pourra lire un fichier `/var/log/toto.log` uniquement si l'utilisateur `toto` a les droits sur ce fichier.

🌞 **Créer un utilisateur applicatif**

```
[antna@localhost ~]$ sudo useradd app_user
```

🌞 **Modifier le service pour que ce nouvel utilisateur lance le programme `efrei_server`**

```
User=app_user
Group=app_user
```

🌞 **Vérifier que le programme s'exécute bien sous l'identité de ce nouvel utilisateur**

```
[antna@localhost ~]$ ps aux | grep efrei_app
efreius+   13916  0.2  0.1   2956  1884 ?        Ss   09:22   0:00 /usr/local/bin/efrei_app
efreius+   13918  0.1  1.4  33772 25984 ?        S    09:22   0:00 /usr/local/bin/efrei_app
antna      13924  0.0  0.1   6408  2176 pts/0    S+   09:22   0:00 grep --color=auto efrei_app
```

## 3. Maîtrisez l'emplacement des fichiers

🌞 **Choisir l'emplacement du fichier de logs**

```
[antna@localhost ~]$ sudo mkdir /var/log/efreiapp
```

```bash
Environment="LOG_DIR=/var/log/efreiapp/"
```

🌞 **Maîtriser les permissions du fichier de logs**

```cmd
[antna@localhost bin]$ sudo mkdir /var/log/efreiapp
[antna@localhost bin]$ sudo chown efreiuser:efreiuser /var/log/efreiapp/
[antna@localhost bin]$ ls -al /var/log/efreiapp/
total 8
drwxr-xr-x. 2 efreiuser efreiuser   24 Sep 10 10:27 .
drwxr-xr-x. 8 root      root      4096 Sep 10 10:25 ..
-rw-r--r--. 1 efreiuser efreiuser   60 Sep 10 10:27 server.log
```

## 4. Security hardening

Il existe beaucoup de clauses qu'on peut ajouter dans un fichier `.service` pour que *systemd* s'occupe de sécuriser le service, en l'isolant du reste du système par exemple.

Ainsi, une commande est fournie `systemd-analyze security` qui permet de voir quelles mesures de sécurité on a activé. Un score (un peu arbitraire) est attribué au *service* ; cela représente son "niveau de sécurité".

Cette commande est **très** pratique d'un point de vue pédagogique : elle va vous montrer toutes les clauses qu'on peut ajouter dans un `.service` pour renforcer sa sécurité.

🌞 **Modifier le `.service` pour augmenter son niveau de sécurité**

```
[Unit]
Description=Super serveur EFREI
After=network.target

[Service]
ExecStart=/usr/local/bin/efrei_app
Environment=LISTEN_ADDRESS=192.168.56.114
Environment=LOG_DIR=/var/log/efreiapp

# Security: Restrict capabilities and privileges
NoNewPrivileges=true
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
ProtectSystem=full
ProtectHome=true
PrivateTmp=true
ReadOnlyPaths=/usr/local/bin/efrei_app
ReadWritePaths=/var/log/efreiapp
ProtectKernelModules=true
ProtectControlGroups=true
ProtectKernelTunables=true
RestrictAddressFamilies=AF_INET AF_INET6 AF_UNIX
PrivateDevices=true
LockPersonality=true

LimitNOFILE=1024
LimitNPROC=256

Restart=always
RestartSec=5s
TimeoutStopSec=30s

User=efreiuser
Group=efreiuser

[Install]
WantedBy=multi-user.target
```

🌟 **BONUS : Essayez d'avoir le score le plus haut avec `systemd-analyze security`**

4.6 la team : `efrei_server.service                      4.6 OK        🙂`

> ➜ [**Lien vers la partie 4**](./part4.md)
