---
title: Como verificar o status de criptografia para Linux
description: Este artigo fornece instruções sobre a verificação do status de criptografia a partir da plataforma e do nível do SO.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123415"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Como verificar o status de criptografia para Linux 

**Este cenário se aplica às extensões ADE dual-pass e single-pass.**  
Este escopo documental é para validar o status de criptografia de uma máquina virtual usando diferentes métodos.

### <a name="environment"></a>Ambiente

- Distribuições Linux

### <a name="procedure"></a>Procedimento

Uma máquina virtual foi criptografada usando passe duplo ou passe único.

O status de criptografia pode ser validado durante ou após a criptografia usando diferentes métodos.

>[!NOTE] 
>Estamos usando variáveis ao longo do documento, substitua os valores de acordo.

### <a name="verification"></a>Verificação

A verificação pode ser feita a partir do Portal, PowerShell, AZ CLI e, ou do lado do Sistema Operacional VM. 

Esta verificação pode ser feita verificando os discos conectados a uma VM específica. 

Ou consultando as configurações de criptografia em cada disco individual se o disco está conectado ou não.

Abaixo os diferentes métodos de validações:

## <a name="using-the-portal"></a>Usar o portal

Valide o status de criptografia verificando a seção de extensões no portal Azure.

Dentro da seção **Extensões,** você verá a extensão ADE listada. 

Clique nele e dê uma olhada na **mensagem de status,** ela indicará o status de criptografia atual:

![Portal verificar número 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Na lista de extensões, você verá a versão de extensão ADE correspondente. A versão 0.x corresponde ao ADE Dual-Pass e a versão 1.x corresponde ao ADE Single-pass.

Você pode obter mais detalhes clicando na extensão e, em seguida, no *status detalhado de Exibir*.

Você verá um status mais detalhado do processo de criptografia no formato json:

![Portal verificar número 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Portal verificar número 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Outra maneira de validar o status de criptografia é dando uma olhada na seção **Discos.**

![Portal verificar número 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Esse status significa que os discos têm configurações de criptografia carimbadas, mas não que foram realmente criptografadas no nível do SO. Pelo design, os discos são carimbados primeiro e criptografados depois. Se o processo de criptografia falhar, os discos podem acabar carimbados, mas não criptografados. Para confirmar se os discos estão realmente criptografados, você pode verificar duas vezes a criptografia de cada disco no nível do SO.

## <a name="using-powershell"></a>Usando o PowerShell

Você pode validar o status **geral** de criptografia de uma VM criptografada usando os seguintes comandos PowerShell:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![verificar PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Você pode capturar as configurações de criptografia de cada disco individual usando os seguintes comandos PowerShell:

### <a name="single-pass"></a>Passe Único
Se um único passe, as configurações de criptografia serão carimbo em cada um dos discos (SO e Data), você pode capturar as configurações de criptografia de disco do SISTEMA OPERACIONAL em passe único da seguinte forma:

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Verifique o os único passe 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Se o disco não tiver as configurações de criptografia carimbadas, a saída estará vazia como mostrado abaixo:

![Configurações de criptografia do SISTEMA OPERACIONAL 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Capturar configurações de criptografia de disco de dados:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Verificar dados únicos ps 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Passe Duplo
No Dual Pass, as configurações de criptografia são carimbadas no modelo VM e não em cada disco individual.

Para verificar se as configurações de criptografia foram carimbadas no dual-pass, você pode usar os seguintes comandos:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Verifique o PowerShell 1 de passe duplo](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Discos não conectados

Verifique as configurações de criptografia para discos que não estão conectados a uma VM.

### <a name="managed-disks"></a>Discos gerenciados
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="using-az-cli"></a>Usando a Cli AZ

Você pode validar o status **geral** de criptografia de uma VM criptografada usando os seguintes comandos AZ CLI:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Verifique geral usando cli ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Passe Único
Você pode validar as configurações de criptografia de cada disco individual usando os seguintes comandos AZ CLI:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Configurações de criptografia de dados](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Caso o disco não tenha as configurações de criptografia carimbadas, ele será mostrado como "O disco não está criptografado"

Configurações detalhadas de status e criptografia:

Disco do SISTEMA OPERACIONAL:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Discos de dados:

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![CLI único de dados ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Passe Duplo

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Verifique o duplo ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) geral usando cli Você também pode verificar as configurações de criptografia no perfil de armazenamento do modelo VM do disco DO SISTEMA OPERACIONAL:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Verificar o perfil vm dual usando CLI ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Discos não conectados

Verifique as configurações de criptografia para discos que não estão conectados a uma VM.

### <a name="managed-disks"></a>Discos gerenciados

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Discos não gerenciados

Discos não gerenciados são arquivos VHD armazenados como blobs de páginas nas Contas de Armazenamento do Microsoft Azure.

Para obter os detalhes de um disco específico, você precisa fornecer:

O ID da conta de armazenamento que contém o disco.
Uma seqüência de conexão para essa conta de armazenamento em particular.
O nome do contêiner que armazena o disco.
O nome do disco.

Este comando lista todos os IDs de todas as suas contas de armazenamento:

```bash
az storage account list --query [].[id] -o tsv
```
Os IDs da conta de armazenamento estão listados no seguinte formulário:

/assinaturas/\<id de assinatura>/recursoGrupos/\<nome do grupo de recursos\<>/provedores/Microsoft.Armazenamento/armazenamentoContas/ nome da conta de armazenamento>

Selecione o ID apropriado e armazene-o em uma variável:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
A cadeia de conexão.

Este comando obtém a seqüência de conexão de uma conta de armazenamento específica e armazena-a em uma variável:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

O nome do contêiner.

O comando a seguir lista todos os contêineres em uma conta de armazenamento:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
O recipiente usado para discos é normalmente chamado de "vhds"

Armazene o nome do contêiner em uma variável 
```bash
ContainerName="name of the container"
```

O nome do disco.

Use este comando para listar todas as bolhas em um recipiente específico
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Escolha o disco que deseja consultar e armazene seu nome em uma variável.
```bash
DiskName="diskname.vhd"
```
Consultar as configurações de criptografia de disco
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Do Sistema Operacional
Valide se as partições do disco de dados forem criptografadas (e o disco do sistema operacional não estiver)

Quando uma partição/disco é criptografada, ela é exibida como tipo **de criptografia,** quando não está criptografada, ela é exibida como tipo **de parte/disco**

``` bash
lsblk
```

![Camada de Cripta Os ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Você pode obter mais detalhes usando a seguinte variante "lsblk". 

Você verá uma camada do tipo **cripta** que é montada pela extensão.

O exemplo a seguir mostra Volumes Lógicos e discos normais com um "**Crypto\_LUKS FSTYPE**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Os Crypt layer 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Como um passo extra, você também pode validar se o disco de dados tiver alguma tecla carregada

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

E quais dispositivos dm estão listados como cripta

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Próximas etapas

- [Guia de solução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)
