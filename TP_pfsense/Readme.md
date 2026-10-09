# TP - pfSense - Becquet Arthur

# **Contexte :**

Dans ce TP, nous allons mettre en place un pfSense avec une interface WAN, un LAN pour l'administration et une DMZ sur laquelle on ajoutera un serveur Apache ainsi qu'une machine avec un accès bureau à distance.

![Schéma du TP](images/Capture_dcran_2026-10-07_155359.png)

# A) Installation du pfSense

## 1. Les prérequis

Dans un premier temps, nous allons télécharger l'ISO pour une machine pfSense et créer une VM avec.

![ISO pfSense](images/Capture_dcran_2026-10-07_155921.png)

Attention, il ne faut pas oublier d'ajouter les trois cartes réseau sur la VM (une pour le WAN, une pour le LAN et une pour la DMZ).

![Les trois cartes réseau de la VM](images/Capture_dcran_2026-10-07_160152.png)

## 2. L'installation

La partie la plus importante de cette procédure est l'affectation des cartes réseau.

- Pour le WAN, rien de spécial n'est à faire, seul le choix entre IP DHCP et statique est à faire.

  ![Affectation de l'interface WAN](images/Capture_dcran_2026-10-07_161028.png)

- En revanche, pour le LAN, il faut différencier l'adresse IP de celle du WAN, sinon le DHCP sera diffusé sur votre réseau et causera des conflits d'adresses.

  ![Affectation de l'interface LAN](images/Capture_dcran_2026-10-07_161534.png)

  ![Configuration IP du LAN](images/Capture_dcran_2026-10-07_161613.png)

- En revanche, la DMZ n'a pas besoin d'être configurée pour le moment.

## TEST du pfSense

Sur une machine connectée à la carte réseau LAN et sur le même réseau que la zone LAN du pfSense, nous allons nous y connecter depuis un navigateur (les identifiants de base sont `admin` / `pfsense`).

![Page de connexion pfSense](images/Capture_dcran_2026-10-07_162258.png)

# B) Mise en place de la DMZ + LAMP

## 1. Ajout de l'interface

Si on souhaite ajouter une page web accessible depuis le WAN, il faut configurer la partie DMZ du pfSense.

Dans la zone Interfaces du pfSense, sélectionner la carte réseau restante et y mettre une IP.

![Adresse IP de la DMZ](images/image_1.png)

## 2. Ajout de la redirection

Dans la zone Firewall, nous allons ajouter l'accès du WAN vers le serveur (Firewall / NAT / Port Forward / Edit).

![Firewall / NAT / Port Forward / Edit](images/image_2.png)

Le protocole choisi est TCP : c'est un protocole de communication entre des machines.

![Interface WAN, IPv4, protocole TCP](images/image_3.png)

Comme nous allons vers un serveur web, nous indiquons HTTP ainsi que l'adresse du serveur.

![Port de destination HTTP et IP de redirection](images/image_4.png)

![Port cible de redirection HTTP](images/image_5.png)

## 3. Mise en place des règles

Maintenant que la redirection est faite, il faut vérifier les règles mises en place.

Normalement, une règle a dû être créée automatiquement, à l'image de ce qui vient d'être configuré dans la partie précédente, et autorise l'accès à la machine.

![Règle NAT vers le serveur web](images/image_6.png)

Ensuite, il est possible de mettre d'autres règles pour les bonnes pratiques :

![Règles de la DMZ](images/image_7.png)

- Dans un premier temps, on refuse l'accès de la DMZ au LAN car inutile dans notre cas.
- Ensuite, j'autorise la connexion entre la DMZ et le serveur web.
- Pour finir, j'autorise toutes les directions pour la DMZ pour ne pas avoir de blocage.

## 4. TEST

Désormais, il devrait être possible d'accéder à la page web à partir de l'IP du pfSense côté WAN.

![Page par défaut d'Apache2](images/image_8.png)

Le contenu du serveur web est libre ; pour l'exemple, je n'ai mis en place qu'Apache.

# C) Mise en place d'une machine avec bureau distant

## 1. Ajout de la redirection

Comme pour la partie précédente, on va configurer l'accès.

![Firewall / NAT / Port Forward / Edit](images/image_2.png)

![Interface WAN, IPv4, protocole TCP](images/image_9.png)

La différence se trouve dans la destination : puisqu'il s'agit d'une connexion à un bureau à distance, il faut choisir un port spécifique (ici 50000) et rediriger vers l'IP de la machine.

![Plage de ports de destination 50000 et IP de redirection](images/image_10.png)

Ensuite, il faut indiquer que le port cible de redirection est de type MS RDP, qui signifie « Remote Desktop Protocol ».

![Port cible MS RDP](images/image_11.png)

## 2. Vérification des règles

Les règles précédentes permettent déjà la communication vers la DMZ ; il faut juste vérifier la présence de la règle pour l'accès à la machine.

![Règle NAT vers le RDP (port 3389)](images/image_12.png)

## 3. TEST

Comme bureau à distance, j'ai fait le choix d'un Windows Server, mais n'importe quelle machine aurait fonctionné.

![Gestionnaire de serveur Windows Server](images/image_13.png)

Puis, sur une machine du WAN, on va tenter une connexion en indiquant l'IP du pfSense côté WAN suivie du port de redirection.

![Connexion Bureau à distance](images/image_14.png)

On remarque que la connexion passe en indiquant le numéro du port.
