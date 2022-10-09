BELHADJ MANSOUR Haythem
3ICS - CPE Lyon

# Exercice 1

1 - Sur vSphère, aller dans les paramètres de notre VM, puis cliquer sur "Ajouter un périphérique", puis sélectionner "Disque Dur". Après l'ajout, alloué 5 Go au nouveau disque dur et sélectionner un provisionnement dynamique. On enregistre les modifications et on peut lancer la VM.

2 - Avec la commande ``lsblk``, on peut remarquer la présence du nouveau disque de 5 Go.

3 - Pour partitionner le disque, on va utiliser la commande ``sudo fdisk /dev/sdb`` (sdb étant notre nouveau disque dur).
Pour créer notre première partition, après avoir taper la commande précédente, on va taper **n** pour créer une nouvelle partition. Le type de partition choisi est le **primary**, on tape donc **p**. On sélectionne le numéro de la partition. On tape 1 ou 2 selon le numéro de la partition que l'on veut ajouté (**1** pour la première de 2 Go, et 2 pour celle de 3 Go). Pour le secteur, on peut taper sur **Entrée**, ça lui attriburera automatiquement. Puis on écris la taille de la partition, soit 2 Go, de cette manière : **+2G**. Pour la deuxième, on peut **Entrée** directement car il reste 3 Go.
Les partitions crées sont de type *Linux* (83). Pour modifier le type de la seconde partition, on va taper **t** pour la commande, choisir la partition voulu (dans notre cas : **2**), puis on écrite le numéro du type. Dans notre cas, c'est **7** pour *NTFS*. Pour sauvegarder les modifications, on entre **w**.

On peut vérifier le changement en tapant **p**.

4 - **mkfs** (Makes File Systems) est la commande à utiliser en ligne de commandes depuis un terminal pour créer et formater un système de fichiers.
Ainsi, on utilise le paramètre **-t** pour spécifier le type de système de fichiers suivi du type de système de fichier puis on précise la partition à formater.
Ex. : sudo mkfs -t ext4 /dev/sdb1 et sudo mkfs -t ntfs /dev/sdb2
Ou : sudo mkfs.ext4 /dev/sdb1

5 - La commande ``df -T`` ne fonctionne pas car notre disque n'est pas encore monté.

6 - Pour faire en sorte que les 2 partitions soient montées automatiquement, on peut suivre le tutoriel à cette adresse : https://doc.ubuntu-fr.org/mount_fstab.
Ex.: /data was

7 - En utilisant la commande ``mount -a``, cette commande exécute le fstab comme si votre machine venait de démarrer. On peut vérifier que leur montage a été effectué en faisant la commande ``df -T``. On remarque bien la présence des deux nouvelles partitions.

8 - Pas possible.

9 - Pas possible.

# Exercice 2 - Partitionnement LVM

1 - Pour démonter un système de fichiers monté, on utilise la commande **sudo umount nom_systeme**. Dans notre cas, on démonte les systèmes de fichiers /data et /win. Puis on supprime les lignes correspondantes du fichier /etc/fstab pour éviter que les systèmes de fichiers soient automatiquement remontés.

2 - Pour supprimer une partition, il faut utiliser la commande **sudo fdisk chemin_partition**. Dans notre cas, on tape la commande **sudo fdisk /dev/sdb**. Puis on tape **d** pour **delete** la partition que l'on veut supprimer. On fait ça pour les 2 partitions créées précédemment.
On crée ensuite une partition qu'on passe ensuite sous lvm (voir au dessus).

3 - Ensuite, on va formater le disque physique et de l’intégrer au système de gestion LVM avec la commande **pvcreate /dev/sdb1**. On remarque sa présence à l'aide de la commande **pvdisplay**.

4 - Après, pour créer notre groupe de volumes, on utilise la commande **vgcreate vg00 /dev/sdb1** (vg00 est le nom du groupe, il est nécessaire). On remarque sa présence à l'aide de la commande **vgdisplay**.

5 - Par conséquent, on crée un volume logique qu'on appelle lvData avec la commande **lvcreate -n lvData -l 100%FREE vg00** (-n pour lui donner le nom et -l 100%FREE pour qu'il occupe tout l'espace disque disponible).

6 - On crée maintenant une partition dans le volume qu'on vient de créer et on le formate en ext4 (même méthode que dans l'exercice 1). On rend le montage automatique (voir méthode dans l'exercice 1).

7 - Voir première question pour savoir comment ajouter une nouveau disque. On remarque bien la présence du nouveau disque avec la commande **lsblk**. On crée le volume physique et on vérifie qu'il a été créé.

8 - Pour ajouter le nouveau disque au groupe de volume, on utilise la commande **vgextend vg00 /dev/sdc1**. Avec la commande **vgdisplay** on peut remarquer la présence des 2 disques.

9 - Enfin, on peut utiliser **lvresize -L 2G /dev/vg00/lvData** pour étendre le volume logique. Puis la commande **resize2fs /dev/vg00/lvData** pour redimensionner le système de fichiers.
