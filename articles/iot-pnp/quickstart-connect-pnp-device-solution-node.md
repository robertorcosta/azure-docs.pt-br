---
title: Interagir com o dispositivo IoT Plug and Play em versão prévia conectado à sua solução – Node.js | Microsoft Docs
description: Use o Node.js para se conectar a um dispositivo IoT Plug and Play Preview que está conectado à sua solução de IoT do Azure e interagir com ele.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 7f86ae8ffefa88fe1cdfa429b7de4671422839ff
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044007"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Início Rápido: Interagir com um dispositivo em versão prévia IoT Plug and Play que está conectado à sua solução (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

O IoT Plug and Play Preview simplifica a IoT, permitindo que você interaja com as funcionalidades de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este início rápido mostra como usar o Node.js para se conectar a um dispositivo IoT Plug and Play que está conectado à sua solução e controlá-lo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa do Node.js em seu computador de desenvolvimento. Você pode baixar a versão mais recente recomendada para várias plataformas de [nodejs.org](https://nodejs.org).

Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o seguinte comando para obter a _cadeia de conexão do hub IoT_ para o seu hub (nota para usar mais tarde):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

Neste início rápido, você usará um sensor ambiental de exemplo que é escrito em Node.js como o dispositivo IoT Plug and Play. As seguintes instruções mostram como instalar e executar o dispositivo:

1. Abra uma janela de terminal no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub das [Amostras de IoT do Azure para Node.js](https://github.com/azure-samples/azure-iot-samples-node) nesta localização:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Esta janela de terminal agora será usada como seu terminal de _dispositivo_. Acesse a pasta do repositório clonado e navegue até a pasta do **/azure-iot-samples-node/digital-twins/Quickstarts/Device**. Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Execute a amostra com o seguinte comando:

    ```cmd/sh
    node sample_device.js
    ```

1. Você vê mensagens dizendo que o dispositivo enviou algumas informações e se reportou online. Isso indica que o dispositivo começou a enviar dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade. Não feche este terminal; você precisará dele mais tarde para confirmar se as amostras de serviço também funcionaram.

## <a name="run-the-sample-solution"></a>Executar a solução de exemplo

Neste início rápido, você usará uma solução de IoT de exemplo em Node.js para interagir com o dispositivo de exemplo.

1. Abra outra janela de terminal (este será o seu terminal de _serviço_). Acesse a pasta do repositório clonado e navegue até a pasta do **/azure-iot-samples-node/digital-twins/Quickstarts/Service**. Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure a _cadeia de conexão do hub IoT_ para permitir que o serviço se conecte a ele:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando você conectou o _dispositivo_ em seu terminal, você viu a seguinte mensagem indicando o status online:

    ```cmd/sh
    reported state property as online
    ```

1. Na pasta **/azure-iot-samples-node/digital-twins/Quickstarts/Service**, abra o arquivo **get_digital_twin.js**. Substitua o espaço reservado `<DEVICE_ID_GOES_HERE>` pela identificação do dispositivo e salve o arquivo.

1. Vá para o terminal de _serviço_ e use o seguinte comando para executar a amostra de leitura de informações do dispositivo:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Na saída do terminal de _serviço_, role até o componente `environmentalSensor`. Você verá que a propriedade `state` foi relatada como _online_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Atualizar uma propriedade gravável

1. Abra o arquivo **update_digital_twin_property.js**.

1. No início do arquivo, há um conjunto de constantes definidas com espaços reservados em maiúsculas. Substitua o espaço reservado `<DEVICE_ID_GOES_HERE>` pela identificação do dispositivo real, atualize as constantes restantes com os seguintes valores e salve o arquivo:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Vá para o terminal de _serviço_ e use o seguinte comando para executar a amostra de atualização da propriedade:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. A saída do terminal de _serviço_ mostra as informações de dispositivo atualizadas. Role até o componente `environmentalSensor` para ver o novo valor de brilho de 42.

    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

1. Acesse o terminal do _dispositivo_ e você verá que o dispositivo recebeu a atualização:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Volte para o terminal de _serviço_ e execute o comando abaixo para obter as informações do dispositivo novamente e confirmar que a propriedade foi atualizada.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. Na saída do terminal de _serviço_, no componente `environmentalSensor`, você vê que o valor de brilho atualizado foi relatado. Observação: pode levar algum tempo para que o dispositivo conclua a atualização. Repita essa etapa até o dispositivo realmente processar a atualização da propriedade.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>Invocar um comando

1. Abra o arquivo **invoke_command.js**.

1. No início do arquivo, substitua o espaço reservado `<DEVICE_ID_GOES_HERE>` pela identificação do dispositivo real. Atualize as constantes restantes com os seguintes valores e, em seguida, salve o arquivo:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Vá para o terminal de _serviço_. Use o seguinte comando para executar a amostra para invocar o comando:

    ```cmd/sh
    node invoke_command.js
    ```

1. A saída no terminal de _serviço_ deve mostrar a seguinte confirmação:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Acesse o terminal do _dispositivo_ e você verá que o comando foi confirmado:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a uma solução de IoT. Para saber mais sobre como criar uma solução que interage com os dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Instruções: Conectar-se a um dispositivo e interagir com ele](howto-develop-solution.md)
