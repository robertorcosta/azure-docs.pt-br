---
title: Tutorial – Conectar um aplicativo cliente Python genérico ao Azure IoT Central | Microsoft Docs
description: Este tutorial mostra a você, desenvolvedor de dispositivos, como conectar um dispositivo que executa um aplicativo cliente Python ao seu aplicativo do Azure IoT Central. Você criará um modelo de dispositivo importando um modelo de funcionalidade do dispositivo e adicionará exibições que permitem interagir com um dispositivo conectado
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- devx-track-python
- device-developer
ms.openlocfilehash: 1be7087e99ca2e4dc605a8d1c6b9821567aecfef
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90968138"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-python"></a>Tutorial: Criar e conectar um aplicativo cliente ao seu aplicativo do Azure IoT Central (Python)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Este artigo se aplica a construtores de solução e desenvolvedores de dispositivos.*

Este tutorial mostra a você, desenvolvedor de dispositivos, como conectar um aplicativo cliente Python ao seu aplicativo do Azure IoT Central. O aplicativo Python simula o comportamento de um dispositivo de sensor ambiental. Use um exemplo de _modelo de funcionalidade do dispositivo_ a fim de criar um _modelo de dispositivo_ no IoT Central. Adicione exibições ao modelo de dispositivo para permitir que um operador interaja com um dispositivo.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Importar um modelo de funcionalidade do dispositivo para criar um modelo de dispositivo.
> * Adicionar exibições padrão e personalizadas a um modelo de dispositivo.
> * Publicar um modelo de dispositivo e adicionar um dispositivo real ao seu aplicativo do IoT Central.
> * Criar e executar o código do dispositivo Python e ver se ele se conecta ao seu aplicativo do IoT Central.
> * Veja a telemetria simulada enviada do dispositivo.
> * Usar uma exibição para gerenciar as propriedades do dispositivo.
> * Chame comandos síncronos e assíncronos para controlar o dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa do seguinte:

* Um aplicativo do Azure IoT Central criado usando o modelo de **Aplicativo personalizado**. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md). O aplicativo precisa ter sido criado em ou após 14/07/2020.
* Um computador de desenvolvimento com o [Python](https://www.python.org/) versão 3.7 ou posterior instalado. É possível executar `python3 --version` na linha de comando para verificar a versão. O Python está disponível para uma ampla variedade de sistemas operacionais. As instruções deste tutorial pressupõem que você esteja executando o comando **python3** no prompt de comando do Windows.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-python-application"></a>Criar um aplicativo Python

As etapas a seguir mostram como criar um aplicativo cliente Python que implementa o dispositivo real adicionado ao aplicativo. O aplicativo Python simula o comportamento de um dispositivo real.

1. No ambiente de linha de comando, navegue até a pasta `environmental-sensor` criada anteriormente.

1. Para instalar as bibliotecas necessárias, execute os seguintes comandos:

    ```cmd
    pip install azure-iot-device
    ```

1. Crie um arquivo chamado **environmental_sensor.py** na pasta `environmental-sensor`.

1. Adicione as seguintes instruções `import` ao início do arquivo **environmental_sensor.py**:

    ```python
    import asyncio
    import os
    import json
    import datetime
    import random

    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import MethodResponse
    from azure.iot.device import Message
    ```

1. Adicione as seguintes funções `main` assíncronas e declarações de variáveis ao arquivo:

    ```python
    async def main():
        # In a production environment, don't store
        # connection information in the code.
        provisioning_host = 'global.azure-devices-provisioning.net'
        id_scope = '{your Scope ID}'
        registration_id = '{your Device ID}'
        symmetric_key = '{your Primary Key}'
  
        delay = 2
  
        # All the remaining code is nested within this main function

    if __name__ == '__main__':
        asyncio.run(main())
    ```

    Atualize os espaços reservados `{your Scope ID}`, `{your Device ID}` e `{your Primary Key}` com os valores anotados anteriormente. Em um aplicativo real, não codifique essas informações no aplicativo.

    Todas as definições de função e o código a seguir são aninhados dentro da função `main`.

1. Adicione as duas funções a seguir dentro da função `main` para registrar o dispositivo e conectá-lo ao seu aplicativo do IoT Central. O registro usa o Serviço de Provisionamento de Dispositivos do Azure:

    ```python
        async def register_device():
            provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
                provisioning_host=provisioning_host,
                registration_id=registration_id,
                id_scope=id_scope,
                symmetric_key=symmetric_key,
            )

            registration_result = await provisioning_device_client.register()

            print(f'Registration result: {registration_result.status}')

            return registration_result
  
        async def connect_device():
            device_client = None
            try:
                registration_result = await register_device()
                if registration_result.status == 'assigned':
                    device_client = IoTHubDeviceClient.create_from_symmetric_key(
                        symmetric_key=symmetric_key,
                        hostname=registration_result.registration_state.assigned_hub,
                        device_id=registration_result.registration_state.device_id
                    )
                    # Connect the client.
                    await device_client.connect()
                    print('Device connected successfully')
            finally:
                return device_client
    ```

1. Adicione a seguinte função dentro da função `main` para enviar a telemetria ao aplicativo do IoT Central:

    ```python
        async def send_telemetry():
            print(f'Sending telemetry from the provisioned device every {delay} seconds')
            while True:
                temp = random.randrange(1, 75)
                humid = random.randrange(30, 99)
                payload = json.dumps(
                    {
                        'temp': temp,
                        'humid': humid
                    })
                msg = Message(payload)
                await device_client.send_message(msg, )
                print(f'Sent message: {msg}')
                await asyncio.sleep(delay)
    ```

    Os nomes dos itens de telemetria (`temp` e `humid`) devem corresponder aos nomes usados no modelo de dispositivo.

1. Adicione as seguintes funções dentro da função `main` para manipular comandos chamados de seu aplicativo do IoT Central:

    ```python
        async def blink_command(request):
            print('Received synchronous call to blink')
            response = MethodResponse.create_from_method_request(
                request,
                status = 200,
                payload = {'description': f'Blinking LED every {request.payload} seconds'}
            )
            await device_client.send_method_response(response)  # send response
            print(f'Blinking LED every {request.payload} seconds')

        async def diagnostics_command(request):
            print('Starting asynchronous diagnostics run...')
            response = MethodResponse.create_from_method_request(
                request,
                status = 202
            )
            await device_client.send_method_response(response)  # send response
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Generating diagnostics...')
            await asyncio.sleep(2)
            print('Sending property update to confirm command completion')
            await device_client.patch_twin_reported_properties(
                {
                    'rundiagnostics':
                    {
                        'value': f'Diagnostics run complete at {datetime.datetime.today()}.'
                    }
                })

        async def turnon_command(request):
            print('Turning on the LED')
            response = MethodResponse.create_from_method_request(
                request, status = 200
            )
            await device_client.send_method_response(response)  # send response

        async def turnoff_command(request):
            print('Turning off the LED')
            response = MethodResponse.create_from_method_request(
                request, status = 200
            )
            await device_client.send_method_response(response)  # send response

        commands = {
            'blink': blink_command,
            'rundiagnostics': diagnostics_command,
            'turnon': turnon_command,
            'turnoff': turnoff_command,
        }

        # Define behavior for handling commands
        async def command_listener():
            while True:
                method_request = await device_client.receive_method_request()  # Wait for commands
                await commands[method_request.name](method_request)
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

1. Adicione as funções a seguir dentro da função `main` para manipular atualizações de propriedades enviadas do seu aplicativo do IoT Central. A mensagem que o dispositivo envia em resposta à [atualização de propriedade gravável](concepts-telemetry-properties-commands.md#writeable-property-types) precisa incluir os campos `av` e `ac`. O campo `ad` é opcional:

    ```python
        async def name_setting(value, version):
            await asyncio.sleep(1)
            print(f'Setting name value {value} - {version}')
            await device_client.patch_twin_reported_properties(
                {
                    'name' :
                    {
                        'value': value,
                        'ad': 'completed',
                        'ac': 200,
                        'av': version
                    }
                })
  
        async def brightness_setting(value, version):
            await asyncio.sleep(5)
            print(f'Setting brightness value {value} - {version}')
            await device_client.patch_twin_reported_properties(
                {
                    'brightness' :
                    {
                        'value': value,
                        'ad': 'completed',
                        'ac': 200,
                        'av': version
                    }
                })
  
        settings = {
            'name': name_setting,
            'brightness': brightness_setting
        }

        # define behavior for receiving a twin patch
        async def twin_patch_listener():
            while True:
                patch = await device_client.receive_twin_desired_properties_patch() # blocking
                to_update = patch.keys() & settings.keys()
                await asyncio.gather(
                    *[settings[setting](patch[setting], patch['$version']) for setting in to_update]
                )
    ```

    Quando o operador define uma propriedade gravável no aplicativo IoT Central, o aplicativo usa uma propriedade desejada do dispositivo gêmeo para enviar o valor para o dispositivo. Em seguida, o dispositivo responde usando uma propriedade relatada do dispositivo gêmeo. Quando o IoT Central recebe o valor da propriedade relatada, ele atualiza a exibição da propriedade com um status **sincronizado**.

    Os nomes das propriedades (`name` e `brightness`) devem corresponder aos nomes usados no modelo de dispositivo.

1. Adicione as seguintes funções dentro da função `main` para controlar o aplicativo:

    ```python
        # Define behavior for halting the application
        def stdin_listener():
            while True:
                selection = input('Press Q to quit\n')
                if selection == 'Q' or selection == 'q':
                    print('Quitting...')
                    break
  
        device_client = await connect_device()
  
        if device_client is not None and device_client.connected:
            print('Send reported properties on startup')
            await device_client.patch_twin_reported_properties(
                {
                    'state': 'true',
                    'processorArchitecture': 'ARM',
                    'swVersion': '1.0.0'
                })
            tasks = asyncio.gather(
                send_telemetry(),
                command_listener(),
                twin_patch_listener(),
            )

            # Run the stdin listener in the event loop
            loop = asyncio.get_running_loop()
            user_finished = loop.run_in_executor(None, stdin_listener)

            # Wait for user to indicate they are done listening for method calls
            await user_finished

            # Cancel tasks
            tasks.add_done_callback(lambda r: r.exception())
            tasks.cancel()
            await device_client.disconnect()
  
        else:
            print('Device could not connect')
    ```

1. Salve o arquivo **environmental_sensor.py**.

## <a name="run-your-python-application"></a>Executar seu aplicativo Python

Para iniciar o aplicativo cliente do dispositivo, execute o seguinte comando no ambiente de linha de comando:

```cmd
python3 environmental_sensor.py
```

Você poderá ver se o dispositivo se conecta ao aplicativo do Azure IoT Central e começa a enviar a telemetria:

![Executar o aplicativo cliente](media/tutorial-connect-device-python/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

Você pode ver como o dispositivo responde a comandos e a atualizações de propriedade:

![Observe o aplicativo cliente](media/tutorial-connect-device-python/run-application-2.png)

## <a name="view-raw-data"></a>Exibir dados brutos

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Próximas etapas

Se você preferir continuar com o conjunto de tutoriais do IoT Central e saber mais sobre como criar uma solução de IoT Central, confira:

> [!div class="nextstepaction"]
> [Criar um modelo de dispositivo de gateway](./tutorial-define-gateway-device-type.md)

Como desenvolvedor de dispositivos, agora que você aprendeu as noções básicas de como criar um dispositivo usando Python, algumas próximas etapas sugeridas são:

* Leia [O que são modelos de dispositivo?](./concepts-device-templates.md) para saber mais sobre a função dos modelos de dispositivo quando você implementa o código do dispositivo.
* Leia [Conectar-se ao Azure IoT Central](./concepts-get-connected.md) para saber mais sobre como registrar dispositivos com o IoT Central e como o IoT Central protege as conexões do dispositivo.