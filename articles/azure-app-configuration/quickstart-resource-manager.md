---
title: Início rápido da implantação automatizada de VM com a Configuração de Aplicativos do Azure
description: Este início rápido demonstra como usar o módulo do Azure PowerShell e os modelos do Azure Resource Manager para implantar um repositório da Configuração de Aplicativos do Azure. Em seguida, use os valores no repositório para implantar uma VM.
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 7b7dd00d3495c24733ecdc213e0e25f8bc9640eb
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661462"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>Início Rápido: Implantação automatizada da VM com a Configuração de Aplicativos e o modelo ARM (modelo do Resource Manager)

Saiba como usar os modelos do Azure Resource Manager e o Azure PowerShell para implantar um repositório de Configuração de Aplicativos do Azure, como adicionar pares chave-valor no repositório e como usar os pares chave-valor no repositório para implantar um recurso do Azure, como uma máquina virtual do Azure neste exemplo.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-templates"></a>Examinar os modelos

Os modelos usados neste início rápido são provenientes dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). O [primeiro modelo](https://azure.microsoft.com/resources/templates/101-app-configuration-store/) cria um repositório de Configuração de Aplicativos:

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

Um recurso do Azure é definido no modelo:

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores): cria um repositório de Configuração de Aplicativos.

O [segundo modelo](https://azure.microsoft.com/resources/templates/101-app-configuration/) cria uma máquina virtual usando os pares chave-valor no repositório. Antes dessa etapa, você precisa adicionar os pares chave-valor usando o portal ou a CLI do Azure.

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>Implantar os modelos

### <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria um repositório de Configuração de Aplicativos.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. Selecione ou insira os seguintes valores.

    - **assinatura**: selecione a assinatura do Azure usada para criar o repositório de Configuração de Aplicativos.
    - **Grupo de recursos**: selecione **Criar** para criar um grupo de recursos, a menos que deseje usar um grupo de recursos existente.
    - **Região**: selecione uma localização para o grupo de recursos.  Por exemplo, **Leste dos EUA**.
    - **Nome do Repositório de Configuração**: insira um novo nome do repositório de Configuração de Aplicativos.
    - **Localização**: especifique a localização do repositório de Configuração de Aplicativos.  Use o valor padrão.
    - **Nome do SKU**: especifique o nome do SKU do repositório de Configuração de Aplicativos. Use o valor padrão.

1. Selecione **Examinar + criar**.
1. Verifique se a página mostra **Validação Aprovada** e, em seguida, selecione **Criar**.

Anote o nome do grupo de recursos e o nome do repositório de Configuração de Aplicativos.  Você precisará desses valores ao implantar a máquina virtual
### <a name="add-vm-configuration-key-values"></a>Adicionar pares chave-valor de configuração da VM

Depois de criar um repositório de Configuração de Aplicativos, você poderá usar o portal do Azure ou a CLI do Azure para adicionar pares chave-valor ao repositório.

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o repositório da Configuração de Aplicativos recém-criado.
1. Selecione **Gerenciador de configurações** no menu à esquerda.
1. Selecione **Criar** para adicionar os seguintes pares chave-valor:

   |Chave|Valor|Rotular|
   |-|-|-|
   |windowsOsVersion|2019-Datacenter|template|
   |diskSizeGB|1023|template|

   Deixe o **Tipo de Conteúdo** vazio.

Para usar a CLI do Azure, confira [Trabalhar com os pares chave-valor em um repositório de Configuração de Aplicativos do Azure](./scripts/cli-work-with-keys.md).

### <a name="deploy-vm-using-stored-key-values"></a>Implantar a VM usando pares chave-valor armazenados

Agora que adicionou pares chave-valor ao repositório, você está pronto para implantar uma VM usando um modelo do Azure Resource Manager. O modelo referencia as chaves **windowsOsVersion** e **diskSizeGB** criadas.

> [!WARNING]
> Os modelos do ARM não podem referenciar chaves em um repositório de Configurações de Aplicativos que tenha o Link Privado habilitado.

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria uma máquina virtual usando pares chave-valor armazenados no repositório de Configuração de Aplicativos.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. Selecione ou insira os seguintes valores.

    - **assinatura**: selecione a assinatura do Azure usada para criar a máquina virtual.
    - **Grupo de recursos**: especifique o mesmo grupo de recursos que o repositório de Configuração de Aplicativos ou selecione **Criar** para criar um grupo de recursos.
    - **Região**: selecione uma localização para o grupo de recursos.  Por exemplo, **Leste dos EUA**.
    - **Localização**: especifique a localização da máquina virtual. Use o valor padrão.
    - **Nome de Usuário do Administrador**: especifique um nome de usuário do administrador para a máquina virtual.
    - **Senha do Administrador**: especifique uma senha do administrador para a máquina virtual.
    - **Rótulo de Nome de Domínio**: especifique um nome de domínio exclusivo.
    - **Nome da Conta de Armazenamento**: especifique um nome exclusivo para uma conta de armazenamento associada à máquina virtual.
    - **Grupo de Recursos do Repositório de Configuração de Aplicativos**: especifique o grupo de recursos que contém o repositório de Configuração de Aplicativos.
    - **Nome do Repositório de Configuração de Aplicativos**: especifique o nome do repositório de Configuração de Aplicativos do Azure.
    - **Chave de SKU da VM**: especifique **windowsOsVersion**.  Esse é o nome do valor de chave que você adicionou ao repositório.
    - **Chave do Tamanho do Disco**: especifique **diskSizeGB**. Esse é o nome do valor de chave que você adicionou ao repositório.

1. Selecione **Examinar + criar**.
1. Verifique se a página mostra **Validação Aprovada** e, em seguida, selecione **Criar**.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até a máquina virtual recém-criada.
1. Selecione **Visão Geral** no menu à esquerda e verifique se o **SKU** é **2019-Datacenter**.
1. Selecione **Discos** no menu à esquerda e verifique se o tamanho do disco de dados é **2013**.

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua o grupo de recursos, o repositório da Configuração de Aplicativos, a VM e todos os recursos relacionados quando eles não forem mais necessários. Se estiver planejando usar o repositório da Configuração de Aplicativos ou a VM no futuro, ignore a exclusão deles. Se você não pretende continuar usando esse trabalho, exclua todos os recursos criados por este início rápido executando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma VM usando um modelo do Azure Resource Manager e pares chave-valor da Configuração de Aplicativos do Azure.

Para saber mais sobre como criar outros aplicativos com a Configuração de Aplicativos do Azure, prossiga para o seguinte artigo:

> [!div class="nextstepaction"]
> [Início Rápido: Criar um aplicativo ASP.NET Core com a Configuração de Aplicativos do Azure](quickstart-aspnet-core-app.md)
