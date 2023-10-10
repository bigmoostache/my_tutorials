# Tutoriel : Démarrer une instance sur un volume attaché avec OVHcloud

## Préambule
Les serveurs cloud d'OVH sont initialement fournis avec un disque d'origine basé sur une image système (par exemple, Debian 8, Windows 10). Toutefois, vous avez également la possibilité d'utiliser des volumes additionnels. Ce sont des disques persistants qui vous permettent de stocker des données de manière durable.

De plus, vous pouvez déployer un système d'exploitation sur un volume et démarrer votre serveur à partir de ce volume, remplaçant ainsi le disque d'origine.

## Prérequis
- Avoir chargé les variables d'environnement OpenStack.
- Posséder un volume contenant un système d'exploitation.

## Étapes :

### 1. Configuration du Volume
#### Marquer le volume comme prioritaire dans l'ordre de boot
Ajoutez une metadata au volume pour que le serveur cloud priorise ce disque lors du démarrage :
```bash
cinder metadata 897ec71d-bae2-4394-b8c1-4d8fd373a725 set boot_from=True
```

### 2. Attacher le volume
Après avoir paramétré la metadata `boot_from` à `True`, attachez le volume à l'instance :
```bash
nova volume-attach myinstance01 897ec71d-bae2-4394-b8c1-4d8fd373a72
```

### 3. Redémarrage de l'instance
Afin que l'instance démarre sur le volume additionnel, vous devez redémarrer l'instance. Vous pouvez le faire soit avec `nova stop` suivi de `nova start`, soit en forçant le redémarrage :
```bash
nova reboot --hard myinstance01
```
Notez qu'un redémarrage "soft" ne suffira pas.

Pour vérifier que tout s'est bien passé, regardez les points de montage avec la commande :
```bash
lsblk
```
Le point de montage `/` devrait être monté depuis `/dev/vdb1`.



From the output of `lsblk`, it looks like you have your main 400GB disk (`sda`) which is partitioned and already mounted (with `/` and `/boot/efi`), and the newly attached 1.7TB disk (`sdb`) which is not yet mounted.

To prepare and use the 1.7TB volume (`sdb`), you can follow these steps:

1. **Format the Attached Volume**:

   First, make sure that the disk is indeed the correct one and doesn't contain any data you need. Once you're sure, you can format it. For this example, I'll use the `ext4` file system.

   ```bash
   sudo mkfs.ext4 /dev/sdb
   ```

2. **Mount the Volume**:

   Create a mount point and then mount the volume:

   ```bash
   sudo mkdir /mnt/data
   sudo mount /dev/sdb /mnt/data
   ```

   To ensure the volume automatically mounts after a reboot, add it to `/etc/fstab`. Before you do this, it's a good practice to first get the UUID of the disk and use that in `/etc/fstab` instead of the `/dev/sdb` path:

   ```bash
   UUID=$(sudo blkid -s UUID -o value /dev/sdb)
   echo "UUID=$UUID /mnt/data ext4 defaults 0 0" | sudo tee -a /etc/fstab
   ```

3. **Verify Mount**:

   Make sure the volume is correctly mounted:

   ```bash
   df -h | grep /mnt/data
   ```

4. **Integrate with Kubernetes**:

   From here, you can follow the previous instructions on setting up a Persistent Volume in Kubernetes to utilize this mounted storage.

A reminder: The local volume approach has its caveats. The storage will be tied to that specific node. If you're running a stateful application and for some reason that node goes down, you might have data accessibility issues. If you need more resilient and distributed storage solutions, you might want to look into distributed storage options or the storage solutions offered by your cloud provider (if applicable).

