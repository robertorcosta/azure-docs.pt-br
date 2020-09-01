---
title: Implantar VMs em seu dispositivo de GPU do Azure Stack Edge via Azure PowerShell
description: Descreve como criar e gerenciar máquinas virtuais (VMs) em um dispositivo de GPU Azure Stack Edge usando Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: d5210a3788f7bb054492c2d83c595c26fa3c4f42
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265704"
---
# <a name="deploy-vms-on-your-azure-stack-edge-gpu-device-via-azure-powershell"></a>Implantar VMs em seu dispositivo de GPU do Azure Stack Edge via Azure PowerShell

<!--[!INCLUDE [azure-stack-edge-gateway-deploy-vm-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]-->

Este tutorial descreve como criar e gerenciar uma VM em seu dispositivo Azure Stack Edge usando Azure PowerShell.

## <a name="vm-deployment-workflow"></a>Fluxo de trabalho de implantação de VM

O fluxo de trabalho de implantação é ilustrado no diagrama a seguir.

![Fluxo de trabalho de implantação de VM](media/azure-stack-edge-j-series-deploy-virtual-machine-powershell/vm-workflow_r.svg)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]



## <a name="query-for-built-in-subscription-on-the-device"></a>Consultar a assinatura interna no dispositivo

Por Azure Resource Manager, há suporte apenas para uma única assinatura fixa visível para o usuário. Esta assinatura é exclusiva por dispositivo e este nome de assinatura ou ID de assinatura não pode ser alterado.

Essa assinatura contém todos os recursos que são criados necessários para a criação da VM. 

> [!IMPORTANT]
> Essa assinatura não está conectada ou está relacionada à sua assinatura do Azure e reside localmente no seu dispositivo.

Esta assinatura será usada para implantar as VMs.

1.  Para listar essa assinatura, digite:

    ```powershell
    Get-AzureRmSubscription
    ```
    
    Um exemplo de saída é mostrado abaixo.

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
3.  Obtenha a lista de provedores de recursos registrados em execução no dispositivo. Essa lista normalmente inclui computação, rede e armazenamento.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > Os provedores de recursos são previamente registrados e não podem ser modificados ou alterados.
    
    Um exemplo de saída é mostrado abaixo:

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure, como a conta de armazenamento, disco, disco gerenciado são implantados e gerenciados.

> [!IMPORTANT]
> Todos os recursos são criados no mesmo local que o do dispositivo e o local é definido como **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Um exemplo de saída é mostrado abaixo.

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma nova conta de armazenamento usando o grupo de recursos criado na etapa anterior. Esta é uma **conta de armazenamento local** que será usada para carregar a imagem de disco virtual para a VM.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Somente as contas de armazenamento local, como o armazenamento com redundância local (Standard_LRS ou Premium_LRS), podem ser criadas via Azure Resource Manager. Para criar contas de armazenamento em camadas, consulte as etapas em [Adicionar, conectar-se a contas de armazenamento em seu Azure Stack Edge](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Um exemplo de saída é mostrado abaixo.

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

Para obter a chave da conta de armazenamento, execute o `Get-AzureRmStorageAccountKey` comando. Uma saída de exemplo desse comando é mostrada aqui.

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-blob-uri-to-hosts-file"></a>Adicionar URI de blob ao arquivo de hosts

Você já adicionou o URI de blob no arquivo de hosts para o cliente que você está usando para se conectar ao armazenamento de blob na seção [modificar arquivo de host para resolução de nome de ponto de extremidade](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution). Essa foi a entrada para o URI do blob:

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\> .\<dnsdomain\>


## <a name="install-certificates"></a>Instalar certificados

Se você estiver usando *https*, precisará instalar os certificados apropriados em seu dispositivo. Nesse caso, instale o certificado de ponto de extremidade de BLOB. Para obter mais informações, consulte como criar e carregar certificados em [gerenciar certificados](azure-stack-edge-j-series-manage-certificates.md).

## <a name="upload-a-vhd"></a>Carregar um VHD

Copie as imagens de disco a serem usadas em blobs de páginas na conta de armazenamento local que você criou nas etapas anteriores. Você pode usar uma ferramenta como [AzCopy](../storage/common/storage-use-azcopy-v10.md) para carregar o VHD na conta de armazenamento que você criou nas etapas anteriores. 

Antes de usar o AzCopy, verifique se o [AzCopy está configurado corretamente](#configure-azcopy) para uso com a versão da API REST do armazenamento de BLOBs que você está usando com seu dispositivo de borda de Azure Stack.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Defina `BlobType` como página para criar um disco gerenciado fora do VHD. Defina `BlobType` para bloquear ao gravar em contas de armazenamento em camadas usando AzCopy.

Você pode baixar as imagens de disco do Marketplace. Para obter etapas detalhadas, acesse [obter a imagem do disco virtual do Azure Marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

Uma saída de exemplo usando AzCopy 7,3 é mostrada abaixo. Para obter mais informações sobre esse comando, acesse [carregar arquivo VHD para conta de armazenamento usando AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```


## <a name="create-managed-disks-from-the-vhd"></a>Criar discos gerenciados a partir do VHD

Crie um disco gerenciado com base no VHD carregado.

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
Um exemplo de saída é mostrado abaixo: 

$DiskConfig = New-AzureRmDiskConfig-local DBELocal-createoption Import – SourceUri http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

Um exemplo de saída é mostrado abaixo. Para obter mais informações sobre esse cmdlet, vá para [New-AzureRmDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0).

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>Criar uma imagem de VM a partir do disco gerenciado de imagem

Use o comando a seguir para criar uma imagem de VM do disco gerenciado. Substitua os valores em \< \> pelos nomes que você escolher.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

Um exemplo de saída é mostrado abaixo. Para obter mais informações sobre esse cmdlet, vá para [New-AzureRmImage](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0).

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-vm-with-previously-created-resources"></a>Criar VM com recursos criados anteriormente

Você deve criar uma rede virtual e associar uma interface de rede virtual antes de criar e implantar a VM.

> [!IMPORTANT]
> Ao criar a rede virtual e a interface de rede virtual, as seguintes regras se aplicam:
> - Somente uma vnet pode ser criada (mesmo entre grupos de recursos) e deve corresponder exatamente à rede lógica em termos de espaço de endereço.
> -   Somente uma sub-rede será permitida na vnet. A sub-rede deve ser exatamente o mesmo espaço de endereço que a vnet.
> -   Somente o método de alocação estática será permitido durante a criação do vNIC e o usuário precisará fornecer um endereço IP privado.

 
**Criar uma vnet**

```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```

**Criar um vNIC usando a ID de sub-rede da vnet**

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

A saída de exemplo desses comandos é mostrada abaixo:

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

Opcionalmente, ao criar um vNIC para uma VM, você pode passar o IP público. Nessa instância, o IP público retornará o IP privado. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```


**Criar uma máquina virtual**

Agora você pode usar a imagem da VM para criar uma VM e anexá-la à rede virtual que você criou anteriormente.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)

You will use this username, password to login to the VM, once it is created and powered up.

$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-a-vm"></a>Como conectar-se a uma VM

Conecte-se à VM usando o IP privado que você passou durante a criação da VM.

Abra uma sessão SSH para se conectar com o endereço IP.

`ssh -l <username> <ip address>`

Quando solicitado, forneça a senha que você usou ao criar a VM.

Se você precisar fornecer a chave SSH, use este comando.

SSH-i c:/Users/Administrator/. ssh/id_rsa Administrator@5.5.41.236

Se você usou um endereço IP público durante a criação da VM, poderá usar esse IP para se conectar à VM. Para obter o IP público: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
O IP público, nesse caso, será o mesmo que o IP privado passado durante a criação da interface de rede virtual.


## <a name="manage-vm"></a>Gerenciar VM

A seção a seguir descreve algumas das operações comuns em toda a VM que você criará em seu dispositivo de Azure Stack Edge.

### <a name="list-vms-running-on-the-device"></a>Listar VMs em execução no dispositivo

Para retornar uma lista de todas as VMs em execução no dispositivo Azure Stack Edge, execute o comando a seguir.


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>Ativar a VM

Execute o seguinte cmdlet para ativar uma máquina virtual em execução no seu dispositivo:


`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`


Para obter mais informações sobre esse cmdlet, acesse [Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0).

### <a name="suspend-or-shut-down-the-vm"></a>Suspender ou desligar a VM

Execute o seguinte cmdlet para parar ou desligar uma máquina virtual em execução no seu dispositivo:


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```


Para obter mais informações sobre esse cmdlet, vá para o [cmdlet Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0).

### <a name="add-a-data-disk"></a>Adicionar um disco de dados

Se os requisitos de carga de trabalho em sua VM aumentarem, talvez seja necessário adicionar um disco de dados.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>Excluir a VM

Execute o seguinte cmdlet para remover uma máquina virtual do seu dispositivo:

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

Para obter mais informações sobre esse cmdlet, vá para o [cmdlet Remove-AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0).


## <a name="supported-vm-sizes"></a>Tamanhos de VM com suporte

O tamanho da VM determina a quantidade de recursos de computação, como memória, CPU e GPU que estão disponíveis para a VM. As máquinas virtuais devem ser criadas usando um tamanho de VM adequado para a carga de trabalho. Embora todos os computadores sejam executados no mesmo hardware, os tamanhos de máquina têm limites diferentes para o acesso ao disco, o que pode ajudá-lo a gerenciar o acesso geral ao disco em suas VMs. Se uma carga de trabalho aumentar, uma máquina virtual existente também poderá ser redimensionada.

As seguintes VMs da série Dv2 padrão têm suporte para a criação no dispositivo Azure Stack Edge.

### <a name="dv2-series"></a>Série Dv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Taxa de transferência máxima do disco do so (IOPS) | Taxa de transferência máxima de armazenamento temporário (IOPS) | Máximo de discos de dados/taxa de transferência (IOPS) | Máximo de NICs |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3,5 |50   |500 |3000  |4 / 4 x 500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16 x 500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24.000 |32 / 32 x 500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64x500 |8 |

### <a name="dsv2-series"></a>Série DSv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Taxa de transferência máxima do disco do so (IOPS) | Taxa de transferência máxima de armazenamento temporário (IOPS) | Máximo de discos de dados/taxa de transferência (IOPS) | Máximo de NICs |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3,5 |7   |1000 |4000  |4/4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8/8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16/16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32/32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64/64x2300 |8 |

### <a name="dv2-series"></a>Série Dv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Taxa de transferência máxima do disco do so (IOPS) | Taxa de transferência máxima de armazenamento temporário (IOPS) | Máximo de discos de dados/taxa de transferência (IOPS) | Máximo de NICs |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8 x 500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16 x 500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24.000  |32 / 32 x 500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64x500  |8 |


### <a name="dsv2-series"></a>Série DSv2
|Tamanho     |vCPU     |Memória (GiB) | Armazenamento temporário (GiB)  | Taxa de transferência máxima do disco do so (IOPS) | Taxa de transferência máxima de armazenamento temporário (IOPS) | Máximo de discos de dados/taxa de transferência (IOPS) | Máximo de NICs |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4/4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8/8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16/16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32/32x2300  |8 |

Para obter mais informações, acesse a [série Dv2 em uso geral tamanhos de VM](../virtual-machines/dv2-dsv2-series.md#dv2-series).

## <a name="unsupported-vm-operations-and-cmdlets"></a>Cmdlets e operações de VM sem suporte

Não há suporte para extensão, conjuntos de dimensionamento, conjuntos de disponibilidade, instantâneos.

## <a name="configure-azcopy"></a>Configurar o AzCopy

Ao instalar a versão mais recente do AzCopy, você precisará configurar o AzCopy para garantir que ele corresponda à versão da API REST do armazenamento de BLOBs do seu dispositivo Azure Stack Edge.

No cliente usado para acessar seu dispositivo Azure Stack Edge, configure uma variável global para corresponder à versão da API REST do armazenamento de BLOBs.

### <a name="on-windows-client"></a>No cliente Windows 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### <a name="on-linux-client"></a>No cliente Linux

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

Para verificar se a variável de ambiente para AzCopy foi definida corretamente, execute as seguintes etapas:

1. Executar "azcopy env"
2. `AZCOPY_DEFAULT_SERVICE_API_VERSION`Parâmetro Find. Isso deve ter o valor que você definiu nas etapas anteriores.


## <a name="next-steps"></a>Próximas etapas

[Azure Resource Manager cmdlets](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
