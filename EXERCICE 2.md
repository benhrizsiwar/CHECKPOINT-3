# Partie 1 : Gestion des utilisateurs
**Q.2.1.1**
sudo adduser siwar

**Q.2.1.2** 
-mot de passe de 12 caractères
sudo usermod -aG sudo siwar —-> utiliser des privilèges administratifs
# Partie 2 : Configuration de SSH

**Q.2.2.1**
sudo nano /etc/ssh/sshd_config
----> PermitRootLogin no

sudo systemctl restart sshd

**Q.2.2.2**
sudo nano /etc/ssh/sshd_config
---> AllowUsers siwar

**Q.2.2.3**
ssh-keygen —-> créer la clé

cat ~/.ssh/id_rsa.pub —> copie la clé publique 

ssh siwar@192.168.1.131 —--> accés au serveur

mkdir -p ~/.ssh 

chmod 700 ~/.ssh

nano ~/.ssh/authorized_keys —-> coller la clé publique

# Partie 3 : Analyse du stockage

**Q.2.3.1**
df -Th

Filesystem     Type     Size  Used Avail Use% Mounted on

/dev/sda1      ext4     30G   20G   10G  70% /

tmpfs          tmpfs    2G     0    2G   0%  /dev/shm

/dev/sdb1      xfs      50G  10G   40G  20% /data

**Q.2.3.2**
✔️ext4 
✔️tmpfs 
✔️xfs 

**Q.2.3.3**
✔️pour /dev/sdb

cat /proc/mdstat ----> identifier le disque défectueux

mdadm --detail /dev/md0

mdadm --remove /dev/md0 /dev/sdb ---->Retirer le disque défaillant sdb

mdadm --add /dev/md0 /dev/sdc ---> Ajouter le nouveau

cat /proc/mdstat ---->Vérifier la reconstruction du RAID 

**Q.2.3.4**
fdisk /dev/sdc ---> n --->p ---->Taille : +2G ---->t → Type 8e (LVM)----> w 

pvcreate /dev/sdc1

vgcreate vg_bareos /dev/sdc1

vgextend vg_bareos /dev/sdc1

lvcreate -L 2G -n lv_backup vg_bareos

mkfs.ext4 /dev/vg_bareos/lv_backup

mkdir -p /var/lib/bareos/storage

`/dev/vg_bareos/lv_backup  /var/lib/bareos/storage  ext4  defaults  0  2`

mount -a

**Q.2.3.5**
vgdisplay vg_bareos | grep "Free  PE" | awk '{print $5*4 " MB"}'

# Partie 4 : Sauvegardes

**Q.2.4.1**

✔️Bareos Director : gère la planification et l'exécution des tâches de sauvegarde.

✔️Bareos File Daemon : installé sur la machine cliente, il envoie les fichiers au Director.

✔️Bareos Storage Daemon gère le stockage des fichiers sauvegardés.

# Partie 5 : Filtrage et analyse réseau
**Q.2.5.1**
sudo iptables -L -v -n

**Q.2.5.2**

# Partie 6 : Analyse de logs

**Q.2.6.1**
sudo grep "Failed password" /var/log/auth.log | tail -n 10









