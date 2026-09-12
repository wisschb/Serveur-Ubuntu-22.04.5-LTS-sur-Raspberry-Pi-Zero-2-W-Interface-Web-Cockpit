Projet : Serveur Ubuntu 22.04.5 LTS sur Raspberry Pi Zero 2 W + Interface Web Cockpit

Matériel utilisé :
- Raspberry Pi Zero 2 W
- Carte microSD (16 Go recommandé)
- Alimentation
- Connexion Wi-Fi ou RJ45
- Un PC pour préparer la carte

(1) 🟩 Installer Ubuntu Server 22.04.5 LTS
- Télécharger Raspberry Pi Imager
- Télécharger et installer : https://www.raspberrypi.com/software/
- Flasher la carte SD
- Ouvrir Raspberry Pi Imager
- Cliquer sur Choisir l’appareil
→ Sélectionner Raspberry Pi Zero 2 W
- Cliquer sur Choisir le système d’exploitation
→ Ubuntu
→ Ubuntu Server 22.04.5 LTS (64/32-bit) -- (version 32bits conseillé pour le pi 0 2W
- Cliquer sur Choisir le stockage
→ Sélectionner la carte microSD


⚙️ Paramètres avancés (IMPORTANT)
Cliquer sur la roue dentée ⚙️ :
- Définir un nom d’hôte (ex : ubuntu-server)
- Activer SSH
- Définir un utilisateur et mot de passe
- Configurer le Wi-Fi
- Choisir le pays
- Cliquer sur Enregistrer puis Écrire
- Attendre la fin de l’écriture


(2) 🟩 Premier démarrage
Insérer la carte SD dans le Raspberry Pi.
Brancher l’alimentation.
Après quelques secondes, l’écran affiche :
Ubuntu 22.04.5 LTS ubuntu-server tty1
ubuntu-server login:
Cela signifie que le système est installé correctement


(3) 🟩 Connexion au système
Entrer :
login: ton_utilisateur
password: ********
⚠️ Le mot de passe ne s’affiche pas (normal).

Après connexion :
ton_utilisateur@ubuntu-server:~$
Tu es maintenant sur Ubuntu Server 22.04.5 LTS !


(4️) 🟩 Mettre à jour le système (OBLIGATOIRE)

Mettre à jour la liste des paquets :
- sudo apt update (Cette commande synchronise le Raspberry Pi avec les serveurs Ubuntu).
Ensuite installer les mises à jour :
- sudo apt upgrade -y
Puis nettoyer :
- sudo apt autoremove -y
Ton serveur est maintenant à jour ✅


(5️) 🟩 Installer Cockpit (Interface Web)
Cockpit permet d’administrer le serveur via navigateur.
Installer Cockpit :
- sudo apt install cockpit -y
Vérifier que le service fonctionne :
- sudo systemctl status cockpit
Si nécessaire :
- sudo systemctl enable cockpit
- sudo systemctl start cockpit


(6) 🟩 Accéder à l’interface Web
Depuis un navigateur :

- https://IP_DU_RASPBERRY:9090
Exemple :
- https://192.168.1.25:9090

Un message de sécurité apparaît (certificat auto-signé).
Cliquer sur Avancé → Continuer.

Nouveaux

Se connecter avec :

Nom d’utilisateur : xxxxx
Mot de passe : xxxxx

(7) 🟩 Installer un serveur de fichiers Samba

Samba permet de créer un partage de fichiers accessible depuis les ordinateurs du réseau local. Dans ce projet, le partage est stocké sur la carte microSD du Raspberry Pi.

- Installation de Samba
Mettre à jour les paquets puis installer Samba :
sudo apt update
sudo apt install samba samba-common-bin smbclient -y

- Création du dossier partagé
Créer le dossier qui contiendra les fichiers :
sudo mkdir -p /srv/samba/partage

- Création de l'utilisateur
Créer un utilisateur dédié au partage :
sudo adduser utilisateur1

- Donner les droits sur le dossier :
sudo chown -R utilisateur1:utilisateur1 /srv/samba/partage
sudo chmod -R 770 /srv/samba/partage

- Ajouter l'utilisateur à Samba :
sudo smbpasswd -a utilisateur1
sudo smbpasswd -e utilisateur1

- Configuration du partage
Sauvegarder la configuration originale :
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak

- Modifier la configuration :
sudo nano /etc/samba/smb.conf

- Ajouter à la fin du fichier :
[Partage]
   path = /srv/samba/partage
   browseable = yes
   read only = no
   writable = yes
   valid users = utilisateur1
   force user = utilisateur1
   create mask = 0660
   directory mask = 0770

- Vérifier la configuration :
sudo testparm

- Redémarrer Samba et l'activer au démarrage :
sudo systemctl restart smbd
sudo systemctl enable smbd

- Vérifier que le service fonctionne :
sudo systemctl status smbd

- Test du partage
Tester les partages Samba depuis le Raspberry Pi :
smbclient -L localhost -U utilisateur1

- Le partage "Partage" doit apparaître.

- Pour connaître l'adresse IP du Raspberry Pi :
hostname -I

- Depuis un ordinateur Windows connecté au même réseau, ouvrir l'explorateur de fichiers et saisir :

\\IP_DU_RASPBERRY\Partage
Exemple : \\192.168.1.25\Partage
L'utilisateur "utilisateur1" et son mot de passe Samba sont ensuite utilisés pour accéder aux fichiers.

- Résultat
Le Raspberry Pi fonctionne maintenant comme un petit serveur de fichiers sur le réseau local. Les fichiers peuvent être créés, modifiés et consultés depuis un ordinateur Windows grâce au protocole SMB/Samba.

