---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453685"
---
## <a name="clean-up-resources"></a>Limpar os recursos

Se planejar continuar com os artigos adicionais de IoT Plug and Play, guarde e use novamente os recursos usados neste início rápido. Caso contrário, exclua os recursos criados neste início rápido a fim de evitar encargos adicionais.

Você pode excluir o hub e o dispositivo registrado ao mesmo tempo excluindo o grupo de recursos inteiro com o comando a seguir para a CLI do Azure. (No entanto, não use isso se esses recursos estiverem compartilhando um grupo de recursos com outros recursos que você tem para finalidades diferentes.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Para excluir apenas o hub IoT, execute o seguinte comando usando a CLI do Azure:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Para excluir apenas a identidade do dispositivo registrada no hub IoT, execute o seguinte comando usando a CLI do Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Talvez você também queira remover os arquivos de exemplo clonados do seu computador de desenvolvimento.