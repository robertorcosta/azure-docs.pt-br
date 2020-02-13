---
title: 'Início Rápido: Criar um hub de eventos com um grupo de consumidores – Hubs de Eventos do Azure'
description: 'Início Rápido: Criar um namespace de Hubs de Eventos com um Hub de Eventos e um grupo de consumidores usando modelos do Azure Resource Manager'
services: event-hubs
documentationcenter: .net
author: spelluru
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 88cd29af75239f0ad79eb78b5ff8e106c3b2ee56
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163068"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Início Rápido: Criar um hub de eventos usando o modelo do Azure Resource Manager

Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Neste guia de início rápido, você criará um hub de eventos usando um [modelo do Azure Resource Manager](../azure-resource-manager/management/overview.md). Implante um modelo do Azure Resource Manager para criar um namespace do tipo [Hubs de Eventos](event-hubs-what-is-event-hubs.md) com um hub de eventos. O artigo mostra como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades. Para obter informações sobre como criar modelos, consulte [Criação de modelos do Azure Resource Manager][Authoring Azure Resource Manager templates]. Para que as propriedades e a sintaxe JSON sejam usadas no modelo, consulte [Tipos de recursos Microsoft.EventHub](/azure/templates/microsoft.eventhub/allversions).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-event-hub"></a>Criar um Hub de Evento

Neste guia de início rápido, você usará um [modelo de início rápido existente](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json):

[!code-json[create-azure-event-hub-namespace](~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json)]

Para encontrar mais exemplos de modelos, consulte [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

Para implantar o modelo:

1. Selecione **Testar** no seguinte bloco de código e, em seguida, siga as instruções para entrar no Azure Cloud Shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Pode demorar alguns minutos para criar um hub de eventos.

1. Selecione **Copiar** para copiar o script do PowerShell.
1. Clique com o botão direito do mouse no console do Shell e selecione **Colar**.

## <a name="verify-the-deployment"></a>Verificar a implantação

Para verificar a implantação é possível abrir o grupo de recursos do [portal do Azure](https://portal.azure.com) ou usar o seguinte script do Azure PowerShell.  Se o Cloud Shell ainda estiver aberto, não será necessário copiar/executar a primeira linha (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos. Se o Cloud Shell ainda estiver aberto, não será necessário copiar/executar a primeira linha (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um namespace dos Hubs de Eventos e um hub de eventos no namespace. Para obter instruções passo a passo sobre como enviar eventos (ou) receber eventos de um hub de eventos, confira os tutoriais para **Enviar e receber eventos**:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (somente enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (somente receber)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
