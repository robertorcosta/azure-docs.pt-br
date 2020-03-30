---
title: Mensagens da nuvem para o dispositivo com o Hub IoT do Azure (Nó)| Microsoft Docs
description: Como enviar mensagens da nuvem para o dispositivo para um dispositivo de um Hub IoT do Azure usando os SDKs do IoT do Azure para Node.js. Modifique um aplicativo de dispositivo simulado para receber mensagens da nuvem para o dispositivo e modificar um aplicativo de back-end para enviá-las.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: 8071ddbc5f6073598daf0a08d359ccd19ccd1e4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110795"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Envie mensagens nuvem-para-dispositivo com o IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. A [telemetria Send de um dispositivo para um hub de IoT](quickstart-send-telemetry-node.md) mostra como criar um hub de IoT, provisionar uma identidade de dispositivo nele e codificar um aplicativo de dispositivo simulado que envia mensagens dispositivo-nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial se baseia em [Enviar telemetria de um dispositivo para um hub de IoT](quickstart-send-telemetry-node.md). Ele mostra como:

* Da sua solução de back-end, envie mensagens da nuvem para o dispositivo em um único dispositivo por meio do Hub IoT.
* Receber mensagens da nuvem para o dispositivo em um dispositivo.
* A partir do back-end da solução, solicite o reconhecimento de entrega *(feedback)* para mensagens enviadas a um dispositivo do IoT Hub.

Você pode encontrar mais informações sobre mensagens de nuvem para dispositivo no guia de desenvolvedores do [IoT Hub](iot-hub-devguide-messaging.md).

Ao fim deste tutorial, você executará dois aplicativos de console do Node.js:

* **SimulatedDevice**, uma versão modificada do aplicativo criado em [Send telemetria de um dispositivo para um hub IoT,](quickstart-send-telemetry-node.md)que se conecta ao seu hub IoT e recebe mensagens nuvem-dispositivo.

* **SendCloudToDeviceMessage**, que envia uma mensagem nuvem-para-dispositivo para o aplicativo de dispositivo simulado através do IoT Hub, e depois recebe seu reconhecimento de entrega.

> [!NOTE]
> O IoT Hub tem suporte a SDK para muitas plataformas de dispositivos e idiomas (incluindo C, Java, Python e Javascript) através de SDKs de dispositivos Azure IoT. Para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, veja o [Centro de Desenvolvedores do IoT do Azure](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Pré-requisitos

* Node.js versão 10.0.x ou posterior. [Preparar o ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) descreve como instalar o Node.js para este tutorial no Windows ou no Linux.

* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial) em apenas alguns minutos.)

* Verifique se a porta 8883 está aberta no firewall. A amostra do dispositivo neste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens no aplicativo do dispositivo simulado

Nesta seção, você modifica o aplicativo de dispositivo simulado que você criou na [telemetria Enviar de um dispositivo para um hub de IoT](quickstart-send-telemetry-node.md) para receber mensagens nuvem-dispositivo do hub IoT.

1. Usando um editor de texto, abra o arquivo **SimuladoDevice.js.** Este arquivo está localizado na pasta **iot-hub\Quickstarts\simulated-device** off da pasta raiz do código de amostra Node.js que você baixou na [telemetria Enviar de um dispositivo para um hub de IoT](quickstart-send-telemetry-node.md) quickstart.

2. Registre um manipulador com o cliente do dispositivo para receber mensagens enviadas do IoT Hub. Adicione a `client.on` chamada logo após a linha que cria o cliente do dispositivo como no seguinte trecho:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    Neste exemplo, o dispositivo invoca a função **completa** para notificar o IoT Hub de que processou a mensagem. A chamada a **ser concluída** não é necessária se você estiver usando o transporte MQTT e pode ser omitido. É necessário para HTTPS e AMQP.
  
   > [!NOTE]
   > Se você usar HTTPS em vez de MQTT ou AMQP como transporte, a instância **DeviceClient** verificará se há mensagens do Hub IoT com pouca frequência (menos de cada 25 minutos). Para obter mais informações sobre as diferenças entre o suporte do MQTT, AMQP e HTTPS e a limitação do Hub IoT, consulte o [Guia do Desenvolvedor do Hub IoT](iot-hub-devguide-messaging.md).
   >

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a seqüência de conexão do hub IoT

Neste artigo, você cria um serviço de back-end para enviar mensagens nuvem-dispositivo através do hub ioT que você criou no [Enviar telemetria de um dispositivo para um hub ioT](quickstart-send-telemetry-node.md). Para enviar mensagens em nuvem para dispositivo, seu serviço precisa da permissão de conexão de **serviço.** Por padrão, todo IoT Hub é criado com uma política de acesso compartilhado chamada **service** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem da nuvem para o dispositivo

Nesta seção, você criará um aplicativo do console do Node.js que envia mensagens da nuvem ao dispositivo para o aplicativo do dispositivo simulado. Você precisa do ID do dispositivo que você adicionou na [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md) quickstart. Você também precisa da seqüência de conexão de hub IoT que você copiou anteriormente em [Obter a seqüência de conexão de hub IoT](#get-the-iot-hub-connection-string).

1. Crie uma pasta vazia denominada **sendcloudtodevicemessage**. Na pasta **sendcloudtodevicemessage**, crie um arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:

    ```shell
    npm init
    ```

2. No prompt de comando, na pasta **sendcloudtodevicemessage**, execute o seguinte comando para instalar o pacote **azure-iothub**:

    ```shell
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um arquivo **SendCloudToDeviceMessage.js** na pasta **sendcloudtodevicemessage**.

4. Adicione as seguintes instruções `require` no início do arquivo **SendCloudToDeviceMessage.js** :

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Adicione o seguinte código ao arquivo **SendCloudToDeviceMessage.js** . Substitua os valores de espaço reservado "{iot hub connection string}" e "{device id}" por uma seqüência de conexões de hub IoT e iD do dispositivo que você observou anteriormente:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Adicione a seguinte função para imprimir os resultados da operação no console:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Adicione a seguinte função para imprimir mensagens de comentários de entrega para o console:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Adicione o seguinte código para enviar uma mensagem ao dispositivo e lidar com a mensagem de comentário quando o dispositivo reconhecer a mensagem da nuvem para o dispositivo:

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Salve e feche o arquivo **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando na pasta **de dispositivo simulado,** execute o seguinte comando para enviar telemetria ao IoT Hub e para ouvir mensagens em nuvem para dispositivo:

    ```shell
    node SimulatedDevice.js
    ```

    ![Executar um aplicativo de dispositivo simulado](./media/iot-hub-node-node-c2d/receivec2d.png)

2. No prompt de comando na pasta **sendcloudtodevicemessage**, execute o comando a seguir para enviar uma mensagem da nuvem para o dispositivo e esperar os comentários de confirmação:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Execute o aplicativo para enviar o comando da nuvem para o dispositivo](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugerido no artigo [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Próximas etapas

Neste tutorial você aprendeu a enviar e receber mensagens da nuvem para o dispositivo.

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Acelerador de solução de Monitoramento Remoto do Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de desenvolvedores do [IoT Hub](iot-hub-devguide.md).
