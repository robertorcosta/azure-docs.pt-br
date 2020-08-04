---
title: Interagir com o dispositivo IoT Plug and Play em versão prévia conectado à sua solução – Java | Microsoft Docs
description: Use o Java para se conectar a um dispositivo IoT Plug and Play Preview que está conectado à sua solução de IoT do Azure e interagir com ele.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 752d1d925c55ed79c7eb1673c6602adb9c2371fb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320979"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Início Rápido: Interagir com um dispositivo IoT Plug and Play Preview que está conectado à sua solução (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

O IoT Plug and Play Preview simplifica a IoT, permitindo que você interaja com as funcionalidades de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este início rápido mostra como usar o Java para se conectar a um dispositivo IoT Plug and Play que está conectado à sua solução e controlá-lo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa do Java SE 8 em seu computador de desenvolvimento. Você também precisa instalar o Maven 3.

Para obter detalhes sobre como configurar essas informações, confira [Preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) no SDK do dispositivo IoT do Microsoft Azure para Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o seguinte comando para obter a _cadeia de conexão do hub IoT_ para o seu hub (nota para usar mais tarde):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

Neste início rápido, você usará um sensor ambiental de exemplo que é escrito em Java como o dispositivo IoT Plug and Play. As seguintes instruções mostram como instalar e executar o dispositivo:

1. Abra uma janela de terminal no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub das [Amostras de IoT do Azure para Java](https://github.com/Azure-Samples/azure-iot-samples-java) nesta localização:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Esta janela de terminal agora será usada como seu terminal de _dispositivo_. Acesse a pasta do repositório clonado e navegue até a pasta **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample** folder. Instale as bibliotecas necessárias e compilar o aplicativo de dispositivo simulado executando o seguinte comando:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Execute o comando a seguir para executar o exemplo da pasta do dispositivo.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Você vê mensagens dizendo que o dispositivo está conectado, executando várias etapas de configuração e aguardando atualizações de serviço, seguidas pelos logs de telemetria. Isso indica que o dispositivo agora está pronto para receber comandos e atualizações de propriedade e começou a enviar dados telemétricos para o hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas. Não feche este terminal; você precisará dele mais tarde para confirmar se as amostras de serviço também funcionaram.

## <a name="run-the-sample-solution"></a>Executar a solução de exemplo

Neste início rápido, você usará uma solução de IoT de exemplo em Java para interagir com o dispositivo de exemplo.

1. Abra outra janela de terminal (este será o seu terminal de _serviço_). Acesse a pasta do repositório clonado e navegue até a pasta **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample**.

1. Instale as bibliotecas necessárias e crie o exemplo de serviço executando o seguinte comando:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configure a _cadeia de conexão do Hub IoT_ e a _ID do dispositivo_ para permitir que o serviço se conecte a ambos:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando você conectou o _dispositivo_ em seu terminal, uma das mensagens de saída foi a seguinte para indicar seu status online. A propriedade `state`, que é usada para indicar se o dispositivo está online ou não, é _true_:

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Vá para o terminal de _serviço_ e use o seguinte comando para executar a amostra de serviço para ler informações do dispositivo:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Na saída do terminal de _serviço_, role até o componente `environmentalSensor`. Você verá que a propriedade `state` foi relatada como _true_:
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Atualizar uma propriedade gravável

1. Vá para o terminal de _serviço_ e defina as seguintes variáveis para determinar qual propriedade atualizar:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Use o comando a seguir para executar a amostra de serviço para atualizar a propriedade:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. A saída do terminal de _serviço_ mostra as informações de dispositivo atualizadas. Role até o componente `environmentalSensor` para ver o novo valor de brilho de 42.

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. Acesse o terminal do _dispositivo_ e você verá que o dispositivo recebeu a atualização:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Volte para o terminal de _serviço_ e execute o comando abaixo para obter as informações do dispositivo novamente e confirmar que a propriedade foi atualizada.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. Na saída do terminal de _serviço_, no componente `environmentalSensor`, você vê que o valor de brilho atualizado foi relatado. Observação: pode levar algum tempo para que o dispositivo conclua a atualização. Repita essa etapa até o dispositivo realmente processar a atualização da propriedade.
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>Invocar um comando

1. Vá para o terminal de _serviço_ e defina as seguintes variáveis para determinar qual comando invocar:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Use o seguinte comando para executar a amostra de serviço para invocar o comando:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. A saída no terminal de _serviço_ deve mostrar a seguinte confirmação:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Acesse o terminal do _dispositivo_ e você verá que o comando foi confirmado:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a uma solução de IoT. Para saber mais sobre como criar uma solução que interage com os dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Instruções: Conectar-se a um dispositivo e interagir com ele](howto-develop-solution.md)
