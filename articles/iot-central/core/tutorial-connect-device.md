---
title: Tutorial – Conectar um aplicativo cliente Node.js genérico ao Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, como desenvolvedor de dispositivos, como conectar um dispositivo que executa um aplicativo cliente Node.js ao seu aplicativo do Azure IoT Central. Você criará um modelo de dispositivo importando um modelo de funcionalidade do dispositivo e adicionará exibições que permitem interagir com um dispositivo conectado
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77624332"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Tutorial: Criar e conectar um aplicativo cliente Node.js ao seu aplicativo do Azure IoT Central (Node.js)

Este tutorial mostra a você, como desenvolvedor de dispositivos, como conectar um aplicativo cliente Node.js ao seu aplicativo do Azure IoT Central. O aplicativo Node.js simula o comportamento de um dispositivo real. Você usará um exemplo de _modelo de funcionalidade do dispositivo_ para um dispositivo de sensor ambiental a fim de criar um _modelo de dispositivo_ no IoT Central. Você adicionará exibições ao modelo de dispositivo para permitir visualizar a telemetria do dispositivo, gerenciar as propriedades dele e usar comandos para controlar seus dispositivos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Importar um modelo de funcionalidade do dispositivo para criar um modelo de dispositivo.
> * Adicionar exibições padrão e personalizadas a um modelo de dispositivo.
> * Publicar um modelo de dispositivo e adicionar um dispositivo real ao seu aplicativo do IoT Central.
> * Criar e executar o código do dispositivo Node.js e ver se ele se conecta ao seu aplicativo do IoT Central.
> * Exiba a telemetria simulada enviada pelo dispositivo.
> * Usar uma exibição para gerenciar as propriedades do dispositivo.
> * Chamar comandos para controlar o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa do seguinte:

* Um aplicativo do Azure IoT Central criado com base no modelo de **Aplicativo personalizado**. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).
* Um computador de desenvolvimento com o [Node.js](https://nodejs.org/) versão 10.0.0 ou posterior instalado. É possível executar `node --version` na linha de comando para verificar a versão. O Node.js está disponível para uma ampla variedade de sistemas operacionais. As instruções deste tutorial pressupõem que você esteja executando o comando **node** no prompt de comando do Windows. Você pode usar o Node.js em uma variedade de sistemas operacionais.

## <a name="create-a-device-template"></a>Crie um modelo de dispositivo

Crie uma pasta chamada `environmental-sensor` no computador local.

Baixe o arquivo JSON [Modelo de capacidade do sensor ambiental](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) e salve-o na pasta `environmental-sensor`.

Use um editor de texto para substituir as duas instâncias de `{YOUR_COMPANY_NAME_HERE}` pelo nome da sua empresa no arquivo `EnvironmentalSensorInline.capabilitymodel.json` baixado.

No aplicativo do Azure IoT Central, crie um modelo de dispositivo chamado *Sensor ambiental* importando o arquivo de modelo de funcionalidade do dispositivo `EnvironmentalSensorInline.capabilitymodel.json`:

![Modelo de dispositivo com o modelo de funcionalidade do dispositivo importado](./media/tutorial-connect-device/device-template.png)

O modelo de funcionalidade do dispositivo inclui duas interfaces: a interface padrão **Informações do Dispositivo** e a interface personalizada **Sensor Ambiental**. A interface **Sensor Ambiental** define as seguintes funcionalidades:

| Type | Nome de exibição | Descrição |
| ---- | ------------ | ----------- |
| Propriedade | Estado do dispositivo     | O estado do dispositivo. Dois estados online/offline estão disponíveis. |
| Propriedade | Nome do Cliente    | O nome do cliente que está operando o dispositivo no momento. |
| Propriedade | Nível de brilho | O nível de brilho da luz no dispositivo. Pode ser especificado como 1 (alto), 2 (médio) ou 3 (baixo). |
| Telemetria | Temperatura | Temperatura atual no dispositivo. |
| Telemetria | Umidade    | Umidade atual no dispositivo. |
| Comando | blink          | Comece a piscar o LED para um intervalo de tempo especificado. |
| Comando | turnon         | Ligue a luz LED no dispositivo. |
| Comando | turnoff        | Desligue a luz LED no dispositivo. |
| Comando | rundiagnostics | Esse comando inicia uma execução de diagnóstico. |

Para personalizar como a propriedade **Estado do Dispositivo** é exibida no aplicativo do IoT Central, selecione **Personalizar** no modelo de dispositivo. Expanda a entrada **Estado do Dispositivo**, insira _Online_ como o **Nome verdadeiro** e _Offline_ como o **Nome falso**. Em seguida, salve as alterações:

![Personalizar o modelo de dispositivo](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Criar exibições

As exibições permitem que você interaja com os dispositivos conectados ao seu aplicativo do IoT Central. Por exemplo, você pode ter exibições que mostrem a telemetria, exibições que mostrem as propriedades e exibições que permitam editar propriedades graváveis e de nuvem. As exibições fazem parte de um modelo de dispositivo.

Para adicionar algumas exibições padrão ao modelo de dispositivo **Sensor ambiental**, navegue até o modelo de dispositivo, selecione **Exibições** e o bloco **Gerar Exibições Padrão**. Verifique se as opções **Visão geral** e **Sobre** estão **Ativadas** e, em seguida, selecione **Gerar exibições de painel padrão**. Agora você tem duas exibições padrão definidas no modelo.

A interface **Sensor Ambiental** inclui duas propriedades graváveis: **Nome do Cliente** e **Nível de Brilho**. Para criar uma exibição, use para editar estas propriedades:

1. Selecione **Exibições** e, em seguida, o bloco **Editando dados do dispositivo e da nuvem**.

1. Insira _Propriedades_ como o nome do formulário.

1. Selecione as propriedades **Nível de Brilho** e **Nome do Cliente**. Em seguida, selecione **Adicionar seção**.

1. Salve suas alterações.

![Adicionar uma exibição para habilitar a edição de propriedade](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>Publicar o modelo

Antes de adicionar um dispositivo ao seu aplicativo do IoT Central que usa o modelo de dispositivo **Sensor Ambiental**, você precisará publicar o modelo.

No modelo de dispositivo, selecione **Publicar**. No painel que mostra as alterações a serem publicadas, selecione **Publicar**.

Para verificar se o modelo está pronto para uso, navegue até a página **Dispositivos** no aplicativo do IoT Central. A seção **Dispositivos** mostra uma lista dos dispositivos publicados no aplicativo:

![Modelos publicados na página dos dispositivos](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo do Azure IoT Central, adicione um dispositivo real ao modelo de dispositivo criado na seção anterior:

1. Na página **Dispositivos**, selecione o modelo de dispositivo **Sensor ambiental**.

1. Selecione **+ Novo**.

1. Verifique se a opção **Simulado** está **Desativada**. Em seguida, selecione **Criar**.

Clique no nome do dispositivo e, em seguida, selecione **Conectar**. Anote as informações de conexão do dispositivo na página **Conexão do Dispositivo**: **Escopo da ID**, **Identificação do Dispositivo** e **Chave primária**. Você precisará desses valores ao criar o código do dispositivo:

![Informações de conexão do dispositivo](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js

As etapas a seguir mostram como criar um aplicativo cliente Node.js que implementa o dispositivo real adicionado ao aplicativo. Esse aplicativo Node.js simula o comportamento de um dispositivo real.

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

1. Para enviar a telemetria ao aplicativo do Azure IoT Central, adicione a seguinte função ao arquivo:

    ```javascript
    // Send device measurements.
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

1. Para enviar as propriedades de dispositivo ao aplicativo Azure IoT Central, adicione a função a seguir ao arquivo:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Para definir e processar as propriedades graváveis às quais o dispositivo responde, adicione o seguinte código:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
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
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. Adicione o seguinte código para processar os comandos enviados do aplicativo do IoT Central:

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
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

    // Handle LED turn off command
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

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
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
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
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

Para iniciar o aplicativo cliente do dispositivo, execute o seguinte comando no ambiente de linha de comando:

```cmd/sh
node environmentalSensor.js
```

Você poderá ver se o dispositivo se conecta ao aplicativo do Azure IoT Central e começa a enviar a telemetria:

![Executar o aplicativo cliente](media/tutorial-connect-device/run-application.png)

Como operador no seu aplicativo do Azure IoT Central, você pode:

* Ver a telemetria enviada pelo dispositivo na página **Visão geral**:

    ![Exibir telemetria](media/tutorial-connect-device/view-telemetry.png)

* Atualizar os valores de propriedades graváveis na página **Propriedades**:

    ![Atualizar as propriedades](media/tutorial-connect-device/update-properties.png)

    ![Atualizar as propriedades no dispositivo](media/tutorial-connect-device/update-properties-device.png)

* Chamar os comandos na página **Comandos**:

    ![Chamar o comando de intermitência](media/tutorial-connect-device/call-command.png)

    ![Chamar o comando de intermitência no dispositivo](media/tutorial-connect-device/call-command-device.png)

* Ver as propriedades do dispositivo na página **Sobre**:

    ![Exibir propriedades](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os modelos de funcionalidade do dispositivo e como criar os próprios modelos de dispositivo, prossiga para o guia de instruções:

> [!div class="nextstepaction"]
> [Definir um novo tipo de dispositivo de IoT](./howto-set-up-template.md)
