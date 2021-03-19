---
title: Guia de início rápido Enviar telemetria do dispositivo para o Hub IoT do Azure (Node.js)
description: Neste guia de início rápido, use o SDK do Dispositivo do Hub IoT do Azure para Node.js para enviar telemetria de um dispositivo para um hub IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 97fcff4706d6da968c93426f85569fed9af95aac
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197802"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>Guia de início rápido: Enviar telemetria de um dispositivo para um hub IoT (Node.js)

**Aplica-se a**: [desenvolvimento de aplicativos do dispositivo](about-iot-develop.md#device-application-development)

Neste guia de início rápido, aprenda um fluxo de trabalho básico de desenvolvimento de aplicativos do dispositivo IoT. Use a CLI do Azure para criar um hub IoT do Azure e um dispositivo simulado e use o SDK do Node.js de IoT do Azure para acessar o dispositivo e enviar telemetria para o hub.

## <a name="prerequisites"></a>Pré-requisitos
- Se você não tiver uma assinatura do Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- CLI do Azure. Você pode executar todos os comandos neste guia de início rápido usando o Azure Cloud Shell, um shell da CLI interativa que é executado em seu navegador. Se você usar o Cloud Shell, não precisará instalar nada. Se você preferir usar a CLI localmente, este guia de início rápido exigirá a CLI do Azure versão 2.0.76 ou posterior. Execute az --version para localizar a versão. Para instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
- [Node.js 10+](https://nodejs.org). Se você estiver usando o Azure Cloud Shell, não atualize a versão instalada do Node.js. O Azure Cloud Shell já tem a versão mais recente do Node.js.

    Verifique a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>Usar o SDK do Node.js para enviar mensagens
Nesta seção, você usará o SDK do Node.js para enviar mensagens do seu dispositivo simulado para o hub IoT. 

1. Abra uma nova janela de terminal. Você usará esse terminal para instalar o SDK do Node.js e trabalhar com o código de exemplo do Node.js. Agora você deve ter dois terminais abertos: aquele que você acabou de abrir para trabalhar com o Node.js e o shell da CLI que você usou nas seções anteriores para inserir os comandos da CLI do Azure. 

1. Copie os [exemplos de dispositivo do SDK do Node.js de IoT do Azure](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) para o computador local:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Navegue até o diretório *azure-iot-sdk-node/device/samples*:

    ```console
    cd azure-iot-sdk-node/device/samples
    ```
1. Instale o SDK do Node.js de IoT do Azure e as dependências necessárias:

    ```console
    npm install
    ```
    Esse comando instala as dependências apropriadas conforme especificado no arquivo *package.json* no diretório de exemplos do dispositivo.

1. Defina a Cadeia de Conexão do Dispositivo como uma variável de ambiente chamada `DEVICE_CONNECTION_STRING`. O valor da cadeia de caracteres a ser usado é da cadeia de caracteres obtida na seção anterior depois de criar o dispositivo Node.js simulado. 

    **Windows (cmd)**

    ```console
    set DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > Para o Windows CMD, não há aspas ao redor da cadeia de conexão.

    **Linux (bash)**

    ```bash
    export DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. No shell da CLI aberta, execute o comando [az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) para iniciar o monitoramento de eventos no seu dispositivo IoT simulado.  As mensagens de evento serão impressas no terminal à medida que chegarem.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. No terminal do Node.js, execute o código do arquivo de exemplo instalado *simple_sample_device.js*. Esse código acessa o dispositivo IoT simulado e envia uma mensagem ao hub IoT.

    Para executar o exemplo do Node.js no terminal:
    ```console
    node ./simple_sample_device.js
    ```

    Opcionalmente, você pode executar o código do Node.js por meio do exemplo no seu JavaScript IDE:
    ```javascript
    'use strict';

    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    // Uncomment one of these transports and then change it in fromConnectionString to test other transports
    // const Protocol = require('azure-iot-device-amqp').AmqpWs;
    // const Protocol = require('azure-iot-device-http').Http;
    // const Protocol = require('azure-iot-device-amqp').Amqp;
    // const Protocol = require('azure-iot-device-mqtt').MqttWs;
    const Client = require('azure-iot-device').Client;
    const Message = require('azure-iot-device').Message;

    // String containing Hostname, Device Id & Device Key in the following formats:
    //  "HostName=<iothub_host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>"
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    let sendInterval;

    function disconnectHandler () {
    clearInterval(sendInterval);
    client.open().catch((err) => {
        console.error(err.message);
    });
    }

    // The AMQP and HTTP transports have the notion of completing, rejecting or abandoning the message.
    // For example, this is only functional in AMQP and HTTP:
    // client.complete(msg, printResultFor('completed'));
    // If using MQTT calls to complete, reject, or abandon are no-ops.
    // When completing a message, the service that sent the C2D message is notified that the message has been processed.
    // When rejecting a message, the service that sent the C2D message is notified that the message won't be processed by the device. the method to use is client.reject(msg, callback).
    // When abandoning the message, IoT Hub will immediately try to resend it. The method to use is client.abandon(msg, callback).
    // MQTT is simpler: it accepts the message by default, and doesn't support rejecting or abandoning a message.
    function messageHandler (msg) {
    console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
    client.complete(msg, printResultFor('completed'));
    }

    function generateMessage () {
    const windSpeed = 10 + (Math.random() * 4); // range: [10, 14]
    const temperature = 20 + (Math.random() * 10); // range: [20, 30]
    const humidity = 60 + (Math.random() * 20); // range: [60, 80]
    const data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed, temperature: temperature, humidity: humidity });
    const message = new Message(data);
    message.properties.add('temperatureAlert', (temperature > 28) ? 'true' : 'false');
    return message;
    }

    function errorCallback (err) {
    console.error(err.message);
    }

    function connectCallback () {
    console.log('Client connected');
    // Create a message and send it to the IoT Hub every two seconds
    sendInterval = setInterval(() => {
        const message = generateMessage();
        console.log('Sending message: ' + message.getData());
        client.sendEvent(message, printResultFor('send'));
    }, 2000);

    }

    // fromConnectionString must specify a transport constructor, coming from any transport package.
    let client = Client.fromConnectionString(deviceConnectionString, Protocol);

    client.on('connect', connectCallback);
    client.on('error', errorCallback);
    client.on('disconnect', disconnectHandler);
    client.on('message', messageHandler);

    client.open()
    .catch(err => {
    console.error('Could not connect: ' + err.message);
    });

    // Helper function to print results in the console
    function printResultFor(op) {
    return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
    };
    }
    ```

Como o código Node.js envia uma mensagem de telemetria simulada do seu dispositivo para o hub IoT, a mensagem é exibida no shell da CLI que está monitorando os eventos:

```output
event:
  component: ''
  interface: ''
  module: ''
  origin: <your device name>
  payload: '{"deviceId":"myFirstDevice","windSpeed":11.853592092144627,"temperature":22.62484121157508,"humidity":66.17960805575937}'
```

Agora, o seu dispositivo está conectado com segurança e está enviando telemetria ao Hub IoT do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos
Se você não precisar mais dos recursos do Azure criados neste guia de início rápido, poderá usar a CLI do Azure para excluí-los.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. 

Para excluir um grupo de recursos por nome:
1. Execute o comando [az group delete](/cli/azure/group#az-group-delete). Esse comando removerá o grupo de recursos, o Hub IoT e o registro de dispositivo que você criou.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Execute o comando [az group list](/cli/azure/group#az-group-list) para confirmar se o grupo de recursos foi excluído.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu um fluxo de trabalho básico do aplicativo de IoT do Azure para conectar com segurança um dispositivo à nuvem e enviar telemetria do dispositivo para nuvem. Você usou a CLI do Azure para criar um hub IoT e um dispositivo simulado e usou o SDK do Node.js de IoT do Azure para acessar o dispositivo e enviar telemetria para o hub. 

Como próxima etapa, explore o SDK do Node.js de IoT do Azure por meio de exemplos de aplicativo.

- [Mais Exemplos de Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples): esse diretório contém mais exemplos do repositório do SDK do Node.js para demonstrar cenários do Hub IoT.