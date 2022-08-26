# LANCER UN CLUSTER DE CALCULS SUR LE CLOUD OSCAR (OpenStack Clermont Auvergne pour la Recherche)  
Le but est de faire une utilisation éphémère pour partager les ressources ET POUR LA SECURITE !! (cyber attaq)

Un fichier yml de configuration (pour l'orchestrateur, ce fichier yml indique à Ansible la structure du mini cluster à déployer avec noeuds maitre, noeuds fils, etc)
L'orchestrateur est un logiciel, ici Ansible.

Se connecter au cloud Oscar (compte openstack)
Aller dans l'onglet orchestration
aller dans Stack (pile) 
+ Lancer la pile 
choisir un fichier yml

Fenêtre "Launch Stack": des info sont demandé comme nom du stack, notre passwd dueuser pour lancer des VM et autre en notre nom


Le cluster aura un noeud maitre (master slurm) et des noeuds fils (deamon slurm)
Montage NFS pour que le home soit accessible depuis les noeuds fils.
On choisi les nb de cpu et ram pour le noeud maître et les noeuds fils
Disque SSD est bcp plus performant pour l'enregistrement des données que les disque HD (pour Triannot il faut des disques SSD)
Choix flavor: mieux vaur choisir plein de noeuds avec peu de ressources (default) que bcp de ressources d'un coup

On crée un réseau/cluser virtuel, et ensuite des VM sont montées dessus
Le noeud master a deux IP: une IP virtuelle pour le réseau virtuel, et une IP publique réelle pour pouvoir y accéder et se connecter en ssh dessus
Une fois connecté sur le noeud maître en ssh, on peut rebondir sur les noeuds fils.

Image: centos7 --> validée pour la dév concernant Triannot

choix de 10Gb donc pas de latence


key name (pour pouvoir se connecter en ssh sur les noeuds)

ET il est possible de renseigner jusqu'à 5  autres users --> reseigner nom user et public rsa_ssh

On peut faire un snapshot, créer une image d'une VM pour crasher une VM qui tourne depuis longtemps et la reancer avec une nouvelle IP (pour la sécurité).
!!! ---> Autre solution, mettre la VM en attente et la reprendre plus tard avec Shelve instance au lieu de Create snapshot

# DIFFERENTS ONGLETS DE L'INTERFACE OSCAR
KEY PAIRS:: importer une clé publique pour pouvoir se connecter à la VM créée
Volumes: on n'a pas à gérer ça a priori 
Network: provider ou projets
	on ne peut pas supprimer les providers

Pour launcher une instance, il est nécessaire de créer un Network qui doit avoir un sub network
Il faut choisir un nom de réseau, et un nom de sous réseau, avec une série d'IPV4 de la forme 198.56.24.0/24 (n'importe quel chiffre mais format à respecter)
--> montage d'un réseau virtuel, le rooteur fait l'intermédiaire entre le réseau virtuel avec les IP virtuelles et le réseau NET eduroam par lequel notre PC portable
doit se connecter au cluster virtuel. Ce rooteur a 2 "pattes", une  dans chaque réseau, soit deux IP. Une pour le réseau virtuel que l'on créé pour lancer notre VM, 
et une pour échanger et pouvoir se connecter au réseau virtuel depuis mon PC portable.

Il faut ensuite créer un Router
Choisir l'instance
Selectionner le réseau provider, et choisir le provider-public-uca1, l'IP publique permettra de dialoguer avec l'exterieur et faire les update ubuntu ou centos par ex.
Pour l'instant le rooter est créé et a une IP publique, mais on ne l'a pas encore lié dans le network virtuel, aller dans ... et choisir l'instance
Ensuite dans Compute/Instances, dans le menu déroulant, associate IP flottante, en choisir une n'importe la quelle
Ensuite créer des security groupes pour la VM à instancier pour permettre la connexion ssh: add rule SSH

Demo sur une VM ubuntu
sudi -i pour être root
installation de pip3: sudo apt install pip3
installation de jupyter-lab notebook: pip3 install jupyter-lab
.local/bin/jupyter-lab --port=80 --ip=0.0.0

# POUR TRANSFERER LES FICHIER:
scp 
ou sudo apt install rclone et faire un rclone
ou git pour le code 

# CONFIGURATION:
On peut ajouter des commandes pour customiser: mount /dev
adduser --group

Il y a un /home/users/shared: rep partagé à tous les users
Et un home par user

Question: accès au NFS de HPC2 depuis les VM: via des scp ou via un montage ssh fs sur le rep que l'on veut 

Pour info les projets CEPH sont gérés sur Oscar aussi 

# Codes sauvegardes pour memoire (from HeleneRimbert)
## rclone mount

acces in read-only mode to my data on oscar:

```console
$ rclone config # setup URL and credentials
$ rclone listremotes
$ nohup rclone mount --dir-cache-time=2m \
  --fast-list \
  --s3-chunk-size=1G \
  --s3-upload-concurrency=8 \
  --read-only \
  remote: /path/to/data/s3/ 1> rclone-mount.log 2> rclone-mount.error &
```

## install miniconda3

```console
$ wget https://repo.anaconda.com/miniconda/Miniconda3-py39_4.12.0-Linux-x86_64.sh
$ bash Miniconda3-py39_4.12.0-Linux-x86_64.sh
$ conda -V
$ conda config --add channels bioconda
$ conda config --add channels conda-forge
$ conda env create --file myenv.yml
```

## check SLURM controler

Sometimes, the SLURM controler is hanging. Restart is needed

```console
# service slurmctld status # check if SLURM controller is dead
# service slurmctld restart # restart the controller
# service slurmctld status # check the restart is OK
# sinfo # check if SLURM detects the nodes
```
