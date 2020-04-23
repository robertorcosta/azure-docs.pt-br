---
title: Criar uma nova versão de imagem a partir de uma versão de imagem existente usando o Azure Image Builder (visualização)
description: Crie uma nova versão de imagem VM a partir de uma versão de imagem existente usando o Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.openlocfilehash: 766e7d5c4151000a582bcf07d80b89af3b7d8a65
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869531"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Visualização: Crie uma nova versão de imagem VM a partir de uma versão de imagem existente usando o Azure Image Builder

Este artigo mostra como pegar uma versão de imagem existente em uma [Galeria de Imagens Compartilhadas](shared-image-galleries.md), atualizá-la e publicá-la como uma nova versão de imagem para a galeria.

Usaremos um modelo sample .json para configurar a imagem. O arquivo .json que estamos usando está aqui: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

> [!IMPORTANT]
> O Azure Image Builder está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registre os recursos
Para usar o Azure Image Builder durante a visualização, você precisa registrar o novo recurso.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Verifique o status do registro do recurso.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Verifique seu registro.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
```

Se eles não disserem registrado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões

Se você usou [Criar uma imagem e distribuir para uma Galeria de Imagens Compartilhadas](image-builder-gallery.md) para criar sua Galeria de Imagens Compartilhadas, você já criou as variáveis que precisamos. Se não, configure algumas variáveis a serem usadas para este exemplo.

Para visualização, o construtor de imagens só suportará a criação de imagens personalizadas no mesmo Grupo de recursos que a imagem gerenciada por origem. Atualize o nome do grupo de recursos neste exemplo para ser o mesmo grupo de recursos que sua imagem gerenciada de origem.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Crie uma variável para o seu ID de assinatura. Você pode obter `az account show | grep id`isso usando .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Obtenha a versão de imagem que você deseja atualizar.

```azurecli-interactive
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Se você já tiver sua própria Galeria de Imagens Compartilhadas e não seguir o exemplo anterior, você precisará atribuir permissões para o Image Builder acessar o Grupo de recursos, para que ele possa acessar a galeria.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Modifique o exemplo da helloImage
Você pode rever o exemplo que estamos prestes a usar abrindo o arquivo .json aqui: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) juntamente com a [referência de modelo do Image Builder](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


Baixe o exemplo .json e configure-o com suas variáveis. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Criar a imagem

Envie a configuração da imagem para o Serviço de Construtor de Imagens VM.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Inicie a compilação da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Aguarde até que a imagem tenha sido construída e a replicação antes de passar para o próximo passo.


## <a name="create-the-vm"></a>Criar a VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Verifique a personalização
Crie uma conexão de área de trabalho remota para a VM usando o nome de usuário e a senha que você definiu quando criou a VM. Dentro da VM, abra um prompt cmd e digite:

```console
dir c:\
```

Agora você deve ver dois diretórios:
- `buildActions`que foi criado na primeira versão de imagem.
- `buildActions2`que foi criada como parte até atualizando a primeira versão de imagem para criar a segunda versão de imagem.


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os componentes do arquivo .json usado neste artigo, consulte [referência de modelo de construtor de imagens](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).