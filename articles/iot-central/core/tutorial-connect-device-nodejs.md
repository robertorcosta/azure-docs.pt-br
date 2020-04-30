---
title: Tutorial – Conectar um aplicativo cliente Node.js genérico ao Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como desenvolvedor de dispositivos, como conectar um dispositivo que executa um aplicativo cliente Node.js ao seu aplicativo do Azure IoT Central. Você criará um modelo de dispositivo importando um modelo de funcionalidade do dispositivo e adicionará exibições que permitem interagir com um dispositivo conectado
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt
ms.openlocfilehash: a8c5d9479585c0a519d0ad05a4d73f3f15b21287
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758190"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Tutorial: Criar e conectar um aplicativo cliente ao seu aplicativo do Azure IoT Central (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Este artigo se aplica a construtores de solução e desenvolvedores de dispositivos.*

Este tutorial mostra a você, como desenvolvedor de dispositivos, como conectar um aplicativo cliente Node.js ao seu aplicativo do Azure IoT Central. O aplicativo Node.js simula o comportamento de um dispositivo de sensor ambiental. Use um exemplo de _modelo de funcionalidade do dispositivo_ a fim de criar um _modelo de dispositivo_ no IoT Central. Adicione exibições ao modelo de dispositivo para permitir que um operador interaja com um dispositivo.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Importar um modelo de funcionalidade do dispositivo para criar um modelo de dispositivo.
> * Adicionar exibições padrão e personalizadas a um modelo de dispositivo.
> * Publicar um modelo de dispositivo e adicionar um dispositivo real ao seu aplicativo do IoT Central.
> * Criar e executar o código do dispositivo Node.js e ver se ele se conecta ao seu aplicativo do IoT Central.
> * Veja a telemetria simulada enviada do dispositivo.
> * Usar uma exibição para gerenciar as propriedades do dispositivo.
> * Chame comandos síncronos e assíncronos para controlar o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa do seguinte:

* Um aplicativo do Azure IoT Central criado usando o modelo de **Aplicativo personalizado**. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).
* Um computador de desenvolvimento com o [Node.js](https://nodejs.org/) versão 10.0.0 ou posterior instalado. É possível executar `node --version` na linha de comando para verificar a versão. As instruções deste tutorial pressupõem que você esteja executando o comando **node** no prompt de comando do Windows. Contudo, você pode usar o Node.js em muitos outros sistemas operacionais.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js

As etapas a seguir mostram como criar um aplicativo cliente Node.js que se conecta ao dispositivo real adicionado ao aplicativo. Esse aplicativo Node.js simula o comportamento de um dispositivo real.

1. No ambiente de linha de comando, navegue até a pasta `environmental-sensor` criada anteriormente.

1. Para inicializar o projeto Node.js e instalar as dependências necessárias, execute os seguintes comandos e aceite todas as opções padrão ao executar `npm init`:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Crie um arquivo chamado **environmentalSensor.js** na pasta `environmental-sensor`.

1. Adicione as seguintes instruções `require` ao início do arquivo **environmentalSensor.js**:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Adicione as declarações de variáveis a seguir ao arquivo:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    Atualize os espaços reservados `{your Scope ID}`, `{your Device ID}` e `{your Primary Key}` com os valores anotados anteriormente. Nesta amostra, você inicializará `targetTemperature` em zero e poderá usar a leitura atual do dispositivo ou um valor do dispositivo gêmeo.

1. Para enviar a telemetria simulada ao aplicativo do Azure IoT Central, adicione a seguinte função ao arquivo:

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Os nomes dos itens de telemetria (`temp` e `humid`) devem corresponder aos nomes usados no modelo de dispositivo.

1. Para enviar as propriedades de dispositivo gêmeo ao aplicativo do Azure IoT Central, adicione a seguinte função ao arquivo:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    O IoT Central usa dispositivos gêmeos para sincronizar valores de propriedade entre o dispositivo e o aplicativo do IoT Central. Os valores de propriedade do dispositivo usam propriedades relatadas do dispositivo gêmeo. As propriedades graváveis usam as propriedades relatadas e desejadas do dispositivo gêmeo.

1. Para definir e processar as propriedades graváveis às quais o dispositivo responde, adicione o seguinte código:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            writeableProperties[setting](desiredChange[setting].value, (newValue, status) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Quando o operador define uma propriedade gravável no aplicativo IoT Central, o aplicativo usa uma propriedade desejada do dispositivo gêmeo para enviar o valor para o dispositivo. Em seguida, o dispositivo responde usando uma propriedade relatada do dispositivo gêmeo. Quando o IoT Central recebe o valor da propriedade relatada, ele atualiza a exibição da propriedade com um status **sincronizado**.

    Os nomes das propriedades (`name` e `brightness`) devem corresponder aos nomes usados no modelo de dispositivo.

1. Adicione o seguinte código para processar os comandos enviados do aplicativo do IoT Central:

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    Os nomes dos comandos (`blink`, `turnon`, `turnoff` e `rundiagnostics`) devem corresponder aos nomes usados no modelo de dispositivo.

    No momento, o IoT Central não usa o esquema de resposta definido no modelo de funcionalidade do dispositivo. Para um comando síncrono, o conteúdo de resposta pode ser qualquer JSON válido. Para um comando assíncrono, o dispositivo deverá retornar uma resposta 202 seguida imediatamente pela atualização da propriedade relatada quando o trabalho for concluído. O formato da atualização da propriedade relatada é:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Um operador pode ver o conteúdo da resposta no histórico de comandos.

1. Adicione o seguinte código para concluir a conexão ao Azure IoT Central e associe as funções no código cliente:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Execute o aplicativo Node.js

Para iniciar o aplicativo cliente do dispositivo, execute o seguinte comando no ambiente de linha de comando:

```cmd/sh
node environmentalSensor.js
```

Você poderá ver se o dispositivo se conecta ao aplicativo do Azure IoT Central e começa a enviar a telemetria:

![Executar o aplicativo cliente](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Você pode ver como o dispositivo responde a comandos e a atualizações de propriedade:

![Observe o aplicativo cliente](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>Próximas etapas

Como desenvolvedor de dispositivos, agora que você aprendeu as noções básicas de como criar um dispositivo usando Node.js, algumas próximas etapas sugeridas são:

- Saiba como conectar um dispositivo real a IoT Central no artigo de instruções [Conectar um dispositivo MXChip IoT DevKit ao seu aplicativo do Azure IoT Central](./howto-connect-devkit.md).
- Leia [Conectar-se ao Azure IoT Central](./concepts-get-connected.md) para saber mais sobre como registrar dispositivos com o IoT Central e como o IoT Central protege as conexões do dispositivo.

Se você preferir continuar com o conjunto de tutoriais do IoT Central e saber mais sobre como criar uma solução de IoT Central, confira:

> [!div class="nextstepaction"]
> [Criar um modelo de dispositivo de gateway](./tutorial-define-gateway-device-type.md)
