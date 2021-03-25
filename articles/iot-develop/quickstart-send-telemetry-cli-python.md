---
title: Guia de início rápido Enviar telemetria do dispositivo para o Hub IoT do Azure (Python)
description: Neste guia início rápido, use o SDK do Dispositivo do Hub IoT do Azure para Python para enviar telemetria de um dispositivo a um hub IoT.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: d73f8eeb7b69440f8db67d0b95b40ed6258ee8e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201780"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>Guia de início rápido: Enviar telemetria de um dispositivo para um hub IoT do Azure (Python)

**Aplica-se a**: [desenvolvimento de aplicativos do dispositivo](about-iot-develop.md#device-application-development)

Neste guia de início rápido, aprenda um fluxo de trabalho básico de desenvolvimento de aplicativos do dispositivo IoT. Use a CLI do Azure para criar um hub IoT do Azure e um dispositivo e use o SDK do Python de IoT do Azure para criar um dispositivo cliente simulado e enviar telemetria para o hub. 

## <a name="prerequisites"></a>Pré-requisitos
- Se você não tiver uma assinatura do Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- CLI do Azure. Você pode executar todos os comandos neste guia de início rápido usando o Azure Cloud Shell, um shell da CLI interativa que é executado em seu navegador. Se você usar o Cloud Shell, não precisará instalar nada. Se você preferir usar a CLI localmente, este guia de início rápido exigirá a CLI do Azure versão 2.0.76 ou posterior. Execute az --version para localizar a versão. Para instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
- [Python 3.7+](https://www.python.org/downloads/). Para outras versões do Python com suporte, confira [Recursos do Dispositivo IoT do Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Para garantir que sua versão do Python esteja atualizada, execute `python --version`. Se o Python 2 e o Python 3 estiverem instalados e você estiver usando um ambiente do Python 3, instale todas as bibliotecas usando `pip3`. Isso garante que as bibliotecas sejam instaladas no seu runtime do Python 3.
    > [!IMPORTANT]
    > No instalador do Python, selecione a opção para **Adicionar Python ao PATH**. Se o Python 3.7 ou superior já estiver instalado, confirme que adicionou a pasta de instalação do Python à variável de ambiente `PATH`.

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Usar o SDK do Python para enviar mensagens
Nesta seção, você usará o SDK do Python para enviar mensagens do seu dispositivo simulado para o hub IoT.

1. Abra uma nova janela de terminal. Você usará esse terminal para instalar o SDK do Python e trabalhar com o código de exemplo do Python. Agora você deve ter dois terminais abertos: aquele que você acabou de abrir para trabalhar com o Python e o shell da CLI que você usou nas seções anteriores para inserir os comandos da CLI do Azure.       

1. Copie os [exemplos de dispositivo do SDK do Python de IoT do Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) para o computador local:

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

    e navegue até o diretório *azure-iot-sdk-python/azure-iot-device/samples*:

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Instale o SDK do Python de IoT do Azure:

    ```console
    pip install azure-iot-device
    ```
1. Defina a Cadeia de Conexão do Dispositivo como uma variável de ambiente chamada `IOTHUB_DEVICE_CONNECTION_STRING`. Essa é a cadeia de caracteres que você obteve na seção anterior depois de criar o dispositivo Python simulado.

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > Para o Windows CMD, não há aspas ao redor da cadeia de conexão.

    **Linux (bash)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. No shell da CLI aberta, execute o comando [az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) para iniciar o monitoramento de eventos no seu dispositivo IoT simulado.  As mensagens de evento serão impressas no terminal à medida que chegarem.

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. No terminal do Python, execute o código para o arquivo de exemplo instalado *simple_send_message.py*. Esse código acessa o dispositivo IoT simulado e envia uma mensagem ao hub IoT.

    Para executar o exemplo de Python no terminal:
    ```console
    python ./simple_send_message.py
    ```

    Opcionalmente, você também pode executar o código Python por meio do exemplo no seu Python IDE:
    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient


    async def main():
        # Fetch the connection string from an environment variable
        conn_str = os.getenv("IOTHUB_DEVICE_CONNECTION_STRING")

        # Create instance of the device client using the authentication provider
        device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

        # Connect the device client.
        await device_client.connect()

        # Send a single message
        print("Sending message...")
        await device_client.send_message("This is a message that is being sent")
        print("Message successfully sent!")

        # finally, disconnect
        await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
    ```

Como o código Python envia uma mensagem do seu dispositivo para o hub IoT, a mensagem é exibida no shell da CLI que está monitorando os eventos:

```output
Starting event monitor, use ctrl-c to stop...
event:
origin: <your Device name>
payload: This is a message that is being sent
```

Agora, o seu dispositivo está conectado com segurança e está enviando telemetria ao Hub IoT do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos
Se você não precisar mais dos recursos do Azure criados neste guia de início rápido, poderá usar a CLI do Azure para excluí-los.

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos.

Para excluir um grupo de recursos por nome:
1. Execute o comando [az group delete](/cli/azure/group#az-group-delete). Isso removerá o grupo de recursos, o Hub IoT e o registro de dispositivo que você criou.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Execute o comando [az group list](/cli/azure/group#az-group-list) para confirmar se o grupo de recursos foi excluído.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você aprendeu um fluxo de trabalho básico do aplicativo de IoT do Azure para conectar com segurança um dispositivo à nuvem e enviar telemetria do dispositivo para nuvem. Você usou a CLI do Azure para criar um hub IoT e um dispositivo e usou o SDK do Python de IoT do Azure para criar um dispositivo simulado e enviar telemetria para o hub. 

Como próxima etapa, explore o SDK do Python de IoT do Azure por meio de exemplos de aplicativo.

- [Exemplos Assíncronos](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): esse diretório contém exemplos de Python assíncronos para cenários adicionais do Hub IoT.
- [Exemplos Síncronos](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): esse diretório contém exemplos de Python para uso com o Python 2.7 ou em cenários de compatibilidade síncrona com o Python 3.5 +
- [Exemplos do IoT Edge](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): esse diretório contém exemplos de Python para trabalhar com módulos do Edge e dispositivos downstream.