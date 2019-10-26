---
title: Conectar um aplicativo cliente Node.js genérico ao Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivos, como conectar um dispositivo node. js genérico ao aplicativo IoT Central do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 09/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 76ab6a229de14af1e3808326c62a7bdbbd188e81
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951361"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Conectar um aplicativo cliente genérico ao aplicativo Azure IoT Central (Node.js)

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve, como um desenvolvedor de dispositivos, como se conectar a um aplicativo Node.js genérico representando um dispositivo real ao aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

- Um aplicativo Azure IoT Central. Para obter mais informações, consulte [criar um início rápido de aplicativo](quick-deploy-iot-central.md).
- Um computador de desenvolvimento com [Node.js](https://nodejs.org/) versão 4.0.0 ou posterior instalado. É possível executar `node --version` na linha de comando para verificar a versão. O Node.js está disponível para uma ampla variedade de sistemas operacionais.

## <a name="create-a-device-template"></a>Criar um modelo de dispositivo

No aplicativo IoT Central do Azure, você precisa de um modelo de dispositivo com as seguintes medidas, propriedades do dispositivo, configurações e comandos:

### <a name="telemetry-measurements"></a>Medidas de telemetria

Adicione a seguinte telemetria na página **medidas** :

| Nome de exibição | Nome do campo  | Unidades | Min | Máx. | Casas Decimais |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperatura | F     | 60  | 110 | 0              |
| Umidade     | umidade    | %     | 0   | 100 | 0              |
| Pressão     | pressão    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> O tipo de dados da medida de telemetria é número de ponto flutuante.

Insira os nomes dos campos exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código de dispositivo correspondente, a telemetria não poderá ser exibida no aplicativo.

### <a name="state-measurements"></a>Medidas de estado

Adicione o seguinte estado na página **medidas** :

| Nome de exibição | Nome do campo  | Valor 1 | Nome de exibição | Valor 2 | Nome de exibição |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Modo ventilação     | fanmode     | 1       | Executando      | 0       | Parado      |

> [!NOTE]
> O tipo de dados da medida de estado é cadeia de caracteres.

Insira os nomes dos campos exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código de dispositivo correspondente, o estado não poderá ser exibido no aplicativo.

### <a name="event-measurements"></a>Medidas de evento

Adicione o seguinte evento na página **medidas** :

| Nome de exibição | Nome do campo  | Gravidade |
| ------------ | ----------- | -------- |
| Superaquecimento  | overheat    | Erro    |

> [!NOTE]
> O tipo de dados da medida de evento é cadeia de caracteres.

### <a name="location-measurements"></a>Medidas de localização

Adicione a seguinte medida de localização na página **medidas** :

| Nome de exibição | Nome do campo  |
| ------------ | ----------- |
| Location     | location    |

O tipo de dados de medição local é composto de dois números de ponto flutuante para longitude e latitude e um número de ponto flutuante opcional para altitude.

Insira os nomes dos campos exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código de dispositivo correspondente, o local não poderá ser exibido no aplicativo.

### <a name="device-properties"></a>Propriedades do dispositivo

Adicione as seguintes propriedades de dispositivo na página **Propriedades** :

| Nome de exibição        | Nome do campo        | Tipo de dados |
| ------------------- | ----------------- | --------- |
| Número de série       | serialNumber      | text      |
| Fabricante do dispositivo | manufacturer      | text      |

Insira os nomes de campo exatamente conforme mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código de dispositivo correspondente, as propriedades não poderão ser exibidas no aplicativo.

### <a name="settings"></a>Configurações

Adicione as seguintes configurações de **número** na página **configurações** :

| Nome de exibição    | Nome do campo     | Unidades | Decimais | Min | Máx.  | Inicial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Velocidade da ventoinha       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Temperatura definida | setTemperature | F     | 0        | 20  | 200  | 80      |

Insira o nome de campo exatamente conforme mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código do dispositivo correspondente, o dispositivo não poderá receber o valor da configuração.

### <a name="commands"></a>Comandos

Adicione o seguinte comando na página **comandos** :

| Nome de exibição    | Nome do campo     | Tempo limite padrão | Tipo de Dados |
| --------------- | -------------- | --------------- | --------- |
| Contagem regressiva       | contagem regressiva      | 30              | número    |

Adicione o seguinte campo de entrada ao comando de contagem regressiva:

| Nome de exibição    | Nome do campo     | Tipo de Dados | Value |
| --------------- | -------------- | --------- | ----- |
| Contar de      | countFrom      | número    | 10    |

Insira nomes de campo exatamente como mostrado nas tabelas no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código de dispositivo correspondente, o dispositivo não poderá processar o comando.

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo IoT Central do Azure, adicione um dispositivo real ao modelo de dispositivo que você criou na seção anterior.

Anote as informações de conexão do dispositivo na página **conexão do dispositivo** : **ID do escopo**, **ID do dispositivo**e **chave primária**. Você adicionará esses valores no código do dispositivo posteriormente neste guia de instruções:

![Informações de conexão do dispositivo](./media/howto-connect-nodejs/device-connection.png)

### <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js

As etapas a seguir mostram como criar um aplicativo cliente que implementa o dispositivo real que você adicionou ao aplicativo. Aqui, o aplicativo Node.js representa o dispositivo real.

1. Crie uma pasta chamada `connected-air-conditioner-adv` no computador. Navegue até essa pasta no ambiente de linha de comando.

1. Para inicializar o projeto Node.js, execute os comandos a seguir:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Crie um arquivo chamado **connectedAirConditionerAdv.js** na pasta `connected-air-conditioner-adv`.

1. Adicione as `require` instruções no início do arquivo **connectedAirConditionerAdv.js**:

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
    var locLong = -122.1215;
    var locLat = 47.6740;
    ```

    Atualize os espaços reservados `{your Scope ID}`, `{your Device ID}`e `{your Primary Key}` com os valores anotados anteriormente. Neste exemplo, você inicializa `targetTemperature` como zero, pode usar a leitura atual do dispositivo ou um valor do dispositivo.

1. Para enviar medidas de telemetria, estado, evento e local para o aplicativo de IoT Central do Azure, adicione a seguinte função ao arquivo:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Para enviar as propriedades de dispositivo ao aplicativo Azure IoT Central, adicione a função a seguir ao arquivo:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Para definir as configurações que o dispositivo responde, adicione a definição a seguir:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Para identificar configurações atualizadas do aplicativo Azure IoT Central, adicione o seguinte ao arquivo:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Adicione o seguinte código para manipular um comando de contagem regressiva enviado do aplicativo IoT Central:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');
    
      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;
    
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          hubClient.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }
    
            doCountdown();
          });
        }
      });
    }
    ```

1. Adicione o seguinte código para concluir a conexão ao Azure IoT Central e associe as funções no código cliente:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        hubClient.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
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

Execute o comando a seguir no ambiente de linha de comando:

```cmd/sh
node connectedAirConditionerAdv.js
```

Como um operador no aplicativo Azure IoT Central, para o dispositivo real, é possível:

* Exibir a telemetria na página **Medidas**:

    ![Exibir telemetria](media/howto-connect-nodejs/viewtelemetry.png)

* Exiba o local na página **medidas** :

    ![Exibir medidas de local](media/howto-connect-nodejs/viewlocation.png)

* Exibir os valores da propriedade de dispositivo enviados pelo dispositivo na página **Propriedades**. Os blocos de propriedades do dispositivo são atualizados quando o dispositivo se conecta:

    ![Exibir propriedades do dispositivo](media/howto-connect-nodejs/viewproperties.png)

* Defina a velocidade do ventilador e a temperatura de destino na página **configurações** :

    ![Configurar velocidade da ventoinha](media/howto-connect-nodejs/setfanspeed.png)

* Chame o comando de contagem regressiva na página **comandos** :

    ![Comando chamar contagem regressiva](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu como conectar um cliente Node. js genérico ao aplicativo IoT Central do Azure, a próxima etapa sugerida é aprender a [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo IOT.
