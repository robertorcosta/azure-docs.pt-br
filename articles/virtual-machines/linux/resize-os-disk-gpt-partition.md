---
title: Redimensionar um disco do sistema operacional com partição GPT | Microsoft Docs
description: Este artigo fornece instruções sobre o redimensionamento de um disco do sistema operacional com partição GPT.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 30a960c3ed76788158b15022947fec49a95ae299
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375203"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Redimensionar um disco do sistema operacional com partição GPT

> [!NOTE]
> Esse cenário se aplica somente a discos do sistema operacional com uma partição GPT (tabela de partição GUID).

Este artigo descreve como aumentar o tamanho de um disco do sistema operacional com GPT no Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Identificar se o disco do sistema operacional tem uma partição MBR ou GPT

Use o `parted` comando para identificar se a partição de disco foi criada com uma partição MBR (registro mestre de inicialização) ou uma partição GPT.

### <a name="mbr-partition"></a>Partição MBR

Na saída a seguir, a **tabela de partição** mostra um valor de **msdos**. Esse valor identifica uma partição MBR.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>Partição GPT

Na saída a seguir, a **tabela de partição** mostra um valor de **gpt**. Esse valor identifica uma partição GPT.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Se sua VM (máquina virtual) tiver uma partição GPT no disco do sistema operacional, aumente o tamanho do disco do sistema operacional.

## <a name="increase-the-size-of-the-os-disk"></a>Aumentar o tamanho do disco do sistema operacional

As instruções a seguir se aplicam a distribuições endossadas pelo Linux.

> [!NOTE]
> Antes de prosseguir, faça uma cópia de backup de sua VM ou tire um instantâneo do disco do sistema operacional.

### <a name="ubuntu"></a>Ubuntu

Para aumentar o tamanho do disco do sistema operacional no Ubuntu 16.x e 18.x:

1. Pare a VM.
1. Aumente o tamanho do disco do sistema operacional no portal.
1. Reinicie a VM e faça logon na VM como um usuário **raiz**.
1. Verifique se o disco do sistema operacional agora exibe um tamanho maior do sistema de arquivos.

Conforme mostrado no exemplo a seguir, o disco do sistema operacional foi redimensionado no portal para 100 GB. O sistema de arquivos **/dev/sda1** montado em **/** mostra agora 97 GB.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

Para aumentar o tamanho do disco do sistema operacional no SUSE 12 SP4, SUSE SLES 12 para SAP, SUSE SLES 15 e SUSE SLES 15 para SAP:

1. Pare a VM.
1. Aumente o tamanho do disco do sistema operacional no portal.
1. Reinicie a VM.

Quando a VM reiniciar, execute as etapas a seguir:

1. Acesse sua VM como usuário **raiz** usando o comando a seguir:

   ```
   # sudo -i
   ```

1. Use o comando a seguir para instalar o pacote **growpart** , que será usado para redimensionar a partição:

   ```
   # zypper install growpart
   ```

1. Use o `lsblk` comando para localizar a partição montada na raiz do sistema de arquivos ("/"). Nesse caso, vemos que a partição 4 do dispositivo SDA está montada em/:

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Redimensione a partição necessária usando o `growpart` comando usando o número de partição encontrado na etapa anterior.

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Execute o `lsblk` comando novamente para verificar se a partição foi aumentada.

   A saída a seguir mostra que a partição **/dev/sda4** foi redimensionada para 46,5 GB:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Identifique o tipo de sistema de arquivos no disco do sistema operacional usando o `lsblk` comando com o `-f` sinalizador:

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. Com base no tipo de sistema de arquivos, use os comandos apropriados para redimensionar o sistema de arquivos.
   
   Para **xfs**, use o seguinte comando:
   
   ```
   #xfs_growfs /
   ```
   
   Saída de exemplo:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Para **ext4**, use o seguinte comando:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Para verificar o aumento de tamanho do sistema de arquivos de **df -Th**, use o comando a seguir:
   
   ```
   #df -Thl
   ```
   
   Saída de exemplo:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   No exemplo anterior, podemos ver que o tamanho do sistema de arquivos para o disco do sistema operacional aumentou.

### <a name="rhel"></a>RHEL

Para aumentar o tamanho do disco do sistema operacional no RHEL 7.x com LVM:

1. Pare a VM.
1. Aumente o tamanho do disco do sistema operacional no portal.
1. Inicie a VM.

Quando a VM reiniciar, execute as etapas a seguir:

1. Acesse sua VM como usuário **raiz** usando o comando a seguir:
 
   ```
   #sudo su
   ```

1. Instale o pacote **gptfdisk** necessário para aumentar o tamanho do disco do sistema operacional.

   ```
   #yum install gdisk -y
   ```

1. Para ver o maior setor disponível no disco, execute o seguinte comando:

   ```
   #sgdisk -e /dev/sda
   ```

1. Redimensione a partição sem excluí-la usando o comando a seguir. O comando **parted** tem uma opção chamada **resizepart** para redimensionar a partição sem excluí-la. O número 4 após **resizepart** indica o redimensionamento da quarta partição.

   ```
   #parted -s /dev/sda "resizepart 4 -1" quit
   ```
    
1. Execute o seguinte comando para verificar se a partição foi aumentada:

   ```
   #lsblk
   ```

   A saída a seguir mostra que a partição **/dev/sda4** foi redimensionada para 99 GB.

   ```
   [user@myvm ~]# lsblk
   NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   fd0                 2:0    1    4K  0 disk
   sda                 8:0    0  100G  0 disk
   ├─sda1              8:1    0  500M  0 part /boot/efi
   ├─sda2              8:2    0  500M  0 part /boot
   ├─sda3              8:3    0    2M  0 part
   └─sda4              8:4    0   99G  0 part
   ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
   ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
   ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
   ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
   ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
   └─rootvg-rootlv   253:5    0    2G  0 lvm  /
   sdb                 8:16   0   50G  0 disk
   └─sdb1              8:17   0   50G  0 part /mnt/resource
   ```

1. Use o seguinte comando para redimensionar o volume físico (PV):

   ```
   #pvresize /dev/sda4
   ```

   A saída a seguir mostra que o PV foi redimensionado para 99,02 GB.

   ```
   [user@myvm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized

   [user@myvm ~]# pvs
   PV         VG     Fmt  Attr PSize   PFree
   /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
   ```

1. No exemplo a seguir, **/dev/mapper/rootvg-rootlv** será redimensionado de 2 GB para 12 GB (um aumento de 10 GB) por meio do comando a seguir. Este comando também redimensionará o sistema de arquivos.

   ```
   #lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Saída de exemplo:

   ```
   [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```
         
1. Verifique se **/dev/mapper/rootvg-rootlv** tem um tamanho de sistema de arquivos maior usando o seguinte comando:

   ```
   #df -Th /
   ```

   Saída de exemplo:

   ```
   [user@myvm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [user@myvm ~]#
   ```

> [!NOTE]
> Para usar o mesmo procedimento para redimensionar qualquer outro volume lógico, altere o nome **lv** na etapa 7.

## <a name="next-steps"></a>Próximas etapas

- [Redimensionar disco](expand-disks.md)
