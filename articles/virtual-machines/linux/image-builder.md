---
title: Crie um VM Linux com o Azure Image Builder (visualização)
description: Crie uma VM Linux com o Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066678"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Pré-visualização: Crie um VM Linux com o Azure Image Builder

Este artigo mostra como você pode criar uma imagem Linux personalizada usando o Azure Image Builder e o Azure CLI. O exemplo neste artigo usa três [personalizadores diferentes](image-builder-json.md#properties-customize) para personalizar a imagem:

- Shell (ScriptUri) - baixa e executa um [script shell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inline) - executa comandos específicos. Neste exemplo, os comandos inline incluem a criação de um diretório e a atualização do sistema operacional.
- File - copia um [arquivo do GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) em um diretório na VM.

Você também pode `buildTimeoutInMinutes`especificar um . O padrão é de 240 minutos, e você pode aumentar um tempo de compilação para permitir compilações mais longas em execução.

Usaremos um modelo sample .json para configurar a imagem. O arquivo .json que estamos usando está aqui: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Definir variáveis de exemplo

Usaremos algumas informações repetidamente, então criaremos algumas variáveis para armazenar essas informações.


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Crie uma variável para o seu ID de assinatura. Você pode obter `az account show | grep id`isso usando .

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Crie o grupo de recursos.
Isso é usado para armazenar o artefato do modelo de configuração da imagem e a imagem.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Defina permissões no grupo de recursos
Dê ao 'contribuinte' do Image Builder permissão para criar a imagem no grupo de recursos. Sem as permissões adequadas, a compilação da imagem falhará. 

O `--assignee` valor é o ID de registro do aplicativo para o serviço Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Baixe o exemplo do modelo

Um modelo de configuração de imagem de amostra parametrizado foi criado para você usar. Baixe o arquivo sample .json e configure-o com as variáveis definidas anteriormente.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Você pode modificar este exemplo .json conforme necessário. Por exemplo, você pode `buildTimeoutInMinutes` aumentar o valor de permitir compilações em execução mais longas. Você pode editar o arquivo no Cloud `vi`Shell usando um editor de texto como .

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Para a imagem de origem, você deve `latest`sempre [especificar uma versão](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), você não pode usar .
>
> Se você adicionar ou alterar o grupo de recursos onde a imagem está sendo distribuída, você precisa ter certeza de que as [permissões estão definidas para o grupo de recursos](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Enviar a configuração da imagem
Envie a configuração de imagem para o serviço VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Se ele for concluído com sucesso, ele retornará uma mensagem de sucesso e criará um artefato de modelo de configuração do construtor de imagens no $imageResourceGroup. Você pode ver o grupo de recursos no portal se você habilitar 'Mostrar tipos ocultos'.

Além disso, em segundo plano, o Image Builder cria um grupo de recursos de estágio em sua assinatura. O Image Builder usa o grupo de recursos de preparação para a compilação de imagens. O nome do grupo de recursos `IT_<DestinationResourceGroup>_<TemplateName>`será neste formato: .

> [!IMPORTANT]
> Não exclua diretamente o grupo de recursos de preparação. Se você excluir o artefato do modelo de imagem, ele excluirá automaticamente o grupo de recursos de estágio. Para obter mais informações, consulte a seção [Limpar](#clean-up) no final deste artigo.

Se o serviço relatar uma falha durante o envio do modelo de configuração de imagem, consulte as [etapas de solução de problemas.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) Você também precisará excluir o modelo antes de tentar novamente enviar a compilação. Para excluir o modelo:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Inicie a compilação de imagens

Inicie a compilação da imagem.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Aguarde até que a compilação esteja concluída, por exemplo, pode levar de 10 a 15 minutos.

Se você encontrar algum erro, por favor, revise essas [etapas de solução de problemas.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)


## <a name="create-the-vm"></a>Criar a VM

Crie a VM usando a imagem que você construiu.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Obtenha o endereço IP da saída da vm e use-o para SSH para a VM.

```bash
ssh azureuser@<pubIp>
```

Você deve ver que a imagem foi personalizada com uma mensagem do dia assim que sua conexão SSH for estabelecida!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Digite `exit` quando terminar de fechar a conexão SSH.

## <a name="check-the-source"></a>Verifique a fonte

No Modelo de Construtor de Imagens, nas 'Propriedades', você verá a imagem de origem, o script de personalização que ele executa e onde ele é distribuído.

```bash
cat helloImageTemplateLinux.json
```

Para obter informações mais detalhadas sobre este arquivo .json, consulte [referência de modelo de construtor de imagens](image-builder-json.md)

## <a name="clean-up"></a>Limpar

Quando terminar, você pode excluir os recursos.

Exclua o modelo do construtor de imagens.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Exclua o grupo de recursos de imagem.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os componentes do arquivo .json usado neste artigo, consulte [referência de modelo do Construtor de imagens](image-builder-json.md).
