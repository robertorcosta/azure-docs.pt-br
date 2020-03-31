---
title: Criar um Hub IoT do Azure usando um modelo (PowerShell) | Microsoft Docs
description: Como usar um modelo do Azure Resource Manager para criar um Hub ioT com o Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976616"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Criar um Hub IoT usando um modelo do Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Aprenda a usar um modelo do Azure Resource Manager para criar um IoT Hub e um grupo de consumidores. Os modelos do Resource Manager são arquivos JSON que definem os recursos necessários para implantar sua solução. Para obter mais informações sobre o desenvolvimento de modelos do Gerenciador de Recursos, consulte [a documentação do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

O modelo de Gerenciador de recursos usado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Veja uma cópia do modelo:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

O modelo cria um hub de Iot do Azure com três pontos finais (eventhub, nuvem-para-dispositivo e mensagens) e um grupo de consumidores. Para obter mais amostras de modelos, consulte [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). O esquema de modelo do Iot Hub pode ser encontrado [aqui](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Existem vários métodos para implantar um modelo.  Você usa o Azure PowerShell neste tutorial.

Para executar o script PowerShell, selecione **Tente-o** para abrir o shell do Azure Cloud. Para colar o script, clique com o botão direito do mouse na concha e, em seguida, selecione Colar:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Como você pode ver no script PowerShell, o modelo usado é de modelos Azure Quickstart. Para usar o seu próprio, você precisa primeiro carregar o arquivo `-TemplateFile` de modelo para o shell Cloud e, em seguida, usar o switch para especificar o nome do arquivo.  Por exemplo, consulte [Implantar o modelo](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou um hub de IoT usando um modelo do Azure Resource Manager, você pode querer explorar mais:

* Leia sobre as funcionalidades da [API REST do provedor de recursos Hub IoT][lnk-rest-api].
* Leia a [Visão geral do Azure Resource Manager][lnk-azure-rm-overview] para saber mais sobre os recursos do Azure Resource Manager.
* Para obter a sintaxe e as propriedades de JSON a serem usadas em modelos, consulte [Tipos de recurso Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Para saber mais sobre como desenvolver para o Hub IoT, veja os seguintes artigos:

* [Introdução ao SDK C][lnk-c-sdk]
* [SDKs do Azure IoT][lnk-sdks]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Implantando o AI em dispositivos de borda com o Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
