###############procedure dextension du /sanhome2/ avec la totalité du volum restant dans la baie de stockage


## dans un prmier temps j'ai creer un volum a partir de storage management client portail:
# le volume cree à le num device ID : 6000d31003953e000000000000000011

## ensuite j'ai redemaré le serveur 
## j'ai identiifer le numéro de device "device ID" dans /dev/mapper/
ls /dev/mapper/*6000d31003953e000000000000000011


##Creer physical volume par pvcreate
pvcreate /dev/mapper/36000d31003953e000000000000000011


##to add physical volumes to a volume group
vgextend sanvg2 /dev/mapper/36000d31003953e000000000000000011

##display attributes of volume groups
vgdisplay sanvg2



cd /dev/
ls
ls *san*
cd sanvg2/
ls
ll
pwd

#extend the size of a logical volume
lvextend -l +100%FREE /dev/sanvg2/sanvol2
df -kh
cd /


##display attributes of a logical volume
lvdisplay /dev/sanvg2/sanvol2
df -kh


#
resize2fs /dev/mapper/sanvg2-sanvol2

# I got this error
resize2fs 1.42.9 (4-Feb-2014)
resize2fs: La nouvelle taille est trop grande pour être exprimée sur 32 bits
### I googled the error and I find thta I need to use a recent version of e2fsprogs, so I followed the procedure below:

resize2fs -h
resize2fs 1.42.9 (4-Feb-2014)
## I need to update the soft to an 1.44 version

git clone -b v1.44.2 https://git.kernel.org/pub/scm/fs/ext2/e2fsprogs.git
cd e2fsprogs/
ls
./configure
make
ls
./resize/resize2fs -h

resize2fs 1.44.2 (14-May-2018)



## unmount the file system first
sudo umount /dev/mapper/sanvg2-sanvol2

### recherchez des erreurs dans le système de fichiers
e2fsck/e2fsck -f /dev/mapper/sanvg2-sanvol2

##activer la prise en charge 64 bits dans le systeme de fichier
resize/resize2fs -b /dev/mapper/sanvg2-sanvol2


#redimensionner le système de fichiers
resize/resize2fs -p /dev/mapper/sanvg2-sanvol2

#Vérifiez à nouveau le système de fichiers
e2fsck/e2fsck -f /dev/mapper/sanvg2-sanvol2


#remontez l'appareil:
mount /dev/mapper/sanvg2-sanvol2
df -kh
ls /sanhome2/
