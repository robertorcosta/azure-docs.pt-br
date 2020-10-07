---
title: Interagir com um dispositivo do IoT Plug and Play conectado à sua solução de IoT do Azure (Python) | Microsoft Docs
description: Use o Python para se conectar a um dispositivo do IoT Plug and Play conectado à sua solução de IoT do Azure e interagir com ele.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: be5ff3e863752dfc187bd91257425af5e8de85c4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574952"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-python"></a>Início Rápido: Interagir com um dispositivo IoT Plug and Play conectado à sua solução (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

O IoT Plug and Play simplifica a IoT ao permitir que você interaja com o modelo de um dispositivo sem conhecer a implementação subjacente dele. Este início rápido mostra como usar o Python para se conectar a um dispositivo IoT Plug and Play que está conectado à sua solução e controlá-lo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para concluir este início rápido, você precisa do Python 3.7 no seu computador de desenvolvimento. Baixe a versão mais recente recomendada para várias plataformas em [python.org](https://www.python.org/). Verifique a sua versão do Python com o seguinte comando:  

```cmd/sh
python --version
```

O pacote **azure-iot-device** é publicado como um PIP.

No ambiente Python local, instale o pacote da seguinte maneira:

```cmd/sh
pip install azure-iot-device
```

Instale o pacote **azure-iot-hub** executando o seguinte comando:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Para saber mais sobre a configuração do exemplo, confira o [leiame de exemplo](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Neste início rápido, você usará um dispositivo de termostato gravado em Python como o dispositivo IoT Plug and Play. Para executar o dispositivo de exemplo:

1. Abra uma janela de terminal em uma pasta de sua escolha. Execute o seguinte comando para clonar o repositório GitHub do [SDK do Python do IoT do Azure](https://github.com/Azure/azure-iot-sdk-python) nesta localização:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Esta janela de terminal é usada como o seu terminal de **dispositivo**. Acesse a pasta do repositório clonado e navegue até a pasta */azure-iot-sdk-python/azure-iot-device/samples/pnp*.

1. Execute o dispositivo de termostato de exemplo com o seguinte comando:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Você vê mensagens dizendo que o dispositivo enviou algumas informações e se reportou online. Essas mensagens indicam que o dispositivo começou a enviar dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade. Não feche este terminal; você precisará dele para confirmar se a amostra de serviço está funcionando.

## <a name="run-the-sample-solution"></a>Executar a solução de exemplo

Neste início rápido, você usará uma solução de IoT de exemplo em Python para interagir com o dispositivo de exemplo que acabou de configurar.

1. Abra outra janela de terminal para usar como o seu terminal de **serviço**. 

1. Navegue até a pasta */azure-iot-sdk-python/azure-iot-hub/samples* do repositório clonado do SDK do Python.

1. Lá existem quatro arquivos de exemplo que demonstram as operações com a classe do Gerenciador dos Gêmeos Digitais: *get_digital_twin_sample.py, update_digitial_twin_sample.py, invoke_command_sample.py e invoke_component_command_sample-.py*.  Estes exemplos mostram como usar cada API para interagir com dispositivos IoT Plug and Play:

### <a name="get-digital-twin"></a>Obter o gêmeo digital

Em [Configurar o ambiente para os inícios rápidos e os tutoriais do IoT Plug and Play](set-up-environment.md), você criou duas variáveis de ambiente para configurar o exemplo a ser conectado ao hub IoT e ao dispositivo:

* **IOTHUB_CONNECTION_STRING**: a cadeia de conexão do hub IoT anotada anteriormente.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

Use o seguinte comando no terminal do **serviço** para executar este exemplo:

```cmd/sh
python get_digital_twin_sample.py
```

A saída mostra o gêmeo digital do dispositivo e imprime a sua ID de modelo:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

O seguinte snippet mostra o código de exemplo de *get_digital_twin_sample.py*:

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

Este exemplo mostra como usar um *patch* para atualizar as propriedades por meio do seu gêmeo digital do dispositivo. O seguinte snippet do *update_digital_twin_sample.py* mostra como construir o patch:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Use o seguinte comando no terminal do **serviço** para executar este exemplo:

```cmd/sh
python update_digital_twin_sample.py
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

Para invocar um comando, execute o exemplo *invoke_command_sample.py*. Este exemplo mostra como invocar um comando em um dispositivo de termostato simples. Antes de executar este exemplo, defina as variáveis de ambiente `IOTHUB_COMMAND_NAME` e `IOTHUB_COMMAND_PAYLOAD` no terminal do **serviço**:

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

Use o comando seguir no terminal do **serviço** para executar o exemplo:
  
```cmd/sh
python invoke_command_sample.py
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

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a uma solução de IoT. Para saber mais sobre os modelos de dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Guia do desenvolvedor de modelagem do IoT Plug and Play](concepts-developer-guide-device-csharp.md)
