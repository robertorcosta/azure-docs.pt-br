---
title: Criar um Hub IoT do Azure usando um cmdlet do PowerShell | Microsoft Docs
description: Aprenda a usar os cmdlets do PowerShell para criar um grupo de recursos e, em seguida, crie um hub de IoT no grupo de recursos. Aprenda também como remover o hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890598"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Crie um hub de IoT usando o cmdlet New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

É possível usar os cmdlets do Azure PowerShell para criar e gerenciar hubs IoT do Azure. Este tutorial mostra como criar um hub IoT com o PowerShell.

Para concluir estas instruções, você precisa de uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Conecte-se à sua assinatura do Azure

Se você estiver usando o Cloud Shell, você já estará conectado à sua assinatura. Se em vez disso você estiver executando o PowerShell localmente, digite o seguinte comando para entrar em sua assinatura do Azure:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Você precisa de um grupo de recursos para implantar um hub IoT. É possível usar um grupo de recursos existente ou criar um novo.

Para criar um grupo de recursos para o seu hub de IoT, use o comando [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) Este exemplo cria um grupo de recursos chamado **MyIoTRG1** na região **Leste dos EUA**:

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Para criar um hub de IoT no grupo de recursos criado na etapa anterior, use o comando [New-AzIotHub.](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) Este exemplo cria um hub **S1** chamado **MyTestIoTHub** na região **Leste dos EUA**:

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

O nome do Hub IoT deve ser globalmente exclusivo.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Você pode listar todos os hubs de IoT em sua assinatura usando o comando [Get-AzIotHub:](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub)

```azurepowershell-interactive
Get-AzIotHub
```

Este exemplo mostra o Hub IoT Standard S1, que você criou na etapa anterior.

Você pode excluir o hub IoT usando o comando [Remove-AzIotHub:](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub)

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Alternativamente, você pode remover um grupo de recursos e todos os recursos que ele contém usando o comando [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup)

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Próximas etapas

Agora que você implantou um Hub IoT usando um cmdlet do PowerShell, se desejar explorar ainda mais, confira os seguintes artigos:

* [Cmdlets do PowerShell para trabalhar com o Hub IoT](https://docs.microsoft.com/powershell/module/az.iothub/).

* [Provedor de recursos IoT Hub REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Para saber mais sobre como desenvolver para o Hub IoT, veja os seguintes artigos:

* [Introdução ao SDK C](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
