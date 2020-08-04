---
title: Interagir com um dispositivo IoT Plug and Play em versão prévia conectado à solução de IoT do Azure (Node.js) | Microsoft Docs
description: Use o Node.js para se conectar a um dispositivo IoT Plug and Play Preview que está conectado à sua solução de IoT do Azure e interagir com ele.
author: elhorton
ms.author: elhorton
ms.date: 07/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9ddca58e166baa1e94fcc3edcfbbc64abd578049
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352533"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Início Rápido: Interagir com um dispositivo em versão prévia IoT Plug and Play que está conectado à sua solução (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

O IoT Plug and Play Preview simplifica a IoT, permitindo que você interaja com as funcionalidades de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este início rápido mostra como usar o Node.js para se conectar a um dispositivo IoT Plug and Play que está conectado à sua solução e controlá-lo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa do Node.js em seu computador de desenvolvimento. Você pode baixar a versão mais recente recomendada para várias plataformas de [nodejs.org](https://nodejs.org).

Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

```cmd/sh
node --version
```

Instale o [SDK do serviço de nó com o suporte do IoT Plug and Play](https://www.npmjs.com/package/azure-iot-digitaltwins-service) executando o seguinte comando:

```cmd/sh
npm i azure-iot-digitaltwins-service
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o comando a seguir para obter a _cadeia de conexão do hub IoT_ para o seu hub. Anote essa cadeia de conexão, pois você a usará posteriormente neste guia de início rápido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Execute o comando a seguir para obter a _cadeia de conexão de dispositivo_ do dispositivo que você adicionou ao hub. Anote essa cadeia de conexão, pois você a usará posteriormente neste guia de início rápido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

Neste início rápido, você pode usar um dispositivo de termostato de exemplo que é escrito em Node.js como o dispositivo do IoT Plug and Play. Para executar o dispositivo de exemplo:

1. Abra uma janela de terminal em uma pasta de sua escolha. Execute o seguinte comando para clonar o repositório GitHub do [SDK do IoT do Microsoft Azure para Node.js](https://github.com/Azure/azure-iot-sdk-node) nesta localização:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Esta janela de terminal é usada como o seu terminal de **dispositivo**. Acesse a pasta do repositório clonado e navegue até a pasta */azure-iot-sdk-node/device/samples/pnp*. Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Execute o dispositivo de termostato de exemplo com o seguinte comando:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Você vê mensagens dizendo que o dispositivo enviou algumas informações e se reportou online. Essas mensagens indicam que o dispositivo começou a enviar dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade. Não feche este terminal; você precisará dele para confirmar se a amostra de serviço está funcionando.

## <a name="run-the-sample-solution"></a>Executar a solução de exemplo

Neste início rápido, você usará uma solução de IoT de exemplo em Node.js para interagir com o dispositivo de exemplo que acabou de configurar.

1. Abra outra janela de terminal para usar como o seu terminal de **serviço**. O SDK do serviço está em versão prévia, portanto, você precisa clonar os exemplos de um [branch de versão prévia do SDK do Node](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node -b public-preview-pnp
    ```

1. Acesse a pasta do branch desse repositório clonado e navegue até a pasta */azure-iot-samples-node/digital-twins/samples/service/javascript*. Instale todas as dependências executando o seguinte comando:

    ```cmd/sh
    npm install
    ```

1. Configure as variáveis de ambiente para a ID do dispositivo e a _cadeia de conexão do Hub IoT_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando você executou o dispositivo termostato de exemplo no terminal do **dispositivo**, você viu as seguintes mensagens indicando seu status online:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Vá para o terminal de **serviço** e use o seguinte comando para executar a amostra de leitura de informações do dispositivo:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. No saída de terminal do **serviço**, observe a resposta do gêmeo digital. Você verá a ID do modelo do dispositivo e as propriedades associadas relatadas:

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. O seguinte snippet mostra o código em *get_digital_twin.js* que recupera a ID do modelo do dispositivo gêmeo:

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

Neste cenário, ele gera `Model Id: dtmi:com:example:Thermostat;1`.

### <a name="update-a-writable-property"></a>Atualizar uma propriedade gravável

1. Abra o arquivo *update_digital_twin_property.js* em um editor de código.

1. Examine o código de exemplo. Você pode ver como criar um patch em JSON para atualizar o gêmeo digital do seu dispositivo. Neste exemplo, o código substitui a temperatura do termostato pelo valor 42:

    ```javascript
    const patch = [{
        op: 'add',
        path: 'targetTemperature',
        value: '42'
      }]
    ```

1. No terminal de **serviço**, use o seguinte comando para executar a amostra de atualização da propriedade:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. A saída do terminal de **serviço** mostra as informações de dispositivo atualizadas. Role até o componente `thermostat1` para ver o novo valor `targetTemperature` de 42:

    ```json
    "modelId": "dtmi:com:example:Thermostat;1",
        "version": 12,
        "properties": {
            "desired": {
                "targetTemperature": "42",
                "$metadata": {
                    "$lastUpdated": "2020-07-09T13:55:50.7976985Z",
                    "$lastUpdatedVersion": 5,
                    "targetTemperature": {
                        "$lastUpdated": "2020-07-09T13:55:50.7976985Z",
                        "$lastUpdatedVersion": 5
                    }
                },
                "$version": 5
            },
            "reported": {
                "serialNumber": "123abc",
                "maxTempSinceLastReboot": 32.279942997143785,
                "targetTemperature": {
                    "value": "42",
                    "ac": 200,
                    "ad": "Successfully executed patch for targetTemperature",
                    "av": 2
                },
    ```

1. No terminal do **dispositivo**, você verá que o dispositivo recebeu a atualização:

    ```cmd/sh
    Received an update for targetTemperature: 42
    updated the property
    ```

1. No terminal de **serviço**, execute o seguinte comando para confirmar se a propriedade está atualizada:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Na saída do terminal de **serviço**, na resposta do gêmeo digital no componente `thermostat1`, você verá a temperatura de destino atualizada relatada. Pode demorar algum tempo para que o dispositivo conclua a atualização. Repita essa etapa até que o dispositivo tenha processado a atualização da propriedade:

    ```json
    "$model": "dtmi:com:example:Thermostat;1",
    "targetTemperature": {
      "desiredValue": 42,
      "desiredVersion": 4,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch for targetTemperature",
      "lastUpdateTime": "2020-07-09T13:55:30.5062641Z"
    }
    ```

### <a name="invoke-a-command"></a>Invocar um comando

1. Abra o arquivo *invoke_command.js* e examine o código.

1. Vá para o terminal de **serviço**. Use o seguinte comando para executar a amostra para invocar o comando:

    ```cmd/sh
    node invoke_command.js
    ```

1. A saída no terminal de **serviço** mostra a seguinte confirmação:

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. No terminal do **dispositivo**, você verá que o comando foi confirmado:

    ```cmd/sh
    MaxMinReport [object Object]
    Response to method 'getMaxMinReport' sent successfully.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a uma solução de IoT. Para saber mais sobre os modelos de dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Guia do desenvolvedor de modelagem de versão prévia do IoT Plug and Play](concepts-developer-guide.md)
