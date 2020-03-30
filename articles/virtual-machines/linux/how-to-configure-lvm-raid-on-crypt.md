---
title: Como configurar LVM e RAID on-crypt em uma VM Linux
description: Este artigo fornece instruções sobre a configuração de LVM e RAID em cripta em VMs Linux.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 78ba47ba887cf7c90adf70d9d444fbd8a3c721cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284901"
---
# <a name="how-to-configure-lvm-and-raid-on-crypt"></a>Como configurar LVM e RAID on-crypt

Este documento é um processo passo-a-passo sobre como executar o LVM em configurações de criptografia e Raid em configurações de criptografia.

### <a name="environment"></a>Ambiente

- Distribuições Linux
    - RHEL 7.6+
    - Ubuntu 18.04+
    - SUSE 12+
- Passe Único ADE
- ADE Dual Pass


## <a name="scenarios"></a>Cenários

**Este cenário é aplicável às extensões adee dual-pass e single-pass.**  

- Configurar LVM em cima de dispositivos criptografados (LVM-on-Crypt)
- Configure RAID em cima de dispositivos criptografados (RAID-on-Crypt)

Uma vez que os dispositivos subjacentes sejam criptografados, então você pode criar as estruturas LVM ou RAID em cima dessa camada criptografada. Os volumes físicos (PV) são criados em cima da camada criptografada.
Os Volumes Físicos são usados para criar o grupo de volumes.
Você cria os volumes e adiciona as entradas necessárias em /etc/fstab. 

![Verifique discos conectados powershell](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Da mesma forma, o dispositivo RAID é criado em cima da camada criptografada nos discos. Um sistema de arquivos é criado em cima do dispositivo RAID e adicionado a /etc/fstab como um dispositivo regular.

### <a name="considerations"></a>Considerações

O método recomendado para usar é LVM-on-Crypt.

O RAID é considerado quando o LVM não pode ser usado devido a limitações específicas de aplicativos/ambientes.

Você usará a opção EncryptFormatAll, as informações sobre https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vmseste recurso estão localizadas aqui: .

Embora este método possa ser feito ao também criptografar o Sistema Operacional, estamos apenas criptografando unidades de dados.

Este procedimento pressupõe que você já revisou https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux os https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstartpré-requisitos aqui mencionados: e aqui .

A versão ade dual pass está em via de depreciação e não deve mais ser usada em novas criptografias ADE.

### <a name="procedure"></a>Procedimento

Ao usar as configurações "on crypt", você estará seguindo o processo descrito abaixo:

>[!NOTE] 
>Estamos usando variáveis ao longo do documento, substitua os valores de acordo.
## <a name="general-steps"></a>Passos gerais
### <a name="deploy-a-vm"></a>Implantar uma máquina virtual 
>[!NOTE] 
>Embora isso seja opcional, recomendamos que você aplique isso em uma VM recém-implantada.

PowerShell
```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
CLI:
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
### <a name="attach-disks-to-the-vm"></a>Anexar discos ao vm:
Repita para $N número de novos discos que deseja anexar ao VM PowerShell
```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```
CLI:
```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```
### <a name="verify-the-disks-are-attached-to-the-vm"></a>Verifique se os discos estão conectados à VM:
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Verifique discos conectados powerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png) CLI:
```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Verifique discos anexados](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png) ao ![Portal CLI:](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png) Verifique discos anexados cli os:
```bash
lsblk 
```
![Verifique discos anexados portal](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)
### <a name="configure-the-disks-to-be-encrypted"></a>Configure os discos a serem criptografados
Essa configuração é feita para que o nível do sistema operacional, os discos correspondentes sejam configurados para uma criptografia ADE tradicional:

Os sistemas de arquivos são criados em cima dos discos.

Pontos de montagem temporários são criados para montar os sistemas de arquivos.

Os sistemas de arquivos estão configurados em /etc/fstab a ser montado na hora da inicialização.

Verifique a letra do dispositivo atribuída aos novos discos, neste exemplo estamos usando quatro discos de dados

```bash
lsblk 
```
![Verifique discos conectados ao sistema operacional](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-filesystem-on-top-of-each-disk"></a>Crie um sistema de arquivos em cima de cada disco.
Este comando iterates uma criação ext4 filesystem em cada disco definido na parte "in" do ciclo "para".
```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Verificar discos anexados](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png) o sistema operacional Encontre o UUID dos sistemas de arquivos criados recentemente, crie uma pasta temporária para montá-lo, adicione as entradas correspondentes em /etc/fstab e monte todos os sistemas de arquivos.

Este comando também itera em cada disco definido na parte "in" do ciclo "para":
```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 
### <a name="verify-the-disks-are-mounted-properly"></a>Verifique se os discos estão montados corretamente:
```bash
lsblk
```
![Verifique os sistemas de](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png) arquivos temporários montados e configurados:
```bash
cat /etc/fstab
```
![Verificar fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)
### <a name="encrypt-the-data-disks"></a>Criptografe os discos de dados:
PowerShell usando KEK:
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
CLI usando KEK:
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
![Verifique a](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png) criptografia ps CLI:
```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Verifique a](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png) criptografia ![do](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png) Portal CLI: Verifique o nível do sistema operacional de criptografia:
```bash
lsblk
```
![Verificar criptografia CLI](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

A extensão adicionará os sistemas de arquivos a "/var/lib/azure_disk_encryption_config/azure_crypt_mount" (uma criptografia antiga) ou adicionada a "/etc/crypttab" (novas criptografias).

Não modifique nenhum desses arquivos.

Este arquivo vai cuidar da ativação desses discos durante o processo de inicialização para que eles possam ser usados posteriormente por LVM ou RAID. 

Não se preocupe com os pontos de montagem neste arquivo, pois o ADE perderá a capacidade de obter os discos montados como um sistema de arquivos normal depois de criarmos um volume físico ou um dispositivo de ataque em cima desses dispositivos criptografados (que se livrarão do formato do sistema de arquivos que usamos durante o processo de preparação).
### <a name="remove-the-temp-folders-and-temp-fstab-entries"></a>Remova as pastas temporárias e as entradas de fstab de temperatura
Você desmonta os sistemas de arquivos nos discos que serão usados como parte do LVM
```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
E remova as entradas /etc/fstab:
```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Verifique se os discos não estão montados e se as entradas em /etc/fstab foram removidas
```bash
lsblk
```
![Verifique os sistemas de](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png) arquivos temporários desmontados e configurados:
```bash
cat /etc/fstab
```
![Verifique se as entradas de fstab temp são removidas](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)
## <a name="for-lvm-on-crypt"></a>Para LVM-on-cript
Agora que os discos subjacentes estão criptografados, você pode continuar a criar as estruturas LVM.

Em vez de usar o nome do dispositivo, use os caminhos /dev/mapper para cada um dos discos para criar um volume físico (na camada de criptografia na parte superior do disco não no disco em si).
### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Configure LVM em cima das camadas criptografadas
#### <a name="create-the-physical-volumes"></a>Crie os volumes físicos
Você receberá um aviso perguntando se está tudo bem para apagar a assinatura do sistema de arquivos. 

Você pode continuar digitando 'y' ou usar o eco "y" como mostrado:
```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Pvcreate](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)
>[!NOTE] 
>Os nomes /dev/mapper/dispositivo aqui precisam ser substituídos por seus valores reais com base na saída de lsblk.
#### <a name="verify-the-physical-volumes-information"></a>Verifique as informações de volumes físicos
```bash
pvs
```
![verificar volumes físicos 1](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)
#### <a name="create-the-volume-group"></a>Criar o grupo de volume
Crie o VG usando os mesmos dispositivos já inicializados
```bash
vgcreate vgdata /dev/mapper/
```
### <a name="check-the-volume-group-information"></a>Verifique as informações do grupo de volume
```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![verificar volumes físicos 2](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)
#### <a name="create-logical-volumes"></a>Criar volumes lógicos
```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 
#### <a name="check-the-logical-volumes-created"></a>Verifique os volumes lógicos criados
```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![verificar lvs](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)
#### <a name="create-filesystems-on-top-of-the-logical-volumes-structures"></a>Criar sistemas de arquivos em cima da estrutura de volumes lógicos(s)
```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```
#### <a name="create-the-mount-points-for-the-new-filesystems"></a>Crie os pontos de montagem para os novos sistemas de arquivos
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
#### <a name="verify-that-the-new-filesystems-are-mounted"></a>Verifique se os novos sistemas de arquivos estão montados
```bash
lsblk -fs
df -h
```
![verificar volumes](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png) lógicos Nesta variação do lsblk, estamos listando os dispositivos que mostram as dependências da ordem inversa, essa opção ajuda a identificar os dispositivos agrupados pelo volume lógico em vez dos nomes originais dos dispositivos /dev/sd[disk].

Importante: Certifique-se de que a opção "nofail" seja adicionada às opções de ponto de montagem dos volumes LVM criados em cima de um dispositivo criptografado ADE. É importante evitar que o Sistema Operacional se acomode durante o processo de inicialização (ou no modo de manutenção). 

O disco criptografado é desbloqueado no final do processo de inicialização, os volumes LVM e os sistemas de arquivos serão montados automaticamente.

Se a opção nofail não for usada, o sistema operacional nunca entrará no estágio em que o ADE é iniciado e os discos de dados serão desbloqueados e montados.

Você pode testar a reinicialização da VM e validar os sistemas de arquivos também estão sendo montados automaticamente após o tempo de inicialização. 

Leve em consideração que este processo pode levar vários minutos, dependendo do número de sistemas de arquivos e dos tamanhos
#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Reinicie a VM e verifique após a reinicialização
```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="for-raid-on-crypt"></a>Para RAID-on-Crypt
Agora que os discos subjacentes são criptografados, você pode continuar a criar as estruturas RAID, assim como lvm, em vez de usar o nome do dispositivo, usar os caminhos /dev/mapper para cada um dos discos.

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
![mdadm criar](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)
>[!NOTE] 
>Os nomes /dev/mapper/dispositivo aqui precisam ser substituídos por seus valores reais com base na saída de lsblk.
#### <a name="checkmonitor-the-raid-creation"></a>Verifique/monitore a criação do RAID:
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![verificar mdadm](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)
#### <a name="create-a-filesystem-on-top-of-the-new-raid-device"></a>Criar um sistema de arquivos em cima do novo dispositivo Raid:
```bash
mkfs.ext4 /dev/md10
```
Crie um novo ponto de montagem para o sistema de arquivos, adicione o novo sistema de arquivos a /etc/fstab e monte-o
```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```
Verifique se os novos sistemas de arquivos estão montados
```bash
lsblk -fs
df -h
```
![verificar mdadm](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Importante: Certifique-se de que a opção "nofail" seja adicionada às opções de ponto de montagem dos volumes RAID criados em cima de um dispositivo criptografado ADE. 

É muito importante evitar que o Sistema Operacional se acomode durante o processo de inicialização (ou no modo de manutenção). 

O disco criptografado será desbloqueado no final do processo de inicialização e os volumes RAID e sistemas de arquivos serão automaticamente montados até que sejam desbloqueados pelo ADE, se a opção nofail não for usada.

O sistema operacional nunca entrará no estágio em que o ADE é iniciado, e os discos de dados são desbloqueados e montados.

Você pode testar a reinicialização da VM e validar os sistemas de arquivos também estão sendo montados automaticamente após o tempo de inicialização. Leve em consideração que este processo pode levar vários minutos, dependendo do número de sistemas de arquivos e dos tamanhos
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

