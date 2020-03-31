---
title: Crie um namespace de ônibus de serviço do Azure usando o modelo
description: Usar modelo do Azure Resource Manager para criar um namespace das Mensagens do Barramento de Serviço
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 5febdd63ab6f854ca3244f8449f6f715a75e735f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264468"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Crie um namespace de barramento de serviço usando um modelo do Azure Resource Manager

Aprenda a implantar um modelo do Azure Resource Manager para criar um namespace do Service Bus. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades. Para obter mais informações sobre a criação de modelos, consulte [a documentação do Azure Resource Manager](/azure/azure-resource-manager/).

Os seguintes modelos também estão disponíveis para criar espaços de nomes de Ônibus de Serviço:

* [Criar um namespace do Barramento de Serviço com fila](./service-bus-resource-manager-namespace-queue.md)
* [Criar um namespace do Barramento de Serviço com tópico e assinatura](./service-bus-resource-manager-namespace-topic.md)
* [Create a Service Bus namespace with queue and authorization rule (Criar um namespace de Barramento de Serviço com fila e regra de autorização)](./service-bus-resource-manager-namespace-auth-rule.md)
* [Criar um namespace do Barramento de Serviço com tópico, assinatura e regra](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-service-bus-namespace"></a>Criar um namespace de barramento de serviço

Neste quickstart, você usa um [modelo de gerenciador](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) de recursos existente a partir de [modelos quickstart do Azure:](https://azure.microsoft.com/resources/templates/)

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Para encontrar mais exemplos de modelos, consulte [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Para criar um namespace de barramento de serviço, implantando um modelo:

1. Selecione **Testar** no seguinte bloco de código e, em seguida, siga as instruções para entrar no Azure Cloud Shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    O nome do grupo de recursos é o nome do namespace do ponto de ônibus de serviço com **rg** anexado.

2. Selecione **Copiar** para copiar o script do PowerShell.
3. Clique com o botão direito do mouse no console do Shell e selecione **Colar**.

Pode demorar alguns minutos para criar um hub de eventos.

## <a name="verify-the-deployment"></a>Verificar a implantação

Para ver o namespace do barramento de serviço implantado, você pode abrir o grupo de recursos do portal Azure ou usar o script Azure PowerShell a seguir. Se o shell cloud ainda estiver aberto, você não precisa copiar/executar a primeira e segunda linhas do script a seguir.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

O Azure PowerShell é usado para implantar o modelo neste tutorial. Para outros métodos de implantação de modelos, consulte:

* [Usando o portal Azure.](../azure-resource-manager/templates/deploy-portal.md)
* [Usando o Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Usando a API REST](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos. Se o shell cloud ainda estiver aberto, você não precisa copiar/executar a primeira e segunda linhas do script a seguir.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um namespace do Barramento de Serviço. Consulte outros guias de início rápido para saber como criar filas, tópicos/assinaturas e como usá-los:

* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Introdução aos tópicos do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
