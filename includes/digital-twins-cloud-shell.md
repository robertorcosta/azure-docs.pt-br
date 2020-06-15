---
author: baanders
description: arquivo de inclusão dos Gêmeos Digitais do Azure – configurar o Cloud Shell e a extensão de IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84611468"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Configurar uma sessão do Cloud Shell

Depois que você abrir uma janela do Cloud Shell, a primeira coisa a fazer será fazer logon e definir o contexto do shell para a sua assinatura nesta sessão. 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Se esta é a primeira vez que você usa essa assinatura com os Gêmeos Digitais do Azure, execute este comando para se registrar no namespace dos Gêmeos Digitais do Azure. (Se você não tiver certeza disso, não haverá problemas em executá-lo novamente mesmo que já tenha feito isso no passado.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Em seguida, execute o comando a seguir na instância do Cloud Shell para adicionar a Extensão de IoT do Microsoft Azure para a CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> Este artigo usa a versão mais recente da extensão de IoT do Azure, chamada `azure-iot`. A versão herdada chama-se `azure-iot-cli-ext`. Você deve ter apenas uma versão instalada por vez. Use o comando `az extension list` para validar quais extensões estão instaladas.
> Use `az extension remove --name azure-cli-iot-ext` para remover a versão herdada da extensão.
> Use `az extension add --name azure-iot` para adicionar a nova versão da extensão. Para ver quais extensões você tem instaladas, use `az extension list`.

> [!TIP]
> Execute `az dt -h` para ver os comandos de nível superior dos Gêmeos Digitais do Azure.