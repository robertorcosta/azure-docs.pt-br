---
title: Gerenciar o IoT Central do Azure PowerShell | Microsoft Docs
description: Este artigo descreve como criar e gerenciar seus aplicativos de IoT Central do Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 7c55d0568832fcefee6e0763810c5e1220480270
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278857"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Gerenciar o IoT Central do Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar IoT Central aplicativos no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) , você pode usar [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para gerenciar seus aplicativos.

## <a name="prerequisites"></a>pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você preferir executar o Azure PowerShell em seu computador local, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Quando você executar o Azure PowerShell localmente, use o cmdlet **AzAccount Connect** para entrar antes de experimentar os cmdlets neste artigo.

## <a name="install-the-iot-central-module"></a>Instalar o módulo do IoT Central

Execute o seguinte comando para verificar se o [módulo do IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) está instalado em seu ambiente do PowerShell:

```powershell
Get-InstalledModule -name Az.I*
```

Se a lista de módulos instalados não incluir **Az.IotCentral**, execute o seguinte comando:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Criar um aplicativo

Use o cmdlet [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) para criar um aplicativo do IoT Central na sua assinatura do Azure. Por exemplo:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

O script cria primeiro um grupo de recursos no local leste dos EUA para o aplicativo. A tabela a seguir descreve os parâmetros usados com o comando **New-AzIotCentralApp**:

|.         |DESCRIÇÃO |
|------------------|------------|
|ResourceGroupName |O grupo de recursos que contém o aplicativo. Esse grupo de recursos já precisa existir na sua assinatura. |
|Local padrão |Por padrão, esse cmdlet usa a localização do grupo de recursos. No momento, você pode criar um aplicativo IoT Central no **Estados Unidos**, na **austrália**, no **Pacífico Asiático**ou nos locais da **Europa** .  |
|NOME              |Digite o nome do aplicativo no portal do Azure. |
|Subdomínio         |O subdomínio na URL do aplicativo. No exemplo, a URL do aplicativo é https://mysubdomain.azureiotcentral.com. |
|Sku               |Atualmente, o único valor é **S1** (camada standard). Confira [Preço do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modelo          | O modelo de aplicativo a usar. Para obter mais informações, confira a tabela a seguir: |
|displayName       |O nome do aplicativo, conforme exibido na interface do usuário. |

**Modelos de aplicativo**

|Nome do modelo  |DESCRIÇÃO |
|---------------|------------|
|iotc-default@1.0.0 |Cria um aplicativo vazio para você preencher com seus próprios dispositivos e modelos de dispositivos. |
|iotc-demo@1.0.0    |Cria um aplicativo que inclui um modelo de dispositivo já criado para uma Máquina de Vendas Refrigerada. Use esse modelo para começar a explorar o Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Cria um aplicativo com modelos de dispositivos prontos para você conectar um dispositivo MXChip ou Raspberry Pi. Use esse modelo se você for um desenvolvedor de dispositivos experimentando com algum desses dispositivos. |

> [!NOTE]
> No momento, o modelo de **aplicativo de visualização** só está disponível nas regiões **Europa setentrional** e **EUA Central** .

## <a name="view-your-iot-central-applications"></a>Exibir seus aplicativos do IoT Central

Use o cmdlet [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) para listar seus aplicativos do IoT Central e exibir os metadados.

## <a name="modify-an-application"></a>Modificar um aplicativo

Use o cmdlet [AzIotCentralApp conjunto](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) para atualizar os metadados de um aplicativo do IoT Central. Por exemplo, para alterar o nome de exibição do aplicativo:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Remover um aplicativo

Use o cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) para excluir um aplicativo do IoT Central. Por exemplo:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central no Azure PowerShell, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administrar o aplicativo](howto-administer.md)
