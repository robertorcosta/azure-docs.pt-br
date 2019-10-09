---
title: Introdução aos dispositivos gêmeos do Hub IoT do Azure (Python) | Microsoft Docs
description: Como usar dispositivos gêmeos do Hub IoT do Azure para adicionar marcas e usar uma consulta do Hub IoT. Use os SDKs do IoT do Azure para Python para implementar o aplicativo de dispositivo simulado e um aplicativo de serviço que adiciona as marcações e executa a consulta do Hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: 729371ede8df3dc21f70bc5742de52f2be874507
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030008"
---
# <a name="get-started-with-device-twins-python"></a>Introdução aos dispositivos gêmeos (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ao final deste tutorial, você terá dois aplicativos de console do Python:

* **AddTagsAndQuery.py**, um aplicativo de back-end do Python, que adiciona marcações e consulta os dispositivos gêmeos.

* **ReportConnectivity.py**, um aplicativo do Python, que simula um dispositivo que se conecta ao hub IoT com a identidade do dispositivo criada anteriormente e relata sua condição de conectividade.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço

Nesta seção, você criará um aplicativo de console do Python que adiciona metadados de local para o dispositivo de entrelaçamento associado à **{Device ID}** . Em seguida, ele consulta os dispositivos gêmeos armazenados no hub IoT selecionando os dispositivos localizados em Redmond e, depois, aqueles que relatam uma conexão celular.

1. No diretório de trabalho, abra um prompt de comando e instale o **SDK do serviço de Hub IOT do Azure para Python**.

   ```cmd/sh
   pip install azure-iothub-service-client
   ```

   > [!NOTE]
   > O pacote Pip para Azure-iothub-Service-Client está disponível no momento apenas para o sistema operacional Windows. Para o Linux/Mac OS, consulte as seções específicas do Linux e do Mac OS na postagem [preparar seu ambiente de desenvolvimento para o Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Usando um editor de texto, crie um novo arquivo **AddTagsAndQuery.py**.

3. Adicione o código a seguir para importar os módulos necessários do SDK de serviço:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Adicione os códigos a seguir. Substitua `[IoTHub Connection String]` pela cadeia de conexão do Hub IoT que você copiou em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string). Substitua `[Device Id]` pela ID do dispositivo que você registrou em [registrar um novo dispositivo no Hub IOT](#register-a-new-device-in-the-iot-hub).
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Adicione o seguinte código ao arquivo **AddTagsAndQuery.py**:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    O objeto **Registry** expõe todos os métodos necessários para interagir com gêmeos de dispositivo do serviço. O código primeiro inicializa o objeto **Registry**, em seguida, atualiza o dispositivo gêmeo para **deviceId** e, por fim, executa duas consultas. A primeira seleciona somente os dispositivos gêmeos de dispositivos localizados na fábrica de **Redmond43**, enquanto a segunda refina a consulta para selecionar somente os dispositivos que também estão conectados por meio da rede de celular.

6. Adicione o seguinte código ao final de **AddTagsAndQuery.py** para implementar a função **iothub_service_sample_run**:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Execute o aplicativo com:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Você deve ver um dispositivo nos resultados da consulta que pergunta sobre todos os dispositivos localizados em **Redmond43**, e nenhum para a consulta que restringe os resultados para dispositivos que usam uma rede de celular.

    ![primeira consulta mostrando todos os dispositivos em Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

Na seção seguinte, você cria um aplicativo de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo do dispositivo

Nesta seção, você cria um aplicativo de console do Python que se conecta ao seu hub como **{Device ID}** e, em seguida, atualiza suas propriedades relatadas do seu dispositivo para conter as informações que ele está conectado usando uma rede de celular.

1. Em um prompt de comando em seu diretório de trabalho, instale o **SDK do dispositivo do Hub IOT do Azure para Python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Usando um editor de texto, crie um novo arquivo **ReportConnectivity.py**.

3. Adicione o seguinte código para importar os módulos necessários do SDK do dispositivo:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Adicione os códigos a seguir. Substitua o valor de espaço reservado `[IoTHub Device Connection String]` pela cadeia de conexão do dispositivo que você copiou no [registro de um novo dispositivo no Hub IOT](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Adicione o seguinte código ao arquivo **ReportConnectivity.py** para implementar a funcionalidade de dispositivo gêmeo:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```

    O objeto **Client** expõe todos os métodos necessários para você interagir com dispositivos gêmeos a partir do dispositivo. O código anterior, depois de inicializar o objeto **Client**, recupera o dispositivo gêmeo para o dispositivo e atualiza suas propriedades relatadas com as informações de conectividade.

6. Adicione o seguinte código ao final de **ReportConnectivity.py** para implementar a função **iothub_client_sample_run**:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Execute o aplicativo do dispositivo:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Você deve ver a confirmação de que os dispositivos gêmeos foram atualizados.

    ![atualizar gêmeos](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Agora que o dispositivo divulgou suas informações de conectividade, ele deve aparecer em ambas as consultas. Volte e execute as consultas novamente:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Desta vez, o **{Device ID}** deve aparecer nos dois resultados da consulta.

    ![segunda consulta](./media/iot-hub-python-twin-getstarted/service-2.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você adicionou os metadados do dispositivo como marcações de um aplicativo de back-end e criou um aplicativo de dispositivo simulado para relatar informações de conectividade no dispositivo gêmeo. Você também aprendeu a consultar essas informações usando o registro.

Veja os recursos a seguir para saber como:

* Envie telemetria de dispositivos com o tutorial [Introdução ao Hub IoT](quickstart-send-telemetry-python.md).

* Configure dispositivos usando as propriedades desejadas do dispositivo ' s ' com o tutorial [usar propriedades desejadas para configurar dispositivos](tutorial-device-twins.md) .

* Controlar dispositivos interativamente (como ativar um ventilador de um aplicativo controlado pelo usuário), com o tutorial [usar métodos diretos](quickstart-control-device-python.md) .
