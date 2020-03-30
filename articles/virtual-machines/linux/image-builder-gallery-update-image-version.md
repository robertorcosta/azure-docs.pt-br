---
title: Crie uma nova versão de imagem VM a partir de uma versão de imagem existente usando o Azure Image Builder (visualização)
description: Crie uma nova versão de imagem VM a partir de uma versão de imagem existente usando o Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 5766e91dc6a17d50c46d396dd8a68d17081e0926
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246799"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Visualização: Crie uma nova versão de imagem VM a partir de uma versão de imagem existente usando o Azure Image Builder

Este artigo mostra como pegar uma versão de imagem existente em uma [Galeria de Imagens Compartilhadas](shared-image-galleries.md), atualizá-la e publicá-la como uma nova versão de imagem para a galeria.

Usaremos um modelo sample .json para configurar a imagem. O arquivo .json que estamos usando está aqui: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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
```

Se eles não disserem registrado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Definir variáveis e permissões

Se você usou [Criar uma imagem e distribuir para uma Galeria de Imagens Compartilhadas](image-builder-gallery.md) para criar sua Galeria de Imagens Compartilhadas, você já criou algumas das variáveis que precisamos. Se não, configure algumas variáveis a serem usadas para este exemplo.

Para visualização, o construtor de imagens só suportará a criação de imagens personalizadas no mesmo Grupo de recursos que a imagem gerenciada por origem. Atualize o nome do grupo de recursos neste exemplo para ser o mesmo grupo de recursos que sua imagem gerenciada de origem.


```console
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Crie uma variável para o seu ID de assinatura. Você pode obter `az account show | grep id`isso usando .

```console
subscriptionID=<Subscription ID>
```

Obtenha a versão de imagem que você deseja atualizar.

```azurecli
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
Você pode rever o exemplo que estamos prestes a usar abrindo o arquivo .json aqui: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) juntamente com a [referência de modelo do Image Builder](image-builder-json.md). 


Baixe o exemplo .json e configure-o com suas variáveis. 

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Criar a imagem

Envie a configuração da imagem para o Serviço de Construtor de Imagens VM.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Inicie a compilação da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Aguarde até que a imagem tenha sido construída e a replicação antes de passar para o próximo passo.


## <a name="create-the-vm"></a>Criar a VM

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Crie uma conexão SSH com a VM usando o endereço IP público da VM.

```console
ssh azureuser@<pubIp>
```

Você deve ver que a imagem foi personalizada com uma "Mensagem do Dia" assim que sua conexão SSH for estabelecida.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Digite `exit` para fechar a conexão SSH.

Você também pode listar as versões de imagem que agora estão disponíveis em sua galeria.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os componentes do arquivo .json usado neste artigo, consulte [referência de modelo de construtor de imagens](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).