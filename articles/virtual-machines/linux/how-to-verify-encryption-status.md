---
title: Como verificar o status de criptografia para Linux
description: Este artigo fornece instruções sobre como verificar o status de criptografia do nível da plataforma e do sistema operacional.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123415"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Como verificar o status de criptografia para Linux 

**Esse cenário se aplica a extensões de passagem dupla e de passagem única do ADE.**  
Esse escopo de documento é validar o status de criptografia de uma máquina virtual usando métodos diferentes.

### <a name="environment"></a>Ambiente

- Distribuições Linux

### <a name="procedure"></a>Procedimento

Uma máquina virtual foi criptografada usando passagem dupla ou passagem única.

O status de criptografia pode ser validado durante ou após a criptografia usando métodos diferentes.

>[!NOTE] 
>Estamos usando variáveis em todo o documento, substitua os valores de acordo.

### <a name="verification"></a>Verificação

A verificação pode ser feita no portal, no PowerShell, no AZ CLI e no no lado do sistema operacional da VM. 

Essa verificação pode ser feita verificando os discos anexados a uma VM específica. 

Ou consultando as configurações de criptografia em cada disco individual, se o disco está anexado ou desanexado.

Abaixo dos diferentes métodos de validação:

## <a name="using-the-portal"></a>Usar o portal

Valide o status de criptografia verificando a seção extensões no portal do Azure.

Dentro da seção **extensões** , você verá a extensão Ade listada. 

Clique nele e dê uma olhada na **mensagem de status**, indicando o status de criptografia atual:

![Cheque do portal número 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Na lista de extensões, você verá a versão de extensão ADE correspondente. A versão 0. x corresponde a uma passagem dupla e a versão 1. x corresponde à passagem única de ADE.

Você pode obter mais detalhes clicando na extensão e, em seguida, em *Exibir status detalhado*.

Você verá um status mais detalhado do processo de criptografia no formato JSON:

![Cheque do portal número 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Cheque do portal número 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Outra maneira de validar o status de criptografia é examinando a seção **discos** .

![Verificação do portal número 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Esse status significa que os discos têm configurações de criptografia carimbadas, mas não que foram realmente criptografadas no nível do sistema operacional. Por design, os discos são marcados primeiro e criptografados mais tarde. Se o processo de criptografia falhar, os discos poderão terminar de ser carimbados, mas não criptografados. Para confirmar se os discos estão verdadeiramente criptografados, você pode verificar a criptografia de cada disco no nível do sistema operacional.

## <a name="using-powershell"></a>Usando o PowerShell

Você pode validar o status de criptografia **geral** de uma VM criptografada usando os seguintes comandos do PowerShell:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![verificar o PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Você pode capturar as configurações de criptografia de cada disco individual usando os seguintes comandos do PowerShell:

### <a name="single-pass"></a>Passagem única
Se houver uma passagem única, as configurações de criptografia serão carimbadas em cada um dos discos (so e dados), você poderá capturar as configurações de criptografia de disco do sistema operacional em uma única passagem da seguinte maneira:

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
![Verificar o sistema operacional único Pass 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Se o disco não tiver as configurações de criptografia carimbadas, a saída estará vazia, conforme mostrado abaixo:

![Configurações de criptografia do SO 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Capturar configurações de criptografia de discos de dados:

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
![Verificar dados único PS 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Passagem dupla
Em passagem dupla, as configurações de criptografia são carimbadas no modelo de VM e não em cada disco individual.

Para verificar se as configurações de criptografia foram carimbadas em passagem dupla, você pode usar os seguintes comandos:

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
![Verificar o PowerShell 1 de passagem dupla](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Discos desanexados

Verifique as configurações de criptografia de discos que não estão anexados a uma VM.

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
## <a name="using-az-cli"></a>Usando AZ CLI

Você pode validar o status de criptografia **geral** de uma VM criptografada usando os seguintes comandos AZ CLI:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Verificar geral usando a CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Passagem única
Você pode validar as configurações de criptografia de cada disco individual usando os seguintes comandos AZ CLI:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Configurações de criptografia de dados](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Caso o disco não tenha as configurações de criptografia carimbadas, ele será mostrado como "o disco não está criptografado"

Status detalhado e configurações de criptografia:

Disco do so:

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

![CLI única de dados ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Passagem dupla

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Verificar o dual geral usando ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) a CLI você também pode verificar as configurações de criptografia no perfil de armazenamento do modelo de VM do disco do sistema operacional:

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

![Verificar o perfil de VM Dual usando a CLI ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Discos desanexados

Verifique as configurações de criptografia de discos que não estão anexados a uma VM.

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

A ID da conta de armazenamento que contém o disco.
Uma cadeia de conexão para essa conta de armazenamento específica.
O nome do contêiner que armazena o disco.
O nome do disco.

Este comando lista todas as IDs de todas as suas contas de armazenamento:

```bash
az storage account list --query [].[id] -o tsv
```
As IDs da conta de armazenamento são listadas no seguinte formato:

ID\<da assinatura do/subscriptions/\<> nome do grupo de\<recursos do/resourcegroups/> nome da conta de armazenamento do/Providers/Microsoft.Storage/storageaccounts/>

Selecione a ID apropriada e armazene-a em uma variável:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
A cadeia de conexão.

Esse comando obtém a cadeia de conexão para uma conta de armazenamento específica e a armazena em uma variável:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

O nome do contêiner.

O comando a seguir lista todos os contêineres em uma conta de armazenamento:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
O contêiner usado para discos normalmente é chamado de "VHDs"

Armazenar o nome do contêiner em uma variável 
```bash
ContainerName="name of the container"
```

O nome do disco.

Use este comando para listar todos os BLOBs em um contêiner específico
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Escolha o disco que você deseja consultar e armazene seu nome em uma variável.
```bash
DiskName="diskname.vhd"
```
Consultar as configurações de criptografia de disco
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Do sistema operacional
Validar se as partições do disco de dados estão criptografadas (e o disco do sistema operacional não está)

Quando uma partição/disco é criptografada, ela é exibida como tipo **cript** . quando não é criptografada, ela é exibida como **parte/** tipo de disco

``` bash
lsblk
```

![Camada cript. do sistema operacional ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Você pode obter mais detalhes usando a variante "lsblk" a seguir. 

Você verá uma camada de tipo **cript** . que é montada pela extensão.

O exemplo a seguir mostra volumes lógicos e discos normais com um "**\_crypto luks FSTYPE**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Camada 2 criptografada do sistema operacional](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Como uma etapa extra, você também pode validar se o disco de dados tem alguma chave carregada

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

E quais dispositivos DM são listados como cript.

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Próximas etapas

- [Guia de solução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)
