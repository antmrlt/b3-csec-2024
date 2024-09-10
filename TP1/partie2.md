# II. Servicer le programme

## 1. Création du service

🌞 **Créer un service `efrei_server.service`**

`[antna@localhost ~]$ sudo nano /etc/systemd/system/efrei_server.service`

```
[Unit]
Description=Super serveur EFREI

[Service]
ExecStart=/usr/local/bin/efrei_app
Environment=LISTEN_ADDRESS=192.168.56.114
```

➜ **Une fois le fichier `/etc/systemd/system/efrei_server.service` créé** :

```
[antna@localhost ~]$ sudo systemctl daemon-reload
```

## 2. Tests

🌞 **Exécuter la commande `systemctl status efrei_server`**

```
[antna@localhost ~]$ systemctl status efrei_server
○ efrei_server.service - Super serveur EFREI
     Loaded: loaded (/etc/systemd/system/efrei_server.service; static)
     Active: inactive (dead)
```

🌞 **Démarrer le service**

```cmd
[antna@localhost ~]$ sudo systemctl status efrei_server
● efrei_server.service - Super serveur EFREI
     Loaded: loaded (/etc/systemd/system/efrei_server.service; static)
     Active: active (running) since Tue 2024-09-10 08:11:33 EDT; 3s ago
   Main PID: 13262 (efrei_app)
      Tasks: 2 (limit: 11099)
     Memory: 32.5M
        CPU: 62ms
     CGroup: /system.slice/efrei_server.service
             ├─13262 /home/antna/efrei_app
             └─13263 /home/antna/efrei_app

Sep 10 08:11:33 localhost.localdomain systemd[1]: Started Super serveur EFREI.
```

🌞 **Vérifier que le programme tourne correctement**

```
[antna@localhost ~]$ ss -tuln | grep 8888
tcp   LISTEN 0      100    192.168.56.114:8888      0.0.0.0:*
```

> ➜ [**Lien vers la partie 3**](./part3.md)

