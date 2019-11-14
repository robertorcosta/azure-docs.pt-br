---
title: Criar e testar um dispositivo da versão prévia do IoT Plug and Play | Microsoft Docs
description: Como desenvolvedor de dispositivos, saiba como usar o VS Code para criar e testar um modelo de funcionalidade do dispositivo para um dispositivo IoT Plug and Play Preview.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: b7b9cd1040accda4d39af4d0a18940b56a45f929
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569893"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Tutorial: Criar e testar um modelo de funcionalidade do dispositivo usando o Visual Studio Code

Este tutorial mostra a você, como desenvolvedor de dispositivos, como usar o Visual Studio Code para criar um _modelo de funcionalidade do dispositivo_. Você pode usar o modelo para gerar um código de esqueleto para ser executado em um dispositivo que se conecta a uma instância do Hub IoT do Azure na nuvem.

A seção deste tutorial que descreve como criar o código de esqueleto gerado pressupõe que você esteja usando o Windows.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um modelo de funcionalidade do dispositivo
> * Gerar o código de esqueleto do dispositivo do modelo
> * Implementar os stubs no código gerado
> * Executar o código para testar as interações com um hub IoT

## <a name="prerequisites"></a>Pré-requisitos

Para trabalhar com o modelo de funcionalidade do dispositivo neste tutorial, você precisará do seguinte:

* [Visual Studio Code](https://code.visualstudio.com/download): O VS Code está disponível para várias plataformas
* Pacote de extensão [Azure IoT Tools para VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools). Use as seguintes etapas para instalar o pacote de extensão no VS Code:

    1. No VS Code, selecione a guia **Extensões**.
    1. Pesquise por **Azure IoT Tools**.
    1. Selecione **Instalar**.

Para criar o código C gerado no Windows neste tutorial, você precisará do seguinte:

* [Ferramentas de Build para o Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) com **Ferramentas de build de C++** e cargas de trabalho do **Componente do gerenciador de pacotes NuGet**. Ou, se você já tiver, o [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 com as mesmas cargas de trabalho instaladas.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

Para testar o código do dispositivo neste tutorial, você precisará do seguinte:

* O [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases).
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Modelar o dispositivo

Use a _linguagem de definição de gêmeo digital_ para criar um modelo de funcionalidade do dispositivo. Um modelo normalmente consiste em vários arquivos de definição de _interface_ e um único arquivo de modelo. O **Azure IoT Tools para VS Code** inclui ferramentas para ajudá-lo a criar e editar esses arquivos JSON.

### <a name="create-the-interface-file"></a>Criar o arquivo de interface

Para criar um arquivo de interface que define as funcionalidades do dispositivo IoT no VS Code:

1. Crie uma pasta chamada **devicemodel**.

1. Inicie o VS Code e use **Ctrl+Shift+P** para abrir a paleta de comandos.

1. Insira **Plug and Play** e, em seguida, selecione o comando **IoT Plug and Play: Criar Interface**.

1. Procure e selecione a pasta **devicemodel** criada.

1. Em seguida, insira **EnvironmentalSensor** como o nome da interface e pressione **Enter**. O VS Code cria um arquivo de interface de exemplo chamado **EnvironmentalSensor.interface.json**.

1. Substitua o conteúdo desse arquivo pelo JSON a seguir e substitua `{your name}` no campo `@id` por um valor exclusivo. Use somente os caracteres a-z, A-Z, 0-9 e sublinhado. Para obter mais informações, confira [Formato do identificador do Gêmeo Digital](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). A ID da interface precisa ser exclusiva para salvar a interface no repositório:

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Essa interface define as propriedades do dispositivo, como **Nome do Cliente**, tipos de telemetria, como **Temperatura**, e comandos como a **turnon**.

1. Adicione uma funcionalidade de comando chamada **piscar** ao final desse arquivo de interface. Adicione uma vírgula antes de adicionar o comando. Tente digitar a definição para ver como o IntelliSense, o preenchimento automático e a validação podem ajudá-lo a editar uma definição de interface:

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Salve o arquivo.

### <a name="create-the-model-file"></a>Criar o arquivo de modelo

O arquivo de modelo especifica as interfaces implementadas pelo dispositivo IoT Plug and Play. Normalmente, há pelo menos duas interfaces em um modelo – uma ou mais que definem as funcionalidades específicas do dispositivo e uma interface padrão que precisa ser implementada por todos os dispositivos IoT Plug and Play.

Para criar um arquivo de modelo que especifica as interfaces implementadas pelo dispositivo IoT Plug and Play no VS Code:

1. Use **Ctrl+Shift+P** para abrir a paleta de comandos.

1. Insira **Plug and Play** e, em seguida, selecione o comando **IoT Plug and Play: Criar Modelo de Funcionalidade**. Em seguida, insira **SensorboxModel** como o nome do modelo. O VS Code cria um arquivo de interface de exemplo chamado **SensorboxModel.capabilitymodel.json**.

1. Substitua o conteúdo desse arquivo pelo JSON a seguir e substitua `{your name}` no campo `@id` e na interface `EnvironmentalSensor` pelo mesmo valor usado no arquivo **EnvironmentalSensor.interface.json**. A ID da interface precisa ser exclusiva para salvar a interface no repositório:

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
    }
    ```

    O modelo define um dispositivo que implementa a interface **EnvironmentalSensor** e a interface **DeviceInformation** padrão.

1. Salve o arquivo.

### <a name="download-the-deviceinformation-interface"></a>Baixar a interface DeviceInformation

Antes de gerar o código de esqueleto do modelo, você precisa criar uma cópia local de **DeviceInformation** do *repositório de modelos públicos*. O repositório de modelos públicos já contém a interface **DeviceInformation**.

Para baixar a interface **DeviceInformation** do repositório de modelos públicos usando o VS Code:

1. Use **Ctrl+Shift+P** para abrir a paleta de comandos.

1. Insira **Plug and Play**, selecione o comando **Abrir Repositório de modelos** e, em seguida, selecione **Abrir Repositório de Modelos Públicos**.

1. Selecione **Interfaces**, selecione a interface de informações do dispositivo com a ID `urn:azureiot:DeviceManagement:DeviceInformation:1` e, em seguida, selecione **Baixar**.

Agora você tem os três arquivos que compõem o modelo de funcionalidade do dispositivo:

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>Publicar o modelo

Para que a ferramenta Azure IoT Explorer leia o modelo de funcionalidade do dispositivo, você precisará publicá-lo no repositório de sua empresa. Para publicá-lo por meio do VS Code, você precisará da cadeia de conexão do repositório da empresa:

1. Navegue até o [portal do Azure Certified para IoT](https://aka.ms/ACFI).

1. Use sua _conta corporativa_ da Microsoft para entrar no portal.

1. Selecione **Repositório da empresa** e, em seguida, **Cadeias de conexão**.

1. Copie a cadeia de conexão.

Para abrir o repositório da empresa no VS Code:

1. Use **Ctrl+Shift+P** para abrir a paleta de comandos.

1. Insira **Plug and Play** e, em seguida, selecione o comando **IoT Plug and Play: Abrir Repositório de Modelos**.

1. Selecione **Abrir Repositório de Modelos Organizacionais** e cole a cadeia de conexão.

1. Pressione **Enter** para abrir o repositório da empresa.

Para publicar o modelo de funcionalidade do dispositivo e as interfaces no repositório da empresa:

1. Use **Ctrl+Shift+P** para abrir a paleta de comandos.

1. Insira **Plug and Play** e, em seguida, selecione o comando **IoT Plug and Play: Enviar arquivos para o comando Repositório de Modelos**.

1. Selecione os arquivos **EnvironmentalSensor.interface.json** e **SensorboxModel.capabilitymodel.json** e selecione **OK**.

Os arquivos agora são armazenados no repositório da empresa.

## <a name="generate-code"></a>Gerar código

Use o **Azure IoT Tools para VS Code** para gerar o código de esqueleto C do modelo. Para gerar o código de esqueleto no VS Code:

1. Use **Ctrl+Shift+P** para abrir a paleta de comandos.

1. Insira **Plug and Play** e, em seguida, selecione o comando **IoT Plug and Play: Gerar Stub do Código do Dispositivo**.

1. Selecione o arquivo de modelo de funcionalidade **SensorboxModel.capabilitymodel.json**.

1. Insira **sensorbox_app** como o nome do projeto.

1. Escolha **ANSI C** como a linguagem.

1. Escolha **Por meio da cadeia de conexão de dispositivo do Hub IoT** como o método de conexão.

1. Escolha **Projeto CMake no Windows** como modelo de projeto.

1. Escolha **Via Vcpkg** como a maneira de incluir o SDK do dispositivo.

O VS Code gera o código de esqueleto C e salva os arquivos da pasta **sensorbox_app** na pasta **modelcode**. O VS Code abre uma nova janela que contém os arquivos de código gerados.

## <a name="update-the-generated-code"></a>Atualizar o código gerado

Antes de criar e executar o código, você precisa implementar as propriedades, a telemetria e os comandos fragmentados.

Para fornecer implementações para o código fragmentado no VS Code:

1. Abra o arquivo **SensorboxModel_impl.c**.

1. Adicione o código para implementar as funções fragmentadas.

1. Salve suas alterações.

## <a name="build-the-code"></a>Compilar o código

Antes de executar o código para testar o dispositivo IoT Plug and Play com um hub IoT do Azure, você precisa compilar o código.

Siga as instruções do arquivo **Readme.md** na pasta **sensorbox_app** para compilar e executar o código no Windows. A seção a seguir inclui instruções para recuperar uma cadeia de conexão de dispositivo a ser usada quando você executar o código do dispositivo.

## <a name="test-the-code"></a>Testar o código

Quando você executa o código, ele se conecta ao Hub IoT e começa a enviar valores de propriedade e telemetria de exemplo. O dispositivo também responde a comandos enviados do Hub IoT. Para verificar esse comportamento:

1. Para criar um hub IoT:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Adicione um dispositivo ao hub IoT e recupere a cadeia de conexão:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Anote a cadeia de conexão.

1. Em um prompt de comando, navegue até a pasta **azure-iot-sdk-c** na qual você criou o SDK e as amostras. Em seguida, navegue até a pasta **cmake\\sensorbox_app\\Release**.

1. Execute o comando a seguir:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Use a ferramenta Azure IoT Explorer para interagir com o dispositivo IoT Plug and Play conectado ao hub IoT. Para obter mais informações, confira [Instalar e usar o Azure IoT Explorer](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um IoT Plug and Play pronto para certificação, saiba como:

> [!div class="nextstepaction"]
> [Criar um dispositivo que esteja pronto para certificação](tutorial-build-device-certification.md)
