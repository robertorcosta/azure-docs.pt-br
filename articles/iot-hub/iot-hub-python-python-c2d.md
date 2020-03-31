---
title: Mensagens da nuvem para dispositivo com o Hub IoT do Azure (Python) | Microsoft Docs
description: Como enviar mensagens da nuvem para um dispositivo em um hub IoT do Azure usando os SDKs de IoT do Azure para Python. Modifique um aplicativo de dispositivo simulado para receber mensagens da nuvem para o dispositivo e modificar um aplicativo de back-end para enviá-las.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110427"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Enviar mensagens da nuvem para dispositivo com o Hub IoT (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. A [telemetria Send de um dispositivo para um hub de IoT](quickstart-send-telemetry-python.md) mostra como criar um hub de IoT, provisionar uma identidade de dispositivo nele e codificar um aplicativo de dispositivo simulado que envia mensagens dispositivo-nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial se baseia em [Enviar telemetria de um dispositivo para um hub de IoT](quickstart-send-telemetry-python.md). Ele mostra como:

* Da sua solução de back-end, envie mensagens da nuvem para o dispositivo em um único dispositivo por meio do Hub IoT.

* Receber mensagens da nuvem para o dispositivo em um dispositivo.

* A partir do back-end da solução, solicite o reconhecimento de entrega *(feedback)* para mensagens enviadas a um dispositivo do IoT Hub.

Você pode encontrar mais informações sobre mensagens de nuvem para dispositivo no guia de desenvolvedores do [IoT Hub](iot-hub-devguide-messaging.md).

Ao final deste tutorial, você executará dois aplicativos de console do Python:

* **SimulatedDevice.py**, uma versão modificada do aplicativo criado na [telemetria Send de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md), que se conecta ao seu hub IoT e recebe mensagens nuvem-dispositivo.

* **SendCloudToDeviceMessage.py**, que envia uma mensagem nuvem-para-dispositivo para o aplicativo de dispositivo simulado através do IoT Hub, e depois recebe seu reconhecimento de entrega.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Verifique se a porta 8883 está aberta no firewall. A amostra do dispositivo neste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens no aplicativo do dispositivo simulado

Nesta seção, você cria um aplicativo de console do Python para simular o dispositivo e receber mensagens da nuvem para dispositivo do hub IoT.

1. Usando um editor de texto, crie um arquivo **SimulatedDevice.py**.

2. Adicione as seguintes instruções `import` e variáveis ao início do arquivo **SimulatedDevice.py**:

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Adicione o código a seguir ao arquivo **SimulatedDevice.py**. Substitua o valor de espaço reservado "{deviceConnectionString}" pela seqüência de conexões do dispositivo para o dispositivo criado na [telemetria Enviar de um dispositivo para um centro ioT](quickstart-send-telemetry-python.md) quickstart:

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Adicione a seguinte função para imprimir as mensagens recebidas para o console:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. Adicione o seguinte código para inicializar o cliente e aguarde para receber a mensagem da nuvem para o dispositivo:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Adicione a seguinte função principal:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Salve e feche o arquivo **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a seqüência de conexão do hub IoT

Neste artigo, você cria um serviço de back-end para enviar mensagens nuvem-dispositivo através do hub ioT que você criou no [Enviar telemetria de um dispositivo para um hub de IoT](quickstart-send-telemetry-python.md). Para enviar mensagens em nuvem para dispositivo, seu serviço precisa da permissão de conexão de **serviço.** Por padrão, todo IoT Hub é criado com uma política de acesso compartilhado chamada **service** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem da nuvem para o dispositivo

Nesta seção, você cria um aplicativo do console do Python que envia mensagens da nuvem para dispositivo ao aplicativo de dispositivo simulado. Você precisa do ID do dispositivo que você adicionou na [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md) quickstart. Você também precisa da seqüência de conexão de hub IoT que você copiou anteriormente em [Obter a seqüência de conexão de hub IoT](#get-the-iot-hub-connection-string).

1. Usando um editor de texto, crie um arquivo **SendCloudToDeviceMessage.py**.

2. Adicione as seguintes instruções `import` e variáveis ao início do arquivo **SendCloudToDeviceMessage.py**:

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. Adicione o código a seguir ao arquivo **SendCloudToDeviceMessage.py**. Substitua os valores de espaço reservado "{iot hub connection string}" e "{device id}" por uma seqüência de conexões de hub IoT e iD do dispositivo que você observou anteriormente:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Adicione a seguinte função para imprimir mensagens de comentários para o console:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Adicione o seguinte código para enviar uma mensagem ao dispositivo e lidar com a mensagem de comentário quando o dispositivo reconhecer a mensagem da nuvem para o dispositivo:

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. Adicione a seguinte função principal:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Salve e feche o arquivo **SendCloudToDeviceMessage.py**.

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Abra um prompt de comando e instale o **SDK do Dispositivo Hub IoT do Azure para Python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. No prompt de comando, execute o seguinte comando para escutar mensagens da nuvem para dispositivo:

    ```shell
    python SimulatedDevice.py
    ```

    ![Executar um aplicativo de dispositivo simulado](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Abra um novo prompt de comando e instale o **SDK de Serviço do Hub IoT do Azure para Python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. No prompt de comando, execute o seguinte comando para enviar uma mensagem da nuvem para dispositivo e aguarde os comentários da mensagem:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Execute o aplicativo para enviar o comando da nuvem para o dispositivo](./media/iot-hub-python-python-c2d/send-command.png)

5. Observe a mensagem recebida pelo dispositivo.

    ![Mensagem recebida](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial você aprendeu a enviar e receber mensagens da nuvem para o dispositivo.

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Acelerador de solução de Monitoramento Remoto do Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de desenvolvedores do [IoT Hub](iot-hub-devguide.md).
