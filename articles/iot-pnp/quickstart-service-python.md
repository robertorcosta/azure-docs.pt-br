---
title: Interagir com um dispositivo de versão prévia do IoT Plug and Play conectado à sua solução de IoT do Azure (Python) | Microsoft Docs
description: Use o Python para se conectar a um dispositivo de versão prévia do IoT Plug and Play que está conectado à sua solução de IoT do Azure e interagir com ele.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e3f00bb601cce17721c5247941588be1c2de788d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352560"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-python"></a>Início Rápido: Interagir com um dispositivo de versão prévia do IoT Plug and Play que está conectado à sua solução (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

A versão prévia do IoT Plug and Play simplifica a IoT, permitindo que você interaja com um modelo de dispositivo sem conhecer a implementação do dispositivo subjacente. Este início rápido mostra como usar o Python para se conectar a um dispositivo IoT Plug and Play que está conectado à sua solução e controlá-lo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa do Python 3.7 no seu computador de desenvolvimento. Baixe a versão mais recente recomendada para várias plataformas em [python.org](https://www.python.org/). Verifique a sua versão do Python com o seguinte comando:  

```cmd/sh
python --version
```

Instale o [pacote de versão prévia do SDK do serviço Python](https://pypi.org/project/azure-iot-hub/2.2.1rc0/) executando o seguinte comando:

```cmd/sh
pip3 install azure-iot-hub==2.2.1rc0
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

Neste início rápido, você usará um dispositivo de termostato gravado em Python como o dispositivo IoT Plug and Play. Para executar o dispositivo de exemplo:

1. Abra uma janela de terminal em uma pasta de sua escolha. Execute o seguinte comando para clonar o repositório GitHub do [SDK do Python do IoT do Azure](https://github.com/Azure/azure-iot-sdk-python) nesta localização:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Esta janela de terminal é usada como o seu terminal de **dispositivo**. Acesse a pasta do repositório clonado e navegue até a pasta */azure-iot-sdk-python/azure-iot-device/samples/pnp*.

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Execute o dispositivo de termostato de exemplo com o seguinte comando:

    ```cmd/sh
    python pnp_thermostat.py
    ```

1. Você vê mensagens dizendo que o dispositivo enviou algumas informações e se reportou online. Essas mensagens indicam que o dispositivo começou a enviar dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade. Não feche este terminal; você precisará dele para confirmar se a amostra de serviço está funcionando.

## <a name="run-the-sample-solution"></a>Executar a solução de exemplo

Neste início rápido, você usará uma solução de IoT de exemplo em Python para interagir com o dispositivo de exemplo que acabou de configurar.

1. Abra outra janela de terminal para usar como o seu terminal de **serviço**. O SDK do serviço está em versão prévia, portanto, você precisa clonar os exemplos de um [branch de versão prévia do SDK do Python](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python -b pnp-preview-refresh
    ```

1. Acesse a pasta do branch do repositório clonado e navegue até a pasta */azure-iot-sdk-python/azure-iot-hub/samples*.

1. Configure as variáveis de ambiente para a ID do dispositivo e a _cadeia de conexão do Hub IoT_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

1. Na pasta de exemplos, há quatro arquivos de exemplo com um prefixo `pnp`. Estes exemplos mostram como usar cada API para interagir com dispositivos IoT Plug and Play:

### <a name="get-digital-twin"></a>Obter o gêmeo digital

Use o seguinte comando no terminal do **serviço** para executar este exemplo:

```cmd/sh
python pnp_get_digital_twin_sample.py
```

A saída mostra o gêmeo digital do dispositivo e imprime a sua ID de modelo:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

O seguinte snippet mostra o código de exemplo de *pnp_get_digital_twin_sample.py*:

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>Atualizar um gêmeo digital

Este exemplo mostra como usar um *patch* para atualizar as propriedades por meio do seu gêmeo digital do dispositivo. O seguinte snippet do *pnp_update_digital_twin_sample.py* mostra como construir o patch:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Use o seguinte comando no terminal do **serviço** para executar este exemplo:

```cmd/sh
python pnp_update_digital_twin_sample.py
```

Você pode verificar se a atualização é aplicada no terminal do **dispositivo** que mostra a saída seguir:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

O terminal do **serviço** confirma que o patch foi bem-sucedido:

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>Invocar um comando

Para invocar um comando, execute o exemplo *pnp_invoke_command_sample.py*. Este exemplo mostra como invocar um comando em um dispositivo de termostato simples. Antes de executar este exemplo, defina as variáveis de ambiente `IOTHUB_COMMAND_NAME` e `IOTHUB_COMMAND_PAYLOAD` no terminal do **serviço**:

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

Use o comando seguir no terminal do **serviço** para executar o exemplo:
  
```cmd/sh
python pnp_invoke_command_sample.py
```

O terminal do **serviço** mostra uma mensagem de confirmação do dispositivo:

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

No terminal do **dispositivo**, você vê que o dispositivo recebe o comando:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a uma solução de IoT. Para saber mais sobre os modelos de dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Guia do desenvolvedor de modelagem de versão prévia do IoT Plug and Play](concepts-developer-guide.md)
