---
title: Use o Azure Image Builder com uma galeria de imagens para VMs do Windows (visualização)
description: Crie versões de imagem compartilhadas do Azure Gallery usando o Azure Image Builder e o Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d5856780d0d9f1a1943bca1c2f076bb3ec914e1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263346"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Visualização: Crie uma imagem do Windows e distribua-a para uma Galeria de Imagens Compartilhadas 

Este artigo é para mostrar como você pode usar o Azure Image Builder e o Azure PowerShell, para criar uma versão de imagem em uma [Galeria de Imagens Compartilhadas](shared-image-galleries.md)e, em seguida, distribuir a imagem globalmente. Você também pode fazer isso usando o [Azure CLI](../linux/image-builder-gallery.md).

Usaremos um modelo .json para configurar a imagem. O arquivo .json que estamos usando está aqui: [armTemplateWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Estaremos baixando e editando uma versão local do modelo, de modo que este artigo é escrito usando a sessão local powershell.

Para distribuir a imagem em uma Galeria de Imagens Compartilhadas, `distribute` o modelo usa a imagem [compartilhada](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) como o valor para a seção do modelo.

O Azure Image Builder executa automaticamente o sysprep para generalizar a imagem, este é um comando genérico sysprep, que você pode [substituir](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully) se necessário. 

Saiba quantas vezes você faz personalizações em camadas. Você pode executar o comando Sysprep até 8 vezes em uma única imagem do Windows. Depois de executar o Sysprep 8 vezes, você deve recriar sua imagem do Windows. Para obter mais informações, consulte [Limites sobre quantas vezes você pode executar o Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> O Azure Image Builder está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registre os recursos
Para usar o Azure Image Builder durante a visualização, você precisa registrar o novo recurso.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Verifique o status do registro do recurso.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Espere `RegistrationState` até `Registered` passar para o próximo passo.

Verifique os registros do seu provedor. Certifique-se `Registered`de que cada retorno .

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

Se eles não `Registered`retornarem, use o seguinte para registrar os provedores:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>Criar variáveis

Usaremos algumas informações repetidamente, então criaremos algumas variáveis para armazenar essas informações. Substitua os valores para `username` `vmpassword`as variáveis, como e , com suas próprias informações.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Crie um grupo de recursos e dê permissão ao Azure Image Builder para criar recursos nesse grupo de recursos.

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>Criar a Galeria de Imagens Compartilhadas

Para usar o Image Builder com uma galeria de imagens compartilhadas, você precisa ter uma galeria de imagens e definição de imagem existentes. O Image Builder não criará a galeria de imagens e a definição de imagem para você.

Se você ainda não tiver uma galeria e uma definição de imagem para usar, comece criando-as. Primeiro, crie uma galeria de imagens.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Baixe e configure o modelo

Baixe o modelo .json e configure-o com suas variáveis.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>Criar a versão de imagem

Seu modelo deve ser submetido ao serviço, isso irá baixar quaisquer artefatos dependentes, como scripts, e armazená-los no grupo de recursos de encenação, prefixado com *IT_*.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

Para construir a imagem, você precisa invocar 'Executar' no modelo.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

Criar a imagem e replicá-la em ambas as regiões pode demorar um pouco. Aguarde até que esta peça esteja terminada antes de passar para a criação de uma VM.

Para obter informações sobre opções para automatizar o status de compilação de imagens, consulte o [Readme](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query) para este modelo no GitHub.


## <a name="create-the-vm"></a>Criar a VM

Crie uma VM a partir da versão de imagem criada pelo Azure Image Builder.

Obtenha a versão de imagem que você criou.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Crie a VM na segunda região que a imagem foi replicada.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Verifique a personalização
Crie uma conexão de área de trabalho remota para a VM usando o nome de usuário e a senha que você definiu quando criou a VM. Dentro da VM, abra um prompt cmd e digite:

```console
dir c:\
```

Você deve ver um `buildActions` diretório chamado que foi criado durante a personalização da imagem.


## <a name="clean-up-resources"></a>Limpar recursos
Se você quiser agora tentar repersonalizar a versão de imagem para criar uma nova versão da mesma imagem, **pule essa etapa** e vá em frente para [usar o Azure Image Builder para criar outra versão de imagem](image-builder-gallery-update-image-version.md).


Isso excluirá a imagem que foi criada, juntamente com todos os outros arquivos de recursos. Certifique-se de que você está terminado com esta implantação antes de excluir os recursos.

Exclua primeiro o modelo de grupo de recursos, caso contrário, o grupo de recursos de preparação *(IT_)* usado pela AIB não será limpo.

Obtenha ResourceID do modelo de imagem. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

Excluir modelo de imagem.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

excluir o grupo de recursos.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Para saber como atualizar a versão de imagem criada, consulte [Use Azure Image Builder para criar outra versão de imagem](image-builder-gallery-update-image-version.md).
