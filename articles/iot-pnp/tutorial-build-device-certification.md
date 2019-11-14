---
title: Criar um dispositivo da versão prévia do Azure IoT Plug and Play pronto para certificação | Microsoft Docs
description: Como desenvolvedor de dispositivos, saiba como você pode criar um dispositivo o IoT Plug and Play Preview pronto para certificação.
author: tbhagwat3
ms.author: tanmayb
ms.date: 06/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e4dd5215812f0fd1a43afe0923601417bc8e6916
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569632"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Criar um dispositivo do IoT Plug and Play Preview pronto para certificação

Este tutorial descreve como, na condição de desenvolvedor de dispositivos, você pode criar um dispositivo do IoT Plug and Play Preview pronto para a certificação.

Os testes de certificação verificam se:

- O código do dispositivo IoT Plug and Play está instalado no dispositivo.
- O código do dispositivo IoT Plug and Play é criado com o SDK de IoT do Azure.
- O código do dispositivo dá suporte ao [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](../iot-dps/about-iot-dps.md).
- O código do dispositivo implementa a Interface de Informações do Dispositivo.
- O modelo de funcionalidade e o código do dispositivo funcionam com o IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

- [Visual Studio Code](https://code.visualstudio.com/download)
- Pacote de extensão [Azure IoT Tools para VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

Você também precisa do dispositivo IoT Plug and Play criado no [Início rápido: usar um modelo de funcionalidade do dispositivo para criar um dispositivo](quickstart-create-pnp-device.md).

## <a name="store-a-capability-model-and-interfaces"></a>Armazenar um modelo de funcionalidade e as interfaces

Para dispositivos do IoT Plug and Play, você deve criar um modelo de funcionalidade e interfaces que definem as funcionalidades do dispositivo como arquivos JSON.

Você pode armazenar esses arquivos JSON em três locais diferentes:

- O repositório de modelos público.
- O repositório de modelos da empresa.
- Em seu dispositivo.

Atualmente, para certificar o dispositivo, os arquivos devem ser armazenados em seu repositório de modelos da empresa ou no repositório de modelos público.

## <a name="include-the-required-interfaces"></a>Incluir as interfaces necessárias

Para aprovação no processo de certificação, você deve incluir e implementar a interface de **Informações do Dispositivo** no modelo de funcionalidade. Essa interface tem a seguinte identificação:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Se você concluiu o [Início Rápido: usar um modelo de funcionalidade do dispositivo para criar um dispositivo](quickstart-create-pnp-device.md), você já incluiu a **Interface de Informações** do dispositivo no modelo.

Para incluir a **interface de informações** do dispositivo no modelo de dispositivo, adicione a ID da interface à propriedade `implements` do modelo de funcionalidade:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

Para exibir a interface de **Informações do Dispositivo** no VS Code:

1. Use **Ctrl+Shift+P** para abrir a paleta de comandos.

1. Insira **Plug and Play** e, em seguida, selecione o comando **Abrir Repositório de Modelos do IoT Plug and Play**. Escolha **Abrir Repositório de Modelos Público**. O repositório de modelos público é aberto no VS Code.

1. No repositório de modelos público, selecione a guia **Interfaces**, selecione o ícone de filtro e insira as **Informações do Dispositivo** no campo de filtro.

1. Para criar uma cópia local da interface de **Informações do Dispositivo**, selecione-a na lista filtrada e, em seguida, selecione **Baixar**. O VS Code exibe o arquivo de interface.

Para exibir a interface de **Informações do Dispositivo** usando a CLI do Azure:

1. [Instale a extensão de CLI do Azure IoT](howto-install-pnp-cli.md).

1. Use o seguinte comando da CLI do Azure para mostrar uma interface com a ID da interface de Informações do Dispositivo:

    ```cmd/sh
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Para saber mais, confira [Instalar e usar a extensão do Azure IoT para a CLI do Azure](howto-install-pnp-cli.md).

## <a name="update-device-code"></a>Atualizar código do dispositivo

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Habilitar o provisionamento de dispositivos por meio do DPS (serviço de provisionamento de dispositivos) do IoT do Azure

Para certificar o dispositivo, ele precisa habilitar o provisionamento de dispositivos por meio do [DPS (serviço de provisionamento de dispositivos) do IoT do Azure](https://docs.microsoft.com/azure/iot-dps/about-iot-dps). Para adicionar a capacidade de usar o DPS, você pode gerar o stub do código C no VS Code. Siga estas etapas:

1. Abra a pasta com o arquivo DCM no VS Code, use **Ctrl+Shift+P** para abrir a paleta de comandos, insira **IoT Plug and Play** e selecione **Gerar Stub do Código do Dispositivo**.

1. Escolha o arquivo DCM que deseja usar para gerar o stub do código do dispositivo.

1. Insira o nome do projeto, que é o nome do aplicativo do dispositivo.

1. Escolha **ANSI C** como a linguagem.

1. Escolha **Por meio da chave simétrica do DPS (Serviço de Provisionamento de Dispositivos)** como o método de conexão.

1. Escolha **Projeto CMake no Windows** ou **Projeto CMake no Linux** como modelo de projeto, dependendo do sistema operacional do dispositivo.

1. O VS Code abre uma nova janela com os arquivos stub do código do dispositivo gerados.

1. Depois de compilar o código, insira as credenciais do DPS (**Escopo de ID do DPS**, **Chave Simétrica do DPS**, **ID do Dispositivo**) como parâmetros para o aplicativo. Para obter as credenciais no portal de certificação, confira [Conectar e testar seu dispositivo IoT Plug and Play](tutorial-certification-test.md#connect-and-discover-interfaces).

    ```cmd/sh
    .\your_pnp_app.exe [DPS ID Scope] [DPS symmetric key] [device ID]
    ```

### <a name="implement-standard-interfaces"></a>Implementar interfaces padrão

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>Implementar as interfaces de Informações do Modelo e Informações do SDK

O SDK do dispositivo IoT do Azure implementa as interfaces de Informações do Modelo e Informações do SDK. Se você usar a função de geração de código no VS Code, o código do dispositivo usará o SDK do dispositivo IoT Plug and Play.

Se você optar por não usar o SDK do dispositivo IoT do Azure, poderá usar o código-fonte do SDK como referência para sua própria implementação.

#### <a name="implement-the-device-information-interface"></a>Implementar a interface de Informações do Dispositivo

Implemente a interface de **Informações do Dispositivo** no dispositivo e forneça informações específicas do dispositivo em tempo de execução.

Você pode usar uma implementação de exemplo da interface de **Informações do Dispositivo** para [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) como referência.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Implementar todos os recursos definidos no modelo

Durante a certificação, o dispositivo é testado programaticamente para garantir que ele implemente os recursos definidos nas interfaces dele. Use o código de status HTTP 501 para responder à propriedade de leitura/gravação e às solicitações de comando se o dispositivo não as implementa.

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um dispositivo IoT Plug and Play pronto para a certificação, a próxima etapa sugerida é:

> [!div class="nextstepaction"]
> [Aprender a certificar o dispositivo](tutorial-certification-test.md)
