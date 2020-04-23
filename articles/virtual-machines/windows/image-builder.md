---
title: Crie uma VM do Windows com o Azure Image Builder (visualização)
description: Crie uma VM do Windows com o Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 269b2f4674f2c99fc438c1a7be65e5660ca58d08
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869504"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Visualização: Crie uma VM do Windows com o Azure Image Builder

Este artigo é para mostrar como você pode criar uma imagem personalizada do Windows usando o Azure VM Image Builder. O exemplo neste artigo usa [personalizadores](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) para personalizar a imagem:
- PowerShell (ScriptUri) - baixe e execute um [script PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Windows Restart - reinicia a VM.
- PowerShell (em linha) - execute um comando específico. Neste exemplo, ele cria um diretório sobre `mkdir c:\\buildActions`a VM usando .
- Arquivo - copie um arquivo do GitHub para a VM. Este exemplo [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) copia `c:\buildArtifacts\index.html` index.md na VM.

Você também pode `buildTimeoutInMinutes`especificar um . O padrão é de 240 minutos, e você pode aumentar um tempo de compilação para permitir compilações mais longas em execução.

Usaremos um modelo sample .json para configurar a imagem. O arquivo .json que estamos usando está aqui: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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
```

Se eles não disserem registrado, execute o seguinte:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables"></a>Definir variáveis

Usaremos algumas informações repetidamente, então criaremos algumas variáveis para armazenar essas informações.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Crie uma variável para o seu ID de assinatura. Você pode obter `az account show | grep id`isso usando .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Esse grupo de recursos é usado para armazenar o artefato do modelo de configuração de imagem e a imagem.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Defina permissões no grupo de recursos

Dê ao 'contribuinte' do Image Builder permissão para criar a imagem no grupo de recursos. Sem isso, a compilação da imagem falhará. 

O `--assignee` valor é o ID de registro do aplicativo para o serviço Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Baixe o exemplo do modelo de configuração de imagem

Um modelo de configuração de imagem parametrizado foi criado para você experimentar. Baixe o exemplo do arquivo .json e configure-o com as variáveis definidas anteriormente.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

Você pode modificar este exemplo, no terminal `vi`usando um editor de texto como .

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Para a imagem de origem, você deve `latest`sempre [especificar uma versão](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), você não pode usar .
> Se você adicionar ou alterar o grupo de recursos para onde a imagem é distribuída, você deve fazer com que as [permissões sejam definidas](#set-permissions-on-the-resource-group) no grupo de recursos.
 
## <a name="create-the-image"></a>Criar a imagem

Envie a configuração de imagem para o serviço VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Quando concluído, isso retornará uma mensagem de `Image Builder Configuration Template` sucesso `$imageResourceGroup`para o console e criará um no . Você pode ver esse recurso no grupo de recursos no portal Azure, se você habilitar 'Mostrar tipos ocultos'.

Em segundo plano, o Image Builder também criará um grupo de recursos de estágio em sua assinatura. Este grupo de recursos é usado para a compilação de imagens. Será neste formato:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Você não deve excluir diretamente o grupo de recursos de estágio. Primeiro exclua o artefato do modelo de imagem, isso fará com que o grupo de recursos de encenação seja excluído.

Se o serviço relatar uma falha durante o envio do modelo de configuração de imagem:
-  Revise essas [etapas de solução de problemas.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) 
- Você precisará excluir o modelo, usando o seguinte trecho, antes de tentar novamente a submissão.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Inicie a compilação de imagens
Inicie o processo de construção de imagens usando [aaz resource invoke-action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Espere até que a compilação esteja completa. Isso pode levar cerca de 15 minutos.

Se você encontrar algum erro, por favor, revise essas [etapas de solução de problemas.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)


## <a name="create-the-vm"></a>Criar a VM

Crie a VM usando a imagem que você construiu. Substitua * \<>de senha* por sua própria senha para o `aibuser` no VM.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Verifique a personalização

Crie uma conexão de área de trabalho remota para a VM usando o nome de usuário e a senha que você definiu quando criou a VM. Dentro da VM, abra um prompt cmd e digite:

```console
dir c:\
```

Você deve ver esses dois diretórios criados durante a personalização da imagem:
- buildActions
- construir artefatos

## <a name="clean-up"></a>Limpar

Quando terminar, exclua os recursos.

### <a name="delete-the-image-builder-template"></a>Exclua o modelo de construtor de imagens

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>Exclua o grupo de recursos de imagem

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os componentes do arquivo .json usado neste artigo, consulte [referência de modelo de construtor de imagens](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
