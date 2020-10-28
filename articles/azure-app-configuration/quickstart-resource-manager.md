---
title: Criar um armazenamento de Configuração de Aplicativos do Azure usando o modelo do ARM (modelo do Azure Resource Manager)
titleSuffix: Azure App Configuration
description: Saiba como criar um armazenamento da Configuração de Aplicativos do Azure usando o modelo do ARM (modelo do Azure Resource Manager).
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 10/16/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: feabac62564729338e41bf30eaf8d9f5a6317126
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149004"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Início Rápido: Criar um armazenamento da Configuração de Aplicativos do Azure usando um modelo do ARM

Este guia de início rápido descreve como:

- Implantar um repositório de Configuração de Aplicativos usando um modelo do ARM (modelo do Azure Resource Manager).
- Criar pares chave/valor em um repositório de Configuração de Aplicativos usando um modelo do ARM.
- Ler pares chave/valor em um repositório de Configuração de Aplicativos por meio do modelo do ARM.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure** . O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/). Ele cria um repositório de Configuração de Aplicativos com dois pares chave/valor dentro. Em seguida, ele usa a função `reference` para gerar os valores dos dois recursos de par chave/valor. Dessa maneira, a leitura do valor da chave permite que ele seja usado em outros locais do modelo.

O guia de início rápido usa o elemento `copy` para criar várias instâncias do recurso de par chave/valor. Para saber mais sobre o elemento `copy`, confira [Iteração de recursos em modelos do ARM](../azure-resource-manager/templates/copy-resources.md).

> [!IMPORTANT]
> Esse modelo requer a versão do provedor de recursos de Configuração de Aplicativos versão `2020-07-01-preview` ou posterior. Essa versão usa a função `reference` para ler pares chave/valor. A função `listKeyValue` que foi usada para ler valores de chave na versão anterior não está disponível da versão `2020-07-01-preview` em diante.

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json":::

Há dois recursos do Azure definidos no modelo:

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-06-01/configurationstores): cria um repositório de Configuração de Aplicativos.
- Microsoft.AppConfiguration/configurationStores/keyValues: crie um par chave/valor dentro do repositório de Configuração de Aplicativos.

> [!NOTE]
> O nome do recurso `keyValues` é uma combinação de chave e rótulo. A chave e o rótulo são unidos pelo delimitador `$`. O rótulo é opcional. No exemplo acima, o recurso `keyValues` com o nome `myKey` cria um par chave/valor sem um rótulo.
>
> A codificação por percentual, também conhecida como codificação de URL, permite que chaves ou rótulos incluam caracteres que não são permitidos em nomes de recursos do modelo do ARM. `%` não é um caractere permitido, portanto, `~` é usado no lugar dele. Para codificar corretamente um nome, siga estas etapas:
>
> 1. Aplicar codificação de URL
> 2. Substitua `~` por `~7E`
> 3. Substitua `%` por `~`
>
> Por exemplo, para criar um par chave-valor com o nome da chave `AppName:DbEndpoint` e o nome do rótulo `Test`, o nome do recurso deve ser `AppName~3ADbEndpoint$Test`.

## <a name="deploy-the-template"></a>Implantar o modelo

Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria um repositório de Configuração de Aplicativos com dois pares chave/valor dentro.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

Você também pode implantar o modelo usando o cmdlet do PowerShell a seguir. Os pares chave/valor estarão na saída do console do PowerShell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

1. Entre no [portal do Azure](https://portal.azure.com).
1. Na caixa de pesquisa do portal do Azure, digite **Configuração de Aplicativos** . Selecione **Configuração de Aplicativos** na lista.
1. Selecione o recurso de Configuração de Aplicativos recém-criado.
1. Em **Operações** , clique em **Gerenciador de configurações** .
1. Verifique se há dois pares chave/valor.

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua o grupo de recursos, o repositório da Configuração de Aplicativos e todos os recursos relacionados quando eles não forem mais necessários. Se estiver planejando usar o repositório da Configuração de Aplicativos no futuro, você pode ignorar a exclusão deles. Se você não pretende continuar usando esse repositório, exclua todos os recursos criados por este início rápido executando o seguinte cmdlet:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como criar outros aplicativos com a Configuração de Aplicativos do Azure, prossiga para o seguinte artigo:

> [!div class="nextstepaction"]
> [Início Rápido: Criar um aplicativo ASP.NET Core com a Configuração de Aplicativos do Azure](quickstart-aspnet-core-app.md)
