---
title: Use o Azure Image Builder com uma galeria de imagens para VMs Linux (visualização)
description: Crie imagens de VM Linux com o Azure Image Builder e a Galeria de Imagens Compartilhadas.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: bf1dca61ec6b39e52d4f76c1c77cd3def6973ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945006"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Visualização: Crie uma imagem Linux e distribua-a para uma Galeria de Imagens Compartilhadas 

Este artigo mostra como você pode usar o Azure Image Builder e o Azure CLI, para criar uma versão de imagem em uma [Galeria de Imagens Compartilhadas](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)e, em seguida, distribuir a imagem globalmente. Você também pode fazer isso usando [o Azure PowerShell](../windows/image-builder-gallery.md).


Usaremos um modelo sample .json para configurar a imagem. O arquivo .json que estamos usando está aqui: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Para distribuir a imagem em uma Galeria de Imagens Compartilhadas, `distribute` o modelo usa a imagem [compartilhada](image-builder-json.md#distribute-sharedimage) como o valor para a seção do modelo.

> [!IMPORTANT]
> O Azure Image Builder está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Usaremos algumas informações repetidamente, então criaremos algumas variáveis para armazenar essas informações.

Para visualização, o construtor de imagens só suportará a criação de imagens personalizadas no mesmo Grupo de recursos que a imagem gerenciada por origem. Atualize o nome do grupo de recursos neste exemplo para ser o mesmo grupo de recursos que sua imagem gerenciada de origem.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Crie uma variável para o seu ID de assinatura. Você pode obter `az account show | grep id`isso usando .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Crie o grupo de recursos.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Dê permissão ao Azure Image Builder para criar recursos nesse grupo de recursos. O `--assignee` valor é o ID de registro do aplicativo para o serviço Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Crie uma definição de imagem e galeria

Para usar o Image Builder com uma galeria de imagens compartilhadas, você precisa ter uma galeria de imagens e definição de imagem existentes. O Image Builder não criará a galeria de imagens e a definição de imagem para você.

Se você ainda não tiver uma galeria e uma definição de imagem para usar, comece criando-as. Primeiro, crie uma galeria de imagens.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Em seguida, crie uma definição de imagem.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Baixe e configure o .json

Baixe o modelo .json e configure-o com suas variáveis.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Criar a versão de imagem

A próxima parte criará a versão de imagem na galeria. 

Envie a configuração de imagem para o serviço Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Inicie a compilação da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Criar a imagem e replicá-la em ambas as regiões pode demorar um pouco. Aguarde até que esta peça esteja terminada antes de passar para a criação de uma VM.


## <a name="create-the-vm"></a>Criar a VM

Crie uma VM a partir da versão de imagem criada pelo Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Faça SSH na VM.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Você deve ver que a imagem foi personalizada com uma *mensagem do dia* assim que sua conexão SSH for estabelecida!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser agora tentar repersonalizar a versão de imagem para criar uma nova versão da mesma imagem, pule os próximos passos e vá em frente para [usar o Azure Image Builder para criar outra versão de imagem](image-builder-gallery-update-image-version.md).


Isso excluirá a imagem que foi criada, juntamente com todos os outros arquivos de recursos. Certifique-se de que você está terminado com esta implantação antes de excluir os recursos.

Ao excluir os recursos da galeria de imagens, você precisa excluir todas as versões da imagem antes de poder excluir a definição de imagem usada para criá-las. Para excluir uma galeria, primeiro você precisa ter excluído todas as definições de imagem na galeria.

Exclua o modelo do construtor de imagens.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Obtenha a versão de imagem criada pelo `0.`construtor de imagens, isso sempre começa com , e, em seguida, excluir a versão da imagem

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Exclua a definição de imagem.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Apague a galeria.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Exclua o grupo de recursos.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [as galerias de imagens compartilhadas do Azure.](shared-image-galleries.md)