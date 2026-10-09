# TP - Pfsense -Becquet Arthur

# **Contexte :**

Dans ce TP nous allons mettre en place un “pfsense” avec une interface WAN, un LAN pour l’administration et une DMZ sur laquelle on ajoutera un serveur apache ainsi qu’une machine avec un accès bureau a distance

![Capture d'écran 2026-10-07 155359.png](Capture_dcran_2026-10-07_155359.png)

# A) Installation du pfsense :

1. **Les près requis :**

Dans un premier temps, nous allons télécharger l’iso pour une machine “pfsense” et allons créer une VM avec.

![Capture d'écran 2026-10-07 155921.png](Capture_dcran_2026-10-07_155921.png)

Attention, il ne faut pas oublier d’ajouter les trois carte réseau sur la VM (une pour le WAN, une pour le LAN et une pour la DMZ)

![Capture d'écran 2026-10-07 160152.png](Capture_dcran_2026-10-07_160152.png)

1. **L’installation :**

La partie la plus importante de cette procédure est l’affectation des cartes réseaux

- Pour la WAN rien de spéciale n’est a faire, seul le choix entre IP DHCP et statique est a faire
    
    ![Capture d'écran 2026-10-07 161028.png](Capture_dcran_2026-10-07_161028.png)
    
- En revanche pour le LAN il faut  différencier l’adresse IP de celle du WAN car sinon le DHCP sera diffuser sur votre réseau et causera des conflit d’adresse

![Capture d'écran 2026-10-07 161534.png](Capture_dcran_2026-10-07_161534.png)

![Capture d'écran 2026-10-07 161613.png](Capture_dcran_2026-10-07_161613.png)

- En revanche la DMZ n’a pas besoin d’être configurer pour le moment

**TEST du pfsense :**

Sur une machine connecter a la carte réseau LAN et sur la même adresse réseau que la zone LAN du pfsense, nous allons nous y connecter depuis un navigateur. (Les identifiants de base sont admin/pfsense

![Capture d'écran 2026-10-07 162258.png](Capture_dcran_2026-10-07_162258.png)

 

# **B) Mise en place de la DMZ + LAMP :**

1. **Ajout de l’interaface**

Si on souhaite ajouter une page web accessible depuis le WAN, il faut configurer la partie DMZ du pfsense.

Dans la zone interface du pfsense sélectionner la carte réseau restante et mettez y une IP

![image.png](image.png)

![image.png](image%201.png)

1. Ajout de la redirection

Dans la zone firewall, nous allons ajouter l’accès du WAN vers le serveur

![image.png](image%202.png)

![image.png](image%203.png)

TCP → est un protocole de communication entre des machines

![image.png](image%204.png)

 comme nous allons vers un serveur web nous indiquons http ainsi que l’adresse du serveur

![image.png](image%205.png)

1. **Mise en place des règles**

maintenant que la redirection est faite, il faut vérifier les règles mise en place

Normalement, une règle à du être créer, a l’image de ce qui viens d’être créer dans la partie précédente qui autorise l’accès a la machine

![image.png](image%206.png)

Ensuite, il est possible de mettre d’autre règles pour les bonnes pratique

![image.png](image%207.png)

Dans un premier temps, on refuse l’accès de la DMZ au LAN car inutile dans notre cas

Ensuite, j’autorise la connexion entre la DMZ et le serveur web

Pour finir, j’autorise toute les directions pour la DMZ pour ne pas avoir de blocage 

1. **TEST**

Désormais, il devrait être possible d’accéder a la page web a partir de l’IP du pfsense coté WAN

![image.png](image%208.png)

Le contenu du serveur web est libre pour l’exemple je n’ai mis en place qu’apache 

# C) Mise en place d’une machine avec bureau distant :

1. **Ajout de la redirection**

Comme pour la partie précédente, on va configurer l’accès

![image.png](image%202.png)

![image.png](image%209.png)

La différence ce trouve dans la destination puisqu’il s’agit de la connexion a un bureau distant , donc il faut choisir un port spécifique

![image.png](image%2010.png)

Ensuite il faut indiquer que la destination est de type MS RDP qui signifie “Remote Desktop Protocole

![image.png](image%2011.png)

1. **vérification des règles**

Les règles précédente permettent permette déjà la communication vers la DMZ il faut juste vérifier la présence de la règles pour l’accès a la machine

![image.png](image%2012.png)

1. **TEST**

Comme bureau a distant j’ai fait le choix d’un Windows serveur mais n’importe quel machine aurait fonctionner 

![image.png](image%2013.png)

Puis sur une machine sur le WAN on va tenter une connexion

![image.png](image%2014.png)

On remarque que la connexion passe en indiquant le numéro du port

![image.png](image%2015.png)