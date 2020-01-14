---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 7a0ea2330e682e1e60eca6540151b0f5f620571c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453692"
---
## <a name="prepare-an-iot-hub"></a>Preparar um hub IoT

Você também precisará de um Hub IoT do Azure em sua assinatura do Azure para concluir este início rápido. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Caso não tenha um hub IoT, siga [estas instruções para criar um](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Durante a versão prévia pública, os recursos de IoT Plug and Play estão disponíveis apenas em hubs IoT criados nas regiões **Centro dos EUA**, **Europa Setentrional** e **Leste do Japão**.

Se estiver usando a CLI do Azure localmente, primeiro entre na assinatura do Azure usando `az login`. Se estiver executando esses comandos no Azure Cloud Shell, você entrará automaticamente.

Se estiver usando a CLI do Azure no local, a versão do `az` deverá ser **2.0.73** ou posterior; o Azure Cloud Shell usa a versão mais recente. Use o comando `az --version` para verificar a versão instalada no computador.

Execute o seguinte comando para adicionar a Extensão de IoT do Microsoft Azure para a CLI do Azure à instância:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Use o comando a seguir para criar uma identidade do dispositivo no Hub IoT. Substitua os espaços reservados **YourIoTHubName** e **YourDeviceID** pelo seu próprio _nome do Hub IoT_ e uma _ID do dispositivo_ de sua escolha.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Execute o seguinte comando para obter a _cadeia de conexão de dispositivo_ do dispositivo que você acabou de registrar (nota para usar mais tarde):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```