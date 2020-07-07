---
author: baanders
description: arquivo de inclusão dos Gêmeos Digitais do Azure – configurar o Cloud Shell e a extensão de IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 8be070826de0334483f4150925c05cb4dfb73f2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85806014"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Configurar uma sessão do Cloud Shell

Depois que você abrir uma janela do Cloud Shell, a primeira coisa a fazer será fazer logon e definir o contexto do shell para a sua assinatura nesta sessão. Execute estes comandos no seu Cloud Shell:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Se esta é a primeira vez que você usa essa assinatura com os Gêmeos Digitais do Azure, execute este comando para se registrar no namespace dos Gêmeos Digitais do Azure. (Se você não tiver certeza disso, não haverá problemas em executá-lo novamente mesmo que já tenha feito isso no passado.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Em seguida, você adicionará a [**Extensão de IoT do Microsoft Azure para a CLI do Azure**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) ao seu Cloud Shell para habilitar comandos para interagir com os Gêmeos Digitais do Azure e com outros serviços de IoT. 

Primeiro, execute este comando para ver uma lista de todas as extensões que você já instalou.

```azurecli-interactive
az extension list
```

Na saída, procure o `"name"` campo de cada entrada de lista para ver os nomes das extensões.

Use a saída para determinar quais dos comandos a seguir devem ser executados para a configuração de extensão (você pode executar mais de um).
* Se a lista contiver `azure-iot` : você já tem a extensão. Execute este comando para verificar se você tem a atualização mais recente:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Se a **lista não contiver** `azure-iot` : você precisa instalar a extensão. Use este comando:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Se a lista contiver `azure-iot-cli-ext` : esta é a versão herdada da extensão. Somente uma versão da extensão deve ser instalada de cada vez, portanto, você deve desinstalar a extensão herdada. Use este comando:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

Agora você está pronto para trabalhar com os Gêmeos Digitais do Azure no Cloud Shell.

É possível verificar isso executando `az dt -h` a qualquer momento para ver uma lista dos comandos dos Gêmeos Digitais do Azure de nível superior que estão disponíveis.