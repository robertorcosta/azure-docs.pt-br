---
title: Carregar arquivos dos dispositivos no Hub IoT do Azure com Python| Microsoft Docs
description: Como carregar arquivos de um dispositivo para a nuvem usando o SDK do dispositivo IoT do Azure para Python. Os arquivos carregados são armazenados em um contêiner de Azure Storage Blob.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 47fb7c615389e24322450ed1785aa7da9ec50db6
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759698"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Carregar arquivos do seu dispositivo para a nuvem com o IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este artigo mostra como usar os [recursos de carregamento de arquivos do Hub IoT](iot-hub-devguide-file-upload.md) para carregar um arquivo para [armazenamento de blobs do Azure](../storage/index.yml). Este tutorial mostra como:

* Fornece um contêiner de armazenamento com segurança para carregar um arquivo.

* Use o cliente do Python para carregar um arquivo por meio de seu Hub IoT.

A [telemetria Send de um dispositivo para um hub de IoT](quickstart-send-telemetry-python.md) demonstra a funcionalidade básica de mensagens dispositivo-nuvem do IoT Hub. No entanto, em alguns cenários você não pode mapear facilmente os dados que seus dispositivos enviam em mensagens relativamente menores do dispositivo para a nuvem que o Hub IoT aceita. Quando você precisar carregar arquivos de um dispositivo, ainda poderá usar a segurança e a confiabilidade do Hub IoT.

No final deste tutorial, você executa o aplicativo de console Python:

* **FileUpload.py**, que carrega um arquivo para o armazenamento usando o SDK do dispositivo Python.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Verifique se a porta 8883 está aberta no firewall. A amostra do dispositivo neste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um arquivo de um aplicativo de dispositivo

Nesta seção, você criará o aplicativo de dispositivo para carregar um arquivo no hub IoT.

1. No prompt de comando, execute o seguinte comando para instalar o pacote **de dispositivo azure-iot.** Você usa este pacote para coordenar o upload de arquivos com o seu hub IoT.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. No prompt de comando, execute o seguinte comando para instalar o pacote [**azure.storage.blob.**](https://pypi.org/project/azure-storage-blob/) Você usa este pacote para executar o upload do arquivo.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Crie um arquivo de teste que você carregará para o armazenamento blob.

1. Usando um editor de texto, crie um arquivo **FileUpload.py** em sua pasta de trabalho.

1. Adicione as instruções `import` e variáveis a seguir ao início do arquivo **FileUpload.py**.

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. Substitua `[Device Connection String]` pela cadeia de conexão do dispositivo do Hub IoT no seu arquivo. Substitua-o pelo `[Full path to local file]` caminho para o arquivo de teste que você criou ou qualquer arquivo em seu dispositivo que você deseja carregar.

1. Criar uma função para carregar o arquivo para o armazenamento blob:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Esta função analisa a *estrutura blob_info* passada para criar uma URL que ela usa para inicializar [um azure.storage.blob.BlobClient](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python). Em seguida, ele carrega seu arquivo para o armazenamento blob do Azure usando este cliente.

1. Adicione o seguinte código para conectar o cliente e carregar o arquivo:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    Esse código cria um **IoTHubDeviceClient** assíncrono e usa as seguintes APIs para gerenciar o upload de arquivos com seu hub ioT:

    * **get_storage_info_for_blob** obtém informações do seu hub de IoT sobre a conta de armazenamento vinculada que você criou anteriormente. Essas informações incluem o nome do host, nome do contêiner, nome blob e um token SAS. As informações de armazenamento são passadas para a função **store_blob** (criada na etapa anterior), para que o **BlobClient** nessa função possa autenticar com o armazenamento Do Zure. O método **get_storage_info_for_blob** também retorna um correlation_id, que é usado no método **notify_blob_upload_status.** A correlation_id é a maneira do IoT Hub de marcar em qual bolha você está trabalhando.

    * **notify_blob_upload_status** notifica o IoT Hub sobre o status da sua operação de armazenamento blob. Você passa o correlation_id obtido pelo método **get_storage_info_for_blob.** Ele é usado pelo IoT Hub para notificar qualquer serviço que possa estar ouvindo uma notificação sobre o status da tarefa de upload de arquivo.

1. Salve e feche o arquivo **UploadFile.py**.

## <a name="run-the-application"></a>Executar o aplicativo

Agora você está pronto para executar o aplicativo.

1. Em um prompt de comando na pasta de trabalho, execute o seguinte comando:

    ```cmd/sh
    python FileUpload.py
    ```

2. A captura de tela a seguir mostra a saída do aplicativo **FileUpload**:

    ![Saída do aplicativo simulated-device](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. Você pode usar o portal para exibir o arquivo carregado no contêiner de armazenamento configurado:

    ![Arquivo carregado](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar os recursos de carregamento de arquivo do Hub IoT para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar explorando os recursos e cenários do Hub IoT com os seguintes artigos:

* [Criar um Hub IoT de modo programático](iot-hub-rm-template-powershell.md)

* [Introdução ao SDK C](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Saiba mais sobre o Azure Blob Storage com os seguintes links:

* [Documentação de armazenamento do Azure Blob](https://docs.microsoft.com/azure/storage/blobs/)

* [Armazenamento do Azure Blob para documentação da API Python](https://docs.microsoft.com/python/api/overview/azure/storage-blob-readme?view=azure-python)
