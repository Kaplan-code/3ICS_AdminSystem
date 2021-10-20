# tp-5-Kaplan Kubilay

## TP 5 - Systèmes de fichiers, partitions et disques

## Exercice 1. Disques et partitions
1. Dans l’interface de configuration de votre VM, créez un second disque dur, de 5 Go dynamiquement
alloués ; puis démarrez la VM

2. Vérifiez que ce nouveau disque dur est bien détecté par le système
```
lsblk -l | grep disk
```
3. Partitionnez ce disque en utilisant fdisk : créez une première partition de 2 Go de type Linux (n°83),
et une seconde partition de 3 Go en NTFS (n°7)
```
kubilay@serveur:~$ sudo fdisk /dev/sdb

Command (m for help): p
Disk /dev/sdb: 5 GiB, 5368709120 bytes, 10485760 sectors
Disk model: Virtual disk
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x927e5b7a

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-10485759, default 10485759): +2G

Created a new partition 1 of type 'Linux' and of size 2 GiB.

Command (m for help): t
Selected partition 1
Hex code (type L to list all codes): 83
Changed type of partition 'Linux' to 'Linux'.
Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2): 2
First sector (4196352-10485759, default 4196352):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-10485759, default 10485759):

Created a new partition 2 of type 'Linux' and of size 3 GiB.

Command (m for help): t
Partition number (1,2, default 2): 2
Hex code (type L to list all codes): 7

Changed type of partition 'Linux' to 'HPFS/NTFS/exFAT'.
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
4. A ce stade, les partitions ont été créées, mais elles n’ont pas été formatées avec leur système de fichiers.
A l’aide de la commande mkfs, formatez vos deux partitions ( pensez à consulter le manuel !)
```
sudo mkfs.ext4 /dev/sdb1
sudo mkfs.ntfs /dev/sdb2
```
5. Pourquoi la commande df -T, qui affiche le type de système de fichier des partitions, ne fonctionne-telle
pas sur notre disque ?
```
Cette commande ne fonctionne pas sur notre disque car les partitions ne sont pas encore montées
```
6. Faites en sorte que les deux partitions créées soient montées automatiquement au démarrage de la
machine, respectivement dans les points de montage /data et /win (vous pourrez vous passer des
UUID en raison de l’impossibilité d’effectuer des copier-coller)
```
sudo mkdir /data
sudo mkdir /win

sudo nano /etc/fstab => On ajoute :
UUID=5c8f388e-08b5-4dd8-9d7c-13dff3ae0dad  /win  ext4  defaults  0  0
UUID=64BD7A873D1E5E23  /data  ntfs  defaults  0  0
```
7. Utilisez la commande mount puis redémarrez votre VM pour valider la configuration
```
mount -a
```
## Exercice 2. Partitionnement LVM
1. On va réutiliser le disque de 5 Gio de l’exercice précédent. Commencez par démonter les systèmes de
fichiers montés dans /data et /win s’ils sont encore montés, et supprimez les lignes correspondantes
du fichier /etc/fstab
```
sudo umount /data
sudo umount /win
nano /etc/fstab
```
2. Supprimez les deux partitions du disque, et créez une patition unique de type LVM
 La création d’une partition LVM n’est pas indispensable, mais vivement recommandée quand
on utilise LVM sur un disque entier. En effet, elle permet d’indiquer à d’autres OS ou logiciels de
gestion de disques (qui ne reconnaissent pas forcément le format LVM) qu’il y a des données sur
ce disque.
 Attention à ne pas supprimer la partition système !
```
sudo fdisk /dev/sdb

Donne :

kubilay@serveur:~$ sudo fdisk /dev/sdb

Command (m for help): d
Partition number (1,2, default 2): 1

Partition 1 has been deleted.

Command (m for help): d
Selected partition 2
Partition 2 has been deleted.
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
3. A l’aide de la commande pvcreate, créez un volume physique LVM. Validez qu’il est bien créé, en
utilisant la commande pvdisplay.
 Toutes les commandes concernant les volumes physiques commencent par pv. Celles concernant
les groupes de volumes commencent par vg, et celles concernant les volumes logiques par lv.
```
sudo pvcreate --force /dev/sdb
sudo pvdisplay
```
4. A l’aide de la commande vgcreate, créez un groupe de volumes, qui pour l’instant ne contiendra que
le volume physique créé à l’étape précédente. Vérifiez à l’aide de la commande vgdisplay.
 Par convention, on nomme généralement les groupes de volumes vgxx (où xx représente l’indice
du groupe de volume, en commençant par 00, puis 01...)
```
sudo vgcreate vg00 /dev/sdb
sudo vgdisplay
```
5. Créez un volume logique appelé lvData occupant l’intégralité de l’espace disque disponible.
 On peut renseigner la taille d’un volume logique soit de manière absolue avec l’option -L (par
exemple -L 10G pour créer un volume de 10 Gio), soit de manière relative avec l’option -l : -l
60%VG pour utiliser 60% de l’espace total du groupe de volumes, ou encore -l 100%FREE pour
utiliser la totalité de l’espace libre.
```
sudo lvcreate -n lvData -l 100%FREE vg00 -y
sudo lvdisplay
```
6. Dans ce volume logique, créez une partition que vous formaterez en ext4, puis procédez comme dans
l’exercice 1 pour qu’elle soit montée automatiquement, au démarrage de la machine, dans /data.
 A ce stade, l’utilité de LVM peut paraître limitée. Il trouve tout son intérêt quand on veut par
exemple agrandir une partition à l’aide d’un nouveau disque.
```
On ajoute une partition dans lvData :
fdisk /dev/mapper/vg00-lvData

sudo mkfs.ext4 /dev/mapper/vg00-lvData

sudo nano /etc/fstab
On ajoute :
/dev/mapper/vg00-lvData   /data  ext4  defaults  0  0

sudo mount -a
sudo reboot
```
7. Eteignez la VM pour ajouter un second disque (peu importe la taille pour cet exercice). Redémarrez
la VM, vérifiez que le disque est bien présent. Puis, répétez les questions 2 et 3 sur ce nouveau disque.
```
lsblk -l | grep disk 
Donne :
sda                     8:0    0   20G  0 disk
sdb                     8:16   0    5G  0 disk
sdc                     8:32   0    5G  0 disk
Donc disque bien cree.

sudo pvcreate --force /dev/sdc

Vérification : 
sudo pvdisplay

   "/dev/sdc" is a new physical volume of "5.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdc
  VG Name
  PV Size               5.00 GiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               VN4AvW-4d37-1fBC-z3fW-jUvP-Ox2j-aedUqg
```
8. Utilisez la commande vgextend <nom_vg> <nom_pv> pour ajouter le nouveau disque au groupe de
volumes
```
sudo vgextend vg00 /dev/sdc
```
9. Utilisez la commande lvresize (ou lvextend) pour agrandir le volume logique. Enfin, il ne faut pas
oublier de redimensionner le système de fichiers à l’aide de la commande resize2fs.
 Il est possible d’aller beaucoup plus loin avec LVM, par exemple en créant des volumes par
bandes (l’équivalent du RAID 0) ou du mirroring (RAID 1). Le but de cet exercice n’était que de
présenter les fonctionnalités de base.
```
sudo lvextend /dev/mapper/vg00-lvData /dev/sdc
Donne :
kubilay@serveur:~$ sudo lvextend /dev/mapper/vg00-lvData /dev/sdc
  Size of logical volume vg00/lvData changed from <5.00 GiB (1279 extents) to 9.99 GiB (2558 extents).
  Logical volume vg00/lvData successfully resized.

On resize :

kubilay@serveur:~$ sudo resize2fs /dev/mapper/vg00-lvData
resize2fs 1.45.5 (07-Jan-2020)
Filesystem at /dev/mapper/vg00-lvData is mounted on /data; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 2
The filesystem on /dev/mapper/vg00-lvData is now 2619392 (4k) blocks long.
```