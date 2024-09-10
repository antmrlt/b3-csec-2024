# TP1 : Héberger un service 

## Partie 0

🌞 **Boom ça commence direct : je veux l'état initial du firewall**

- dans le compte-rendu, une commande qui me montre l'état initial de votre firewall (qui ne doit autoriser que le trafic SSH en entrée)

```cmd
[antna@localhost ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: ssh
  ports:
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

➜ **Gestion d'utilisateurs**

- la machine doit posséder un user qui porte votre prénom ou pseudo ou truc du genre
- il est interdit d'utiliser `root` directement
- si besoin des privilèges de `root`, vous utiliserez exclusivement la commande `sudo`

🌞 **Fichiers /etc/sudoers /etc/passwd /etc/group** dans le dépôt de compte-rendu svp !

sudoers file
```
## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       ALL
```

passwd file
```
antna:x:1000:1000:antna:/home/antna:/bin/bash
```

group file
```
wheel:x:10:antna
```