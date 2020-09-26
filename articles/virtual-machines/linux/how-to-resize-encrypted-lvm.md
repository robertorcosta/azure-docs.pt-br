---
title: Como redimensionar discos de gerenciamento de volumes lógicos criptografados com Azure Disk Encryption
description: Este artigo fornece instruções para redimensionar discos do ADE criptografados usando o gerenciamento de volume lógico
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ba652b9424b8d5ce1b6a2c5b7d70b8fd9e999323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341029"
---
# <a name="how-to-resize-logical-volume-management-devices-encrypted-with-azure-disk-encryption"></a>Como redimensionar dispositivos de gerenciamento de volume lógico criptografados com Azure Disk Encryption

Este artigo é um processo passo a passo sobre como redimensionar discos de dados com criptografia ADE usando o LVM (gerenciamento de volume lógico) no Linux, aplicável a vários cenários.

O processo se aplica aos seguintes ambientes:

- Distribuições Linux
    - RHEL 7+
    - Ubuntu 16 +
    - SUSE 12 +
- Azure Disk Encryption extensão de passagem única
- Extensão de passagem dupla Azure Disk Encryption

## <a name="considerations"></a>Considerações

Este documento pressupõe que:

1. Há uma configuração de LVM existente.
   
   Marque [Configurar LVM em uma VM do Linux](configure-lvm.md) para obter mais informações sobre como configurar o LVM em uma VM do Linux.

2. Os discos já estão criptografados usando Azure Disk Encryption Marque [Configurar LVM em cript](how-to-configure-lvm-raid-on-crypt.md) . para obter informações sobre como configurar o LVM em cript.

3. Você tem a experiência de Linux e LVM necessária para seguir estes exemplos.

4. Você entende que a recomendação para usar discos de dados no Azure, conforme mencionado em [solucionar problemas de nomes de dispositivos](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems), é usar os caminhos/dev/disk/scsi1/.

## <a name="scenarios"></a>Cenários

Os procedimentos neste artigo se aplicam aos seguintes cenários:

### <a name="for-traditional-lvm-and-lvm-on-crypt-configurations"></a>Para configurações tradicionais de LVM e LVM-criptografado

- Estendendo um volume lógico quando há espaço disponível no VG

### <a name="for-traditional-lvm-encryption-the-logical-volumes-are-encrypted-not-the-whole-disk"></a>Para a criptografia LVM tradicional (os volumes lógicos são criptografados, não o disco inteiro)

- Estendendo um volume LVM tradicional adicionando um novo VP
- Estendendo um volume LVM tradicional redimensionando um PV existente

### <a name="for-lvm-on-crypt-recommended-method-the-entire-disk-is-encrypted-not-only-the-logical-volume"></a>Para LVM-on-cript (método recomendado, o disco inteiro é criptografado, não apenas o volume lógico)

- Estendendo um LVM no volume cript. adicionando um novo VP
- Estendendo um LVM no volume cript. redimensionando um PV existente

> [!NOTE]
> Não é recomendável misturar criptografia de LVM tradicional e LVM em cript na mesma VM.

> [!NOTE]
> Esses exemplos usam os nomes pré-existentes para discos, volumes físicos, grupos de volumes, volumes lógicos, sistemas de sistema, UUIDs e montagem, você precisa substituir os valores fornecidos nesses exemplos para se ajustar ao seu ambiente.

#### <a name="extending-a-logical-volume-when-theres-available-space-in-the-vg"></a>Estendendo um volume lógico quando há espaço disponível no VG

Método tradicional usado para redimensionar volumes lógicos, ele pode ser aplicado a discos não criptografados, volumes criptografados de LVM tradicionais e configurações de LVM em criptografia.

1. Verifique o tamanho atual do sistema de arquivos que desejamos aumentar:

    ``` bash
    df -h /mountpoint
    ```

    ![cenárioa-check-FS1](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Verifique se o VG tem espaço suficiente para aumentar o LV

    ``` bash
    vgs
    ```

    ![cenárioa-check-VG](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Você também pode usar "vgdisplay"

    ``` bash
    vgdisplay vgname
    ```

    ![cenárioa-check-vgdisplay](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Identificar qual volume lógico precisa ser redimensionado

    ``` bash
    lsblk
    ```

    ![cenárioa-check-lsblk1](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Para LVM-on-cript, a diferença está nessa saída, que mostra que a camada criptografada está na camada criptografada que cobre o disco inteiro

    ![cenárioa-check-lsblk2](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Verificar o tamanho do volume lógico

    ``` bash
    lvdisplay lvname
    ```

    ![cenárioa-check-lvdisplay01](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Aumentar o tamanho LV usando "-r" para redimensionamento online do sistema de arquivos

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![cenárioa-redimensionar-LV](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Verifique os novos tamanhos para o LV e o sistema de arquivos

    ``` bash
    df -h /mountpoint
    ```

    ![cenárioa-check-FS](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    O novo tamanho é refletido, indica um redimensionamento bem-sucedido do LV e do sistema de arquivos

7. Você pode verificar as informações de LV novamente para confirmar as alterações no nível de LV

    ``` bash
    lvdisplay lvname
    ```

    ![cenárioa-check-lvdisplay2](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extending-a-traditional-lvm-volume-adding-a-new-pv"></a>Estendendo um volume LVM tradicional adicionando um novo VP

Aplicável quando você precisa adicionar um novo disco para aumentar o tamanho do grupo de volumes.

1. Verifique o tamanho atual do sistema de arquivos que desejamos aumentar:

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-check-FS](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Verificar a configuração do volume físico atual

    ``` bash
    pvs
    ```

    ![scenariob-check-PVS](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Verificar as informações de VG atuais

    ``` bash
    vgs
    ```

    ![scenariob-check-VGS](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Verificar a lista de discos atual

    Os discos de dados devem ser identificados verificando os dispositivos em/dev/disk/Azure/scsi1/

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-check-SCS1](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Verificar a saída de lsblk 

    ``` bash
    lsbk
    ```

    ![scenariob-check-lsblk](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Anexar o novo disco à VM

    Siga até a etapa 4 do documento a seguir

   - [Anexar um disco a uma VM](attach-disk-portal.md)

7. Depois que o disco for anexado, verifique a lista de discos, observe o novo disco

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-check-scsi12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![scenariob-check-lsblk12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Criar um novo VP sobre o novo disco de dados

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![scenariob-pvcreate](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Esse método usa o disco inteiro como um PV sem uma partição, opcionalmente, você pode usar "fdisk" para criar uma partição e, em seguida, usar essa partição para o "pvcreate".

9. Verifique se o PV foi adicionado à lista PV.

    ``` bash
    pvs
    ```

    ![scenariob-check-pvs1](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Estenda o VG adicionando o novo PV a ele

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![scenariob-VG-extend](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Verificar o novo tamanho de VG

    ``` bash
    vgs
    ```

    ![scenariob-check-VGS1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Use lsblk para identificar qual LV precisa ser redimensionado

    ``` bash
    lsblk
    ```

    ![scenariob-check-lsblk1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Estenda o tamanho LV usando "-r" para fazer um aumento online do sistema de arquivos

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenariob-lvextend](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Verificar os novos tamanhos LV e FileSystem

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-check-FS1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    É importante saber que quando o ADE é usado em configurações de LVM tradicionais, a camada criptografada é criada no nível LV, não no nível do disco.

    Neste ponto, a camada criptografada é expandida para o novo disco.
    O disco de dados real não tem nenhuma configuração de criptografia no nível de plataforma, portanto, seu status de criptografia não é atualizado.

    >[!NOTE]
    >Esses são alguns dos motivos pelos quais o LVM-on-cript é a abordagem recomendada. 

15. Verifique as informações de criptografia do portal:

    ![scenariob-check-portal1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Você precisa adicionar um novo LV e habilitar a extensão na VM para atualizar as configurações de criptografia no disco.
    
16. Adicionar um novo LV, criar um sistema de arquivos nele e adicioná-lo a/etc/fstab

17. Defina a extensão de criptografia novamente para carimbar as configurações de criptografia no novo disco de dados no nível da plataforma.

    Exemplo:

    CLI

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Verifique as informações de criptografia do portal:

    ![scenariob-check-portal2](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

19. Depois que as configurações de criptografia forem atualizadas, você poderá excluir o novo LV, também precisará excluir a entrada de/etc/fstab e/etc/crypttab que foram criadas para ela.

    ![scenariob-Delete-fstab-crypttab](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

20. Desmontar o volume lógico

    ``` bash
    umount /mountpoint
    ```

21. Fechar a camada criptografada do volume

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

22. Excluir o LV

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extending-a-traditional-lvm-volume-resizing-an-existing-pv"></a>Estendendo um volume LVM tradicional redimensionando um PV existente

Determinados cenários ou limitações exigem que você redimensione um disco existente.

1. Identificar seus discos criptografados

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenarioc-check-scsi1](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![scenarioc-check-lsblk](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Verificar as informações de PV

    ``` bash
    pvs
    ```

    ![scenarioc-check-PVS](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Todo o espaço em todos os PVs é usado no momento

3. Verifique as informações do VGs

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![scenarioc-check-VGS](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Verificar os tamanhos de disco, você pode usar fdisk ou lsblk para listar os tamanhos de unidade

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-cheque-fdisk](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Identificamos quais PVs estão associados a qual LVs usando o lsblk-FS, você pode identificá-lo também executando "lvdisplay"

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![check-lvdisplay](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    Nesse caso em particular, todas as quatro unidades de dados fazem parte do mesmo VG e um único LV, sua configuração pode ser diferente do exemplo.

5. Verifique a utilização atual do sistema de arquivos:

    ``` bash
    df -h /datalvm*
    ```

    ![scenarioc-check-DF](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Redimensione os discos de dados:

    Você pode fazer referência aos [discos de expansão do Linux](expand-disks.md) (apenas consulte o redimensionamento de disco), você pode usar o portal, a CLI ou o PowerShell para executar esta etapa.

    >[!NOTE]
    >Considere que as operações de redimensionamento em discos virtuais não podem ser executadas com a VM em execução. Será necessário desalocar sua VM para esta etapa

7. Quando os discos forem redimensionados para o valor necessário, inicie a VM e verifique os novos tamanhos usando fdisk

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-check-fdisk1](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    Nesse caso em particular,/dev/sdd foi redimensionado de 5G para 20G

8. Verificar o tamanho atual do VP

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-check-pvdisplay](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Mesmo que o disco tenha sido redimensionado, o PV ainda terá o tamanho anterior.

9. Redimensionar o PV

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![scenarioc-check-pvresize](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Verificar o tamanho do PV

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-check-pvdisplay1](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Aplique o mesmo procedimento para todos os discos que você deseja redimensionar.

11. Verifique as informações do VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioc-check-vgdisplay1](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    O VG agora tem espaço a ser alocado para o LVs

12. Redimensionar o LV

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![scenarioc-check-lvresize1](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Verificar tamanho FS

    ``` bash
    df -h /datalvm2
    ```

    ![scenarioc-check-Df3](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extending-an-lvm-on-crypt-volume-adding-a-new-pv"></a>Estendendo um volume LVM-em-criptografado adicionando um novo VP

Esse método segue com mais detalhes as etapas de [Configurar o LVM em cript](how-to-configure-lvm-raid-on-crypt.md) para adicionar um novo disco e configurá-lo em uma configuração LVM em criptografia.

Você pode usar esse método para adicionar espaço a um LV já existente ou, em vez disso, você pode criar um novo VGs ou LVs.

1. Verificar o tamanho atual de seu VG

    ``` bash
    vgdisplay vgname
    ```

    ![Scenario-check-vg01](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Verifique o tamanho do FS e do LV que você deseja aumentar

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Scenario-check-lv01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![Scenario-check-FS01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Adicione um novo disco de dados à VM e identifique-o.

    Verifique os discos antes de adicionar o disco

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Scenario-check-newdisk01](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Verifique os discos antes de adicionar o novo disco

    ``` bash
    lsblk
    ```

    ![Scenario-check-newdisk002](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Adicione um novo disco com o PowerShell, o CLI do Azure ou o portal do Azure. Verifique como [anexar um disco](attach-disk-portal.md) para referência sobre como adicionar discos a uma VM.

    Seguindo o esquema de nome de kernel para os dispositivos, a nova unidade normalmente receberá a próxima carta disponível, nesse caso em particular, o novo disco adicionado será SSD.

4. Verifique os discos depois de adicionar o novo disco

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Scenario-check-newdisk02](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)-

    ``` bash
    lsblk
    ```

    ![Scenario-check-newdisk003](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Criar um sistema de arquivos na parte superior do disco adicionado recentemente

    Corresponder o disco adicionado recentemente aos dispositivos vinculados em/dev/disk/Azure/scsi1/

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![Scenario-check-newdisk03](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![cenário-mkfs01](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Criar um novo mountpoint temporário para o novo disco adicionado

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Adicionar o sistema de arquivos criado recentemente a/etc/fstab

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Montar o novo FS criado usando Mount-a

    ``` bash
    mount -a
    ```

9. Verifique se o novo FS adicionado está montado

    ``` bash
    df -h
    ```

    ![redimensionar-LVM-cenário-DF](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![redimensionar-LVM-Scenario-lsblk](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Iniciar novamente a criptografia iniciada anteriormente para unidades de dados

    Para o LVM-on-cript, a recomendação é usar EncryptFormatAll caso contrário, uma criptografia dupla pode ocorrer durante a configuração de discos adicionais.

    Para obter informações sobre o uso, consulte [Configurar o LVM em cript](how-to-configure-lvm-raid-on-crypt.md).

    Exemplo:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    Quando a criptografia for concluída, você verá uma camada cript no disco recém-adicionado

    ``` bash
    lsblk
    ```

    ![cenário-lsblk2](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Desmontar a camada criptografada do novo disco

    ``` bash
    umount ${newmount}
    ```

12. Verificar as informações de PVS atuais

    ``` bash
    pvs
    ```

    ![cenário-currentpvs](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Criar um PV sobre a camada criptografada do disco

    Pegue o nome do dispositivo do comando lsblk anterior e adicione/dev/mapper na frente do nome do dispositivo para criar o PV

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![cenário-pvcreate](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Você verá um aviso sobre como apagar a assinatura atual do ext4 FS, isso é esperado, responder y a esta pergunta

14. Verificar se o novo PV foi adicionado à configuração do LVM

    ``` bash
    pvs
    ```

    ![cenário-newpv](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Adicionar o novo PV ao VG que você precisa aumentar

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![cenário-vgextent](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Verifique o novo tamanho e o espaço livre do VG

    ``` bash
    vgdisplay vgname
    ```

    ![cenário-vgdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Observe o aumento na contagem total de PE e o PE/tamanho livre

17. Aumente o tamanho do LV e do sistema de arquivos usando a opção-r em lvextend (neste exemplo, estamos pegando o espaço total disponível no VG e adicionando-o ao volume lógico fornecido)

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![cenário-lvextend](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

18. Verificar o tamanho do LV

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![cenário-lvdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

19. Verifique o tamanho do sistema de arquivos que você acabou de redimensionar

    ``` bash
    df -h mountpoint
    ```

    ![cenário-DF1](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

20. Verifique se a camada LVM é criada na parte superior da camada criptografada

    ``` bash
    lsblk
    ```

    ![cenário-lsblk3](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Usar lsblk sem opções mostrará os pontos de montagem várias vezes, uma vez que ele classifica por volumes lógicos e de dispositivo, talvez você queira usar o lsblk-FS,-s reverte a classificação para que os montagem sejam mostrados uma vez, os discos serão mostrados várias vezes.

    ``` bash
    lsblk -fs
    ```

    ![cenário-lsblk4](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extending-an-lvm-on-crypt-volume-resizing-an-existing-pv"></a>Estendendo um LVM no volume cript. redimensionando um PV existente

1. Identificar seus discos criptografados

    ``` bash
    lsblk
    ```

    ![scenariof-lsblk01](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![scenariof-lsblk012](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Verifique as informações de PV

    ``` bash
    pvs
    ```

    ![scenariof-pvs1](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Verifique suas informações do VG

    ``` bash
    vgs
    ```

    ![scenariof-VGS](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Verifique suas informações de LV

    ``` bash
    lvs
    ```

    ![scenariof-LVS](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Verificar a utilização do sistema de arquivos

    ``` bash
    df -h /mountpoint(s)
    ```

    ![LVM-scenariof-FS](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Verifique os tamanhos dos discos

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk01](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Redimensionar o disco de dados

    Você pode fazer referência aos [discos de expansão do Linux](expand-disks.md) (apenas consulte o redimensionamento de disco), você pode usar o portal, a CLI ou o PowerShell para executar essa etapa.

    >[!NOTE]
    >Considere que as operações de redimensionamento em discos virtuais não podem ser executadas com a VM em execução. Será necessário desalocar sua VM para esta etapa

8. Verifique os tamanhos dos discos

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk02](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Observe que (nesse caso) ambos os discos foram redimensionados de 2GB para 4 GB, no entanto, o tamanho FS, LV e VP permanece o mesmo.

9. Verificar o tamanho atual do VP

    Lembre-se de que, em LVM-on-cript, o PV é o dispositivo/dev/mapper/, não o dispositivo/dev/SD *

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-PVS](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Redimensionar o PV

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![scenariof-Resize-PV](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Verificar o tamanho do PV após o redimensionamento

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-PV](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Redimensionar a camada criptografada no PV

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Aplique o mesmo procedimento para todos os discos que você deseja redimensionar.

13. Verifique suas informações do VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenariof-VG](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    O VG agora tem espaço a ser alocado para o LVs

14. Verificar as informações de LV

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-LV](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Verificar a utilização de FS

    ``` bash
    df -h /mountpoint
    ```

    ![scenariof-FS](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Redimensionar o LV

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![scenariof-lvresize](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Estamos usando a opção-r para também executar o redimensionamento FS

17. Verificar as informações de LV

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lvsize](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Verificar a utilização do sistema de arquivos

    ``` bash
    df -h /mountpoint
    ```

    ![criar sistema de arquivos](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

    Aplicar o mesmo procedimento de redimensionamento a qualquer lv adicional que o exija

## <a name="next-steps"></a>Próximas etapas

- [Guia de solução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)
