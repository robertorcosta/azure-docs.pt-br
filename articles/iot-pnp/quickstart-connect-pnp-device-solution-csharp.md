---
title: Interagir com um dispositivo IoT Plug and Play Preview conectado à solução de IoT do Azure | Microsoft Docs
description: Use o C# (Java) para se conectar a um dispositivo IoT Plug and Play Preview que está conectado à sua solução de IoT do Azure e interagir com ele.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0953f68839217c1c75eb86f8399ce023f3863ab4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963964"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Início Rápido: Interagir com um dispositivo IoT Plug and Play Preview que está conectado à sua solução (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

O IoT Plug and Play Preview simplifica a IoT, permitindo que você interaja com as funcionalidades de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este início rápido mostra como usar o C# (com .NET) para se conectar a um dispositivo IoT Plug and Play que está conectado à sua solução e controlá-lo.

## <a name="prerequisites"></a>Prerequisites

Para concluir este início rápido, você precisa instalar o .NET Core (2.x.x ou 3.x.x) em seu computador de desenvolvimento. Você pode baixar sua versão preferida do SDK do .NET Core para várias plataformas em [Baixar o .NET Core](https://dotnet.microsoft.com/download/dotnet-core/).

Você pode verificar a versão do .NET que está em seu computador de desenvolvimento executando o seguinte comando em uma janela do terminal local: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o seguinte comando para obter a _cadeia de conexão do hub IoT_ para o seu hub (nota para usar mais tarde):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

Neste início rápido, você usará um sensor ambiental de exemplo que é escrito em C# como o dispositivo IoT Plug and Play. As seguintes instruções mostram como instalar e executar o dispositivo:

1. Abra uma janela de terminal no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub das [Amostras de IoT do Azure para C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) nesta localização:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Esta janela de terminal agora será usada como seu terminal de _dispositivo_. Acesse a pasta do repositório clonado e navegue até a pasta **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample**.

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Crie os pacotes necessários e execute o exemplo com o seguinte comando:

    ```cmd\sh
        dotnet run
    ```

1. Você vê mensagens dizendo que o dispositivo foi registrado com êxito e está aguardando atualizações da nuvem. Isso indica que o dispositivo agora está pronto para receber comandos e atualizações de propriedade e começou a enviar dados telemétricos para o hub. Não feche este terminal; você precisará dele mais tarde para confirmar se as amostras de serviço também funcionaram.

## <a name="run-the-sample-solution"></a>Executar a solução de exemplo

Neste início rápido, você usará uma solução de IoT de exemplo em C# para interagir com o dispositivo de exemplo.

1. Abra outra janela de terminal (este será o seu terminal de _serviço_). Acesse a pasta do repositório clonado e navegue até a pasta **/azure-iot-samples-csharp/digitaltwin/Samples/service**.

1. Configure a _cadeia de conexão do Hub IoT_ e a _ID do dispositivo_ para permitir que o serviço se conecte a ambos:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Ler uma propriedade

1. Quando você conectou o _dispositivo_ em seu terminal, você viu a seguinte mensagem indicando o status online:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Vá para o terminal de _serviço_ e use os seguintes comandos para executar a amostra de leitura de informações do dispositivo:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. Na saída do terminal de _serviço_, role até o componente `environmentalSensor`. Você verá que a propriedade `state`, que é usada para indicar se o dispositivo está online ou não, foi relatada como _true_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
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

1. Use os comandos a seguir para executar a amostra para atualizar a propriedade:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
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
            "value": true
          }
        }
      }
    }
    ```

1. Acesse o terminal do _dispositivo_ e você verá que o dispositivo recebeu a atualização:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Volte para o terminal de _serviço_ e execute os comandos abaixo para obter as informações do dispositivo novamente e confirmar que a propriedade foi atualizada.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. Na saída do terminal de _serviço_, no componente `environmentalSensor`, você vê que o valor de brilho atualizado foi relatado. Observação: pode levar algum tempo para que o dispositivo conclua a atualização. Repita essa etapa até o dispositivo realmente processar a atualização da propriedade.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>Invocar um comando

1. Vá para o terminal de _serviço_ e defina as seguintes variáveis para determinar qual comando invocar:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Use os seguintes comandos para executar a amostra para invocar o comando:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. A saída no terminal de _serviço_ deve mostrar a seguinte confirmação:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Acesse o terminal do _dispositivo_ e você verá que o comando foi confirmado:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a uma solução de IoT. Para saber mais sobre como criar uma solução que interage com os dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Instruções: Conectar-se a um dispositivo e interagir com ele](howto-develop-solution.md)
