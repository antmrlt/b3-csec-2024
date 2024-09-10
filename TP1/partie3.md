# III. MAKE SERVICES GREAT AGAIN

## 1. Restart automatique

Bon pour ça, facile, on va juste faire en sorte que si le programme coupe, il soit relancé automatiquement.

🌞 **Ajoutez une clause dans le fichier `efrei_server.service` pour le restart automatique**

- c'est la clause `Restart=`
- trouvez la valeur adaptée pour qu'il redémarre tout le temps, dès qu'il est coupé

🌞 **Testez que ça fonctionne**

- lancez le *service* avec une commande `systemctl`
- affichez le processus lancé par *systemd* avec une commande `ps`
  - je veux que vous utilisiez une commande avec `| grep quelquechose` pour n'afficher que la ligne qui nous intéresse
  - vous devriez voir un processus `efrei_server` qui s'exécute
- tuez le processus manuellement avec une commande `kill`
- constatez que :
  - le service a bien été relancé
  - il y a bien un nouveau processus `efrei_server` qui s'exécute

> Pour rappel, **TOUTES** les commandes pour faire ce qui est demandé avec un 🌞 doivent figurer dans le compte-rendu.

## 2. Utilisateur applicatif

Lorsqu'un programme s'exécute sur une machine (peu importe l'OS ou le contexte), le programme est **toujours** exécuté sous l'identité d'un utilisateur.  
Ainsi, pendant son exécution, le programme aura les droits de cet utilisateur.  

> Par exemple, un programme lancé en tant que `toto` pourra lire un fichier `/var/log/toto.log` uniquement si l'utilisateur `toto` a les droits sur ce fichier.

🌞 **Créer un utilisateur applicatif**

- c'est lui qui lancera `efrei_server`
- avec une commande `useradd`
- choisissez...
  - un nom approprié
  - un homedir approprié
  - un shell approprié

> N'hésitez pas à venir vers moi pour discuter de ce qui est le plus "approprié" si nécessaire.

🌞 **Modifier le service pour que ce nouvel utilisateur lance le programme `efrei_server`**

- je vous laisse chercher la clause appropriée à ajouter dans le fichier `.service`

🌞 **Vérifier que le programme s'exécute bien sous l'identité de ce nouvel utilisateur**

- avec une commande `ps`
- encore là, filtrez la sortie avec un `| grep`
- n'oubliez pas de redémarrer le service pour que ça prenne effet hein !

> *Déjà à ce stade, le programme a des droits vraiment limités sur le système.*

## 3. Maîtrisez l'emplacement des fichiers

Pour fonctionner, l'application a besoin de deux choses :

- des **variables d'environnement définies**, ou des valeurs par défaut nulles seront utilisées
- un **fichier de log** où elle peut écrire
  - par défaut elle écrit dans `/tmp` comme l'indique le warning au lancement de l'application
  - vous pouvez définir la variable `LOG_DIR` pour choisir l'emplacement du fichier de logs

🌞 **Choisir l'emplacement du fichier de logs**

- créez un dossier dédié dans `/var/log/` (le dossier standard pour stocker les logs)
- indiquez votre nouveau dossier de log à l'application avec la variable `LOG_DIR`
- l'application créera un fichier `server.log` à l'intérieur

🌞 **Maîtriser les permissions du fichier de logs**

- avec les commandes `chown` et `chmod`
- appliquez les permissions les plus restrictives possibles sur le dossier dans `var/log/`

![chown chmod](./img/chown-chmod-2.webp)

## 4. Security hardening

Il existe beaucoup de clauses qu'on peut ajouter dans un fichier `.service` pour que *systemd* s'occupe de sécuriser le service, en l'isolant du reste du système par exemple.

Ainsi, une commande est fournie `systemd-analyze security` qui permet de voir quelles mesures de sécurité on a activé. Un score (un peu arbitraire) est attribué au *service* ; cela représente son "niveau de sécurité".

Cette commande est **très** pratique d'un point de vue pédagogique : elle va vous montrer toutes les clauses qu'on peut ajouter dans un `.service` pour renforcer sa sécurité.

🌞 **Modifier le `.service` pour augmenter son niveau de sécurité**

- ajoutez au moins 5 clauses dans le fichier pour augmenter le niveau de sécurité de l'application
- n'utilisez que des clauses que vous comprenez, useless sinon

🌟 **BONUS : Essayez d'avoir le score le plus haut avec `systemd-analyze security`**

➜ 💡💡💡 **A ce stade, vous pouvez ré-essayez l'injection que vous avez trouvé dans la partie 1. Normalement, on peut faire déjà moins de trucs avec.**

> ➜ [**Lien vers la partie 4**](./part4.md)
