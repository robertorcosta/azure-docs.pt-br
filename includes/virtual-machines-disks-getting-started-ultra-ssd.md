---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dfb094bc9f84e7129a3e1c733a054c5f6cd96372
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008613"
---
Os discos ultra azure oferecem alta capacidade de transmissão, IOPS elevado e armazenamento consistente de disco de baixa latência para máquinas virtuais Azure IaaS (VMs). Essa nova oferta fornece o melhor em desempenho de linha, nos mesmos níveis de disponibilidade que nossas ofertas de discos atuais. Um grande benefício dos discos ultra é a capacidade de alterar dinamicamente o desempenho do SSD junto com suas cargas de trabalho sem a necessidade de reiniciar suas VMs. Os discos Ultra são adequados para cargas de trabalho de grande volume de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho de transações pesadas.

## <a name="ga-scope-and-limitations"></a>Escopo e limitações do GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Determine o tamanho da VM e a disponibilidade da região

### <a name="vms-using-availability-zones"></a>VMs usando zonas de disponibilidade

Para aproveitar os discos ultra, você precisa determinar em qual zona de disponibilidade você está. Nem todas as regiões suportam todos os tamanhos de VM com ultra discos. Para determinar se o tamanho da região, região e VM suporta manuais ultra, execute qualquer um dos seguintes comandos, certifique-se de substituir os valores de **região,** **vmSize**e **assinatura** primeiro:

#### <a name="cli"></a>CLI

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

A resposta será semelhante ao formulário abaixo, onde X é a zona a ser usada para implantação na região escolhida. X pode ser 1, 2 ou 3.

Preserve o valor **de Regiões,** ele representa sua zona de disponibilidade e você precisará dela para implantar um disco Ultra.

|ResourceType  |Nome  |Location  |Zonas  |Restrição  |Recurso  |Valor  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Se não houve resposta do comando, então o tamanho de VM selecionado não será suportado com discos ultra na região selecionada.

Agora que você sabe para qual zona implantar, siga as etapas de implantação neste artigo para implantar uma VM com um disco ultra conectado ou anexar um disco ultra a uma VM existente.

### <a name="vms-with-no-redundancy-options"></a>VMs sem opções de redundância

Os discos ultra implantados no Oeste dos EUA devem ser implantados sem opções de redundância, por enquanto. No entanto, nem todo tamanho de disco que suporta discos ultra podem estar nesta região. Para determinar quais nos EUA ocidentais suportam discos ultra, você pode usar qualquer um dos seguintes trechos de código. Certifique-se de `vmSize` `subscription` substituir os valores e valores primeiro:

```azurecli
$subscription = "<yourSubID>"
$region = "westus"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

A resposta será semelhante à `UltraSSDAvailable   True` forma a seguir, indica se o tamanho da VM suporta discos ultra nesta região.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Implantar um disco ultra usando o Azure Resource Manager

Primeiro, determine o tamanho da VM para implantar. Para obter uma lista de tamanhos de VM suportados, consulte a seção [de escopo e limitações do GA.](#ga-scope-and-limitations)

Se você quiser criar uma VM com vários discos ultra, consulte a amostra [Crie uma VM com vários discos ultra](https://aka.ms/ultradiskArmTemplate).

Se você pretende usar seu próprio modelo, **certifique-se de que o apiVersion** `Microsoft.Compute/virtualMachines` para e `Microsoft.Compute/Disks` é definido como `2018-06-01` (ou posterior).

Defina o sku de disco para **UltraSSD_LRS,** em seguida, defina a capacidade do disco, IOPS, zona de disponibilidade e throughput em MBps para criar um disco ultra.

Depois que a VM for provisionada, será possível particionar e formatar os discos de dados e configurá-los para suas cargas de trabalho.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Implantar um disco ultra usando o portal Azure

Esta seção abrange a implantação de uma máquina virtual equipada com um disco ultra como um disco de dados. Ele assume que você tem familiaridade com a implantação de uma máquina virtual, se você não fizer isso, veja o nosso [Quickstart: Crie uma máquina virtual do Windows no portal Azure](../articles/virtual-machines/windows/quick-create-portal.md).

- Faça login no [portal Azure](https://portal.azure.com/) e navegue para implantar uma máquina virtual (VM).
- Certifique-se de escolher um [tamanho e região de VM suportados.](#ga-scope-and-limitations)
- Selecione **A zona disponibilidade** nas **opções disponibilidade**.
- Preencha as entradas restantes com seleções de sua escolha.
- Escolha **Discos**.

![criar-ultra-disco ativado-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- Na lâmina Discos, selecione **Sim** para **ativar a compatibilidade com o Disco Ultra**.
- Selecione **Criar e anexar um novo disco** para anexar um disco ultra agora.

![ativar-e-anexar-ultra-disco.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Na **Criação de uma nova** lâmina de disco, digite um nome e selecione Tamanho de **alteração**.
- Alterar o **tipo conta** para **ultra disco**.
- Alterar os valores do tamanho do **disco Personalizado (GiB),** **do Disk IOPS**e **do throughput** do disco para os de sua escolha.
- Selecione **OK** em ambas as lâminas.
- Continue com a implantação da VM, será o mesmo que você implantaria qualquer outra VM.

![create-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Anexar um disco ultra usando o portal Azure

Alternativamente, se a VM existente estiver em uma região/zona de disponibilidade capaz de usar discos ultra, você pode fazer uso de ultra discos sem ter que criar uma nova VM. Ativando discos ultra em sua VM existente, anexando-os como discos de dados.

- Navegue até sua VM e selecione **Discos**.
- Selecione **Editar**.

![opções-seletor-ultra-discos.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Selecione **Sim** para **ativar a compatibilidade com o disco ultra**.

![ultra-opções-sim-habilitar.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Clique em **Salvar**.
- Selecione **Adicionar disco de dados** em seguida, na série **'Nome'** Select **Criar disco**.

![criar e anexar-novo-ultra-disco.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Preencha um nome para o seu novo disco e selecione **Tamanho de alteração**.
- Alterar o **tipo conta** para **ultra disco**.
- Alterar os valores do tamanho do **disco Personalizado (GiB),** **do Disk IOPS**e **do throughput** do disco para os de sua escolha.
- Selecione **OK** e selecione **Criar**.

![making-a-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- Depois de retornar à lâmina do disco, **selecione Salvar**.

![salvando-e-anexando-novo-ultra-disco.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Ajuste o desempenho de um disco ultra usando o portal Azure

Os discos ultra oferecem um recurso único que permite ajustar seu desempenho. Você pode fazer esses ajustes a partir do portal Azure, nos próprios discos.

- Navegue até sua VM e selecione **Discos**.
- Selecione o disco ultra que deseja modificar o desempenho.

![selecionando-ultra-disco-para-modificar.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Selecione **Configuração** e, em seguida, faça suas modificações.
- Clique em **Salvar**.

![configuração-ultra-disco-desempenho e tamanho.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Implantar um ultra disco usando CLI

Primeiro, determine o tamanho da VM para implantar. Consulte a seção [de escopo e limitações](#ga-scope-and-limitations) de GA para obter uma lista de tamanhos de VM suportados.

Você deve criar uma VM capaz de usar discos ultra, a fim de anexar um disco ultra.

Substitua ou defina as variáveis **$vmname,** **$rgname**, **$diskname** **,** **$location**$password , **$user** com seus próprios valores. Defina **$zone** ao valor da sua zona de disponibilidade que você obteve desde o [início deste artigo](#determine-vm-size-and-region-availability). Em seguida, execute o seguinte comando CLI para criar uma VM ultra ativada:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Habilite a compatibilidade de disco ultra em uma VM existente

Se a VM atender aos requisitos descritos no [escopo e limitações do GA](#ga-scope-and-limitations) e estiver na zona apropriada para sua [conta,](#determine-vm-size-and-region-availability)então você poderá ativar a compatibilidade de disco ultra em sua VM.

Para ativar a compatibilidade de disco ultra, você deve parar a VM. Depois de parar a VM, você pode ativar a compatibilidade, anexar um disco ultra e reiniciar a VM:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>Crie um ultra disco usando cli

Agora que você tem uma VM que é capaz de anexar discos ultra, você pode criar e anexar um disco ultra a ele.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Anexar um disco ultra a uma VM usando CLI

Alternativamente, se a VM existente estiver em uma região/zona de disponibilidade capaz de usar discos ultra, você pode fazer uso de ultra discos sem ter que criar uma nova VM.

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Ajuste o desempenho de um disco ultra usando CLI

Os discos ultra oferecem um recurso único que permite ajustar seu desempenho, o seguinte comando mostra como usar esse recurso:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Implantar um disco ultra usando o PowerShell

Primeiro, determine o tamanho da VM para implantar. Consulte a seção [de escopo e limitações](#ga-scope-and-limitations) de GA para obter uma lista de tamanhos de VM suportados.

Para usar discos ultra, você deve criar uma VM capaz de usar ultra discos. Substitua ou defina as **variáveis $resourcegroup** e **$vmName** com seus próprios valores. Defina **$zone** ao valor da sua zona de disponibilidade que você obteve desde o [início deste artigo](#determine-vm-size-and-region-availability). Em seguida, execute o seguinte comando [New-AzVm](/powershell/module/az.compute/new-azvm) para criar uma VM ultra ativada:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD $true `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Habilite a compatibilidade de disco ultra em uma VM existente

Se a VM atender aos requisitos descritos no [escopo e limitações do GA](#ga-scope-and-limitations) e estiver na zona apropriada para sua [conta,](#determine-vm-size-and-region-availability)então você poderá ativar a compatibilidade de disco ultra em sua VM.

Para ativar a compatibilidade de disco ultra, você deve parar a VM. Depois de parar a VM, você pode ativar a compatibilidade, anexar um disco ultra e reiniciar a VM:

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>Crie um disco ultra usando o PowerShell

Agora que você tem uma VM capaz de usar discos ultra, você pode criar e anexar um disco ultra a ele:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Conecte um disco ultra a uma VM usando powershell

Alternativamente, se a VM existente estiver em uma região/zona de disponibilidade capaz de usar discos ultra, você pode fazer uso de ultra discos sem ter que criar uma nova VM.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Ajuste o desempenho de um disco ultra usando o PowerShell

Os discos ultra têm um recurso único que permite ajustar seu desempenho, o comando a seguir é um exemplo que ajusta o desempenho sem ter que desprender o disco:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```