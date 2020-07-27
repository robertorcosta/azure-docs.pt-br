---
title: Com esse modelo do Azure Resource Manager, você poderá implantar o ponto de extremidade privado para o Aplicativo Web.
description: Saiba como usar o modelo do ARM para implantar o ponto de extremidade privado para seu Aplicativo Web
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ff2d59ed569037f34e24a69ffafa0df237a3de34
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524775"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-resource-manager-template"></a>Criar um aplicativo do Serviço de Aplicativo e implantar um ponto de extremidade privado usando o modelo do Azure Resource Manager

Neste guia de início rápido, você usará um modelo do Azure Resource Manager para criar um Aplicativo Web e expô-lo com um ponto de extremidade privado.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Pré-requisito

Você precisa de uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

Este modelo cria um ponto de extremidade privado para um Aplicativo Web do Azure.

### <a name="review-the-template"></a>Examinar o modelo

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Implantar o modelo

Veja como implantar o modelo do Azure Resource Manager no Azure:

1. Para entrar no Azure e abrir o modelo, selecione **Implantar no Azure**. O modelo cria a VNet, o Aplicativo Web, o ponto de extremidade privado e a zona DNS privada.

   [Implantar no Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)

2. Selecione ou crie o grupo de recursos.
3. Digite o nome do Aplicativo Web, do Plano do Serviço de Aplicativo e do ponto de extremidade privado.
5. Leia os termos e condições. Se você concordar, selecione Concordo com os termos e as condições declarados acima > Comprar. A implantação poderá levar vários minutos para ser concluída.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos criados com o ponto de extremidade privado, exclua o grupo de recursos. Isso remove o ponto de extremidade privado e todos os recursos relacionados.

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Próximas etapas

- Encontre outros modelos do Azure Resource Manager para Aplicativos Web do Serviço de Aplicativo do Azure nas [amostras de modelos do ARM](../samples-resource-manager-templates.md).
