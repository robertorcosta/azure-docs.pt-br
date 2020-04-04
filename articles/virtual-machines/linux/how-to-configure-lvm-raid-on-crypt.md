---
title: Configurar LVM e RAID em dispositivos criptografados - Criptografia de disco azure
description: Este artigo fornece instruções para configurar LVM e RAID em dispositivos criptografados para VMs Linux.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 4e342ff44af38b8e79dc8695c1270b1f5c68e0a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657435"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Configurar LVM e RAID em dispositivos criptografados

Este artigo é um processo passo-a-passo de como executar o LVM (Logical Volume Management, gerenciamento de volumes lógicos) e RAID em dispositivos criptografados. O processo se aplica aos seguintes ambientes:

- Distribuições Linux
    - RHEL 7.6+
    - Ubuntu 18.04+
    - SUSE 12+
- Extensão de passe único de criptografia de disco azure
- Extensão de dual-pass de criptografia de disco azure


## <a name="scenarios"></a>Cenários

Os procedimentos deste artigo corroboram os seguintes cenários:  

- Configure lvM em cima de dispositivos criptografados (LVM-on-crypt)
- Configure RAID em cima de dispositivos criptografados (RAID-on-crypt)

Depois que o dispositivo ou dispositivo subjacente forcriptoado, então você pode criar as estruturas LVM ou RAID em cima dessa camada criptografada. 

Os volumes físicos (PVs) são criados em cima da camada criptografada. Os volumes físicos são usados para criar o grupo de volumes. Você cria os volumes e adiciona as entradas necessárias em /etc/fstab. 

![Diagrama das camadas das estruturas LVM](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Da mesma forma, o dispositivo RAID é criado em cima da camada criptografada nos discos. Um sistema de arquivos é criado em cima do dispositivo RAID e adicionado a /etc/fstab como um dispositivo regular.

## <a name="considerations"></a>Considerações

Recomendamos que você use LVM-on-cript. RAID é uma opção quando o LVM não pode ser usado devido a limitações específicas de aplicativos ou ambientes.

Você usará a opção **EncryptFormatAll.** Para obter mais informações sobre essa opção, consulte [Use o recurso EncryptFormatAll para discos de dados em VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms).

Embora você possa usar este método quando você também está criptografando o Sistema Operacional, estamos apenas criptografando unidades de dados aqui.

Os procedimentos supõem que você já analisou os pré-requisitos em [cenários de Criptografia de Disco Do Azure em VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux) e no [Quickstart: Crie e criptografe uma VM Linux com o Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart).

A versão dual-pass de criptografia de disco do Azure está em um caminho de depreciação e não deve mais ser usada em novas criptografias.

## <a name="general-steps"></a>Passos gerais

Quando estiver usando as configurações "on-crypt", use o processo descrito nos procedimentos a seguir.

>[!NOTE] 
>Estamos usando variáveis ao longo do artigo. Substitua os valores em conformidade.

### <a name="deploy-a-vm"></a>Implantar uma máquina virtual 
Os seguintes comandos são opcionais, mas recomendamos que você os aplique em uma máquina virtual (VM) recém-implantada.

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
CLI do Azure:

```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Anexar discos à VM
Repita os seguintes `$N` comandos para o número de novos discos que deseja anexar à VM.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

CLI do Azure:

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Verifique se os discos estão conectados à VM
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Lista de discos conectados no PowerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

CLI do Azure:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Lista de discos anexados na CLI do Azure](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portal:

![Lista de discos anexados no portal](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

sistema operacional:

```bash
lsblk 
```
![Lista de discos conectados no sistema operacional](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Configure os discos a serem criptografados
Esta configuração é feita no nível do sistema operacional. Os discos correspondentes são configurados para uma criptografia tradicional através da Criptografia de Disco do Azure:

- Os sistemas de arquivos são criados em cima dos discos.
- Pontos de montagem temporários são criados para montar os sistemas de arquivos.
- Os sistemas de arquivos estão configurados em /etc/fstab a ser montado na hora da inicialização.

Verifique a letra do dispositivo atribuída aos novos discos. Neste exemplo, estamos usando quatro discos de dados.

```bash
lsblk 
```
![Discos de dados conectados ao SISTEMA OPERACIONAL](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Crie um sistema de arquivos em cima de cada disco
Este comando itera a criação de um sistema de arquivos ext4 em cada disco definido na parte "in" do ciclo "para".

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Criação de um sistema de arquivos ext4](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Encontre o identificador universalmente exclusivo (UUID) dos sistemas de arquivos que você criou recentemente, crie uma pasta temporária, adicione as entradas correspondentes em /etc/fstab e monte todos os sistemas de arquivos.

Este comando também itera em cada disco definido na parte "in" do ciclo "para":

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Verifique se os discos estão montados corretamente
```bash
lsblk
```
![Lista de sistemas de arquivos temporários montados](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Verifique também se os discos estão configurados:

```bash
cat /etc/fstab
```
![Informações de configuração via fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Criptografe os discos de dados
PowerShell usando uma chave de criptografia (KEK):

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

Azure CLI usando um KEK:

```bash
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
### <a name="verify-the-encryption-status"></a>Verifique o status da criptografia

Continue até o próximo passo somente quando todos os discos estiverem criptografados.

PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Status de criptografia no PowerShell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

CLI do Azure:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Status de criptografia no Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portal:

![Status de criptografia no portal](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Nível do Sistema Operacional:

```bash
lsblk
```
![Status de criptografia no Sistema Operacional](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

A extensão adicionará os sistemas de arquivos a /var/lib/azure_disk_encryption_config/azure_crypt_mount (uma criptografia antiga) ou a /etc/crypttab (novas criptografias).

>[!NOTE] 
>Não modifique nenhum desses arquivos.

Este arquivo cuidará da ativação desses discos durante o processo de inicialização para que o LVM ou RAID possam usá-los mais tarde. 

Não se preocupe com os pontos de montagem neste arquivo. A Criptografia de Disco do Azure perderá a capacidade de obter os discos montados como um sistema de arquivos normal depois de criarmos um volume físico ou um dispositivo RAID em cima desses dispositivos criptografados. (Isso removerá o formato do sistema de arquivos que usamos durante o processo de preparação.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Remova as pastas temporárias e as entradas temporárias de fstab
Você desmonta os sistemas de arquivos nos discos que serão usados como parte do LVM.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
E remova as entradas /etc/fstab:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Verifique se os discos não estão montados e se as entradas em /etc/fstab foram removidas

```bash
lsblk
```
![Verificação de que os sistemas temporários de arquivos estão desmontados](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

E verifique se os discos estão configurados:
```bash
cat /etc/fstab
```
![Verificação de que as entradas temporárias de fstab são removidas](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Etapas para LVM-on-crypt
Agora que os discos subjacentes estão criptografados, você pode criar as estruturas LVM.

Em vez de usar o nome do dispositivo, use os caminhos /dev/mapper para cada um dos discos para criar um volume físico (na camada de criptografia na parte superior do disco, não no disco em si).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Configure LVM em cima das camadas criptografadas
#### <a name="create-the-physical-volumes"></a>Crie os volumes físicos
Você receberá um aviso que pergunta se está tudo bem para apagar a assinatura do sistema de arquivos. Continue digitando **y**, ou use **o eco "y"** como mostrado:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Verificação de que um volume físico foi criado](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>Os nomes /dev/mapper/dispositivo aqui precisam ser substituídos por seus valores reais com base na saída de **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Verifique as informações para volumes físicos
```bash
pvs
```

![Informações para volumes físicos](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Criar o grupo de volume
Crie o grupo de volume usando os mesmos dispositivos já inicializados:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Verifique as informações para o grupo de volume

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Informações para o grupo de volume](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Criar volumes lógicos

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Verifique os volumes lógicos criados

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Informações para volumes lógicos](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Criar sistemas de arquivos em cima das estruturas para volumes lógicos

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Crie os pontos de montagem para os novos sistemas de arquivos

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Adicione os novos sistemas de arquivos a /etc/fstab e monte-os

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Verifique se os novos sistemas de arquivos estão montados

```bash
lsblk -fs
df -h
```
![Informações para sistemas de arquivos montados](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

Nesta variação de **lsblk,** estamos listando os dispositivos mostrando as dependências em ordem inversa. Essa opção ajuda a identificar os dispositivos agrupados pelo volume lógico em vez dos nomes originais dos dispositivos /dev/sd[disk].

É importante garantir que a opção **nofail** seja adicionada às opções de ponto de montagem dos volumes LVM criados em cima de um dispositivo criptografado através do Azure Disk Encryption. Evita que o Sistema Operacional se acomode durante o processo de inicialização (ou no modo de manutenção).

Se você não usar a opção **nofail:**

- O sistema operacional nunca entrará no estágio em que o Azure Disk Encryption é iniciado e os discos de dados são desbloqueados e montados. 
- Os discos criptografados serão desbloqueados no final do processo de inicialização. Os volumes de LVM e os sistemas de arquivos serão automaticamente montados até que o Azure Disk Encryption os desbloqueie. 

Você pode testar a reinicialização da VM e validar que os sistemas de arquivos também estão sendo montados automaticamente após o tempo de inicialização. Esse processo pode levar vários minutos, dependendo do número e tamanhos dos sistemas de arquivos.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Reinicie a VM e verifique após a reinicialização

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Etapas para RAID-on-crypt
Agora que os discos subjacentes estão criptografados, você pode continuar a criar as estruturas RAID. O processo é o mesmo do LVM, mas em vez de usar o nome do dispositivo, use os caminhos /dev/mapper para cada disco.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Configure RAID na parte superior da camada criptografada dos discos
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Informações para RAID configurado através do comando mdadm](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>Os nomes /dev/mapper/dispositivo aqui precisam ser substituídos por seus valores reais, com base na saída de **lsblk**.

### <a name="checkmonitor-raid-creation"></a>Criação de RAID de verificação/monitor
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Status do RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Crie um sistema de arquivos em cima do novo dispositivo RAID
```bash
mkfs.ext4 /dev/md10
```

Crie um novo ponto de montagem para o sistema de arquivos, adicione o novo sistema de arquivos a /etc/fstab e monte-o:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Verifique se o novo sistema de arquivos está montado:

```bash
lsblk -fs
df -h
```
![Informações para sistemas de arquivos montados](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

É importante garantir que a opção **nofail** seja adicionada às opções de ponto de montagem dos volumes RAID criados em cima de um dispositivo criptografado através do Azure Disk Encryption. Evita que o Sistema Operacional se acomode durante o processo de inicialização (ou no modo de manutenção).

Se você não usar a opção **nofail:**

- O sistema operacional nunca entrará no estágio em que o Azure Disk Encryption é iniciado e os discos de dados são desbloqueados e montados.
- Os discos criptografados serão desbloqueados no final do processo de inicialização. Os volumes RAID e os sistemas de arquivos serão automaticamente montados até que o Azure Disk Encryption os desbloqueie.

Você pode testar a reinicialização da VM e validar que os sistemas de arquivos também estão sendo montados automaticamente após o tempo de inicialização. Esse processo pode levar vários minutos, dependendo do número e tamanhos dos sistemas de arquivos.

```bash
shutdown -r now
```

E quando você pode fazer login:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Próximas etapas

- [Guia de solução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)

