---
title: Crie uma imagem de Máquina Virtual e use uma identidade gerenciada atribuída pelo usuário para acessar arquivos no Azure Storage (visualização)
description: Crie uma imagem de máquina virtual usando o Azure Image Builder, que pode acessar arquivos armazenados no Azure Storage usando a identidade gerenciada atribuída pelo usuário.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 27f4073efc8647d331faa14afbda0e15f92b8d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060740"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Crie uma imagem e use uma identidade gerenciada atribuída pelo usuário para acessar arquivos no Azure Storage 

O Azure Image Builder suporta o uso de scripts ou a cópia de arquivos de vários locais, como o armazenamento GitHub e a zure etc. Para usá-los, eles devem ter sido acessíveis externamente ao Azure Image Builder, mas você poderia proteger blobs de armazenamento Azure usando Tokens SAS.

Este artigo mostra como criar uma imagem personalizada usando o Azure VM Image Builder, onde o serviço usará uma [Identidade Gerenciada atribuída pelo usuário](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para acessar arquivos no armazenamento Do Zure para a personalização da imagem, sem que você tenha que tornar os arquivos acessíveis publicamente ou configurar tokens SAS.

No exemplo abaixo, você criará dois grupos de recursos, um será usado para a imagem personalizada e o outro hospedará uma conta de armazenamento do Azure, que contém um arquivo de script. Isso simula um cenário da vida real, onde você pode ter artefatos de construção, ou arquivos de imagem em diferentes contas de armazenamento, fora do Image Builder. Você criará uma identidade atribuída pelo usuário e, em seguida, concederá permissões de leitura no arquivo de script, mas não definirá nenhum acesso público a esse arquivo. Em seguida, você usará o personalizador Shell para baixar e executar esse script a partir da conta de armazenamento.


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


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Usaremos algumas informações repetidamente, então criaremos algumas variáveis para armazenar essas informações.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Crie uma variável para o seu ID de assinatura. Você pode obter `az account show | grep id`isso usando .

```console
subscriptionID=<Your subscription ID>
```

Crie os grupos de recursos para a imagem e o armazenamento de script.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Crie o armazenamento e copie o script de exemplo no GitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Dê permissão ao Image Builder para criar recursos no grupo de recursos de imagem. O `--assignee` valor é o ID de registro do aplicativo para o serviço Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Criar identidade gerenciada atribuída pelo usuário

Crie a identidade e atribua permissões para a conta de armazenamento de script. Para obter mais informações, consulte [Identidade gerenciada atribuída pelo usuário](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Modifique o exemplo

Baixe o arquivo .json de exemplo e configure-o com as variáveis criadas.

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Criar a imagem

Envie a configuração de imagem para o serviço Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Inicie a compilação da imagem.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Aguarde a conclusão do build. Isso pode levar cerca de 15 minutos.

## <a name="create-a-vm"></a>Criar uma máquina virtual

Crie uma VM a partir da imagem. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Depois que a VM tiver sido criada, inicie uma sessão de SSH com a VM.

```console
ssh aibuser@<publicIp>
```

Você deve ver que a imagem foi personalizada com uma mensagem do dia assim que sua conexão SSH for estabelecida!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Limpar

Quando você terminar, você pode excluir os recursos se eles não forem mais necessários.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Se você tiver algum problema em trabalhar com o Azure Image Builder, consulte [Solução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).