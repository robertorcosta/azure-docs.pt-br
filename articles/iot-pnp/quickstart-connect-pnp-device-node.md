---
title: Conectar o código do dispositivo de exemplo do IoT Plug and Play Preview ao Hub IoT   Microsoft Docs
description: Usando o Node.js, compile e execute o código do dispositivo de exemplo do IoT Plug and Play Preview que se conecta a um Hub IoT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo ao hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c5d69ae21cd240b0c68b9694a55de2cf879a1966
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964780"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Início Rápido: Conectar um aplicativo de exemplo de dispositivo IoT Plug and Play Preview ao Hub IoT (Node.js)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Este guia de início rápido mostra como criar um aplicativo de exemplo de dispositivo IoT Plug and Play, conectá-lo ao Hub IoT e usar a ferramenta Azure IoT Explorer para exibir as informações que ele envia ao Hub. O aplicativo de exemplo é escrito para Node.js e é incluído no SDK do Dispositivo do Hub IoT do Azure para Node.js. Um desenvolvedor de soluções pode usar a ferramenta do Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de exibir nenhum código de dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa do Node.js em seu computador de desenvolvimento. Você pode baixar a versão mais recente recomendada para várias plataformas de [nodejs.org](https://nodejs.org).

Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

```cmd/sh
node --version
```

### <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Baixe e instale a versão mais recente do **Explorador do Azure IoT** na página do [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o arquivo .msi em "Assets" (Ativos) para obter a atualização mais recente.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o seguinte comando para obter a _cadeia de conexão do hub IoT_ para o seu hub (nota para usar mais tarde):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste início rápido, você preparará um ambiente de desenvolvimento que pode ser usado para clonar e compilar o SDK do dispositivo Hub IoT do Azure para Node.js.

Abra um prompt de comando no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub do [SDK do Microsoft Azure IoT para Node.js](https://github.com/Azure/azure-iot-sdk-node) nesta localização:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node --recursive -b digitaltwins-preview
```

Essa operação pode levar vários minutos para ser concluída.

## <a name="install-required-libraries"></a>Instalar as bibliotecas necessárias

Use o SDK do dispositivo para criar o código de exemplo incluído. O aplicativo criado simula um dispositivo que se conecta a um Hub IoT. O aplicativo envia a telemetria e as propriedades e recebe comandos.

1. Em uma janela de terminal local, vá para a pasta do repositório clonado e navegue até a pasta **/azure-iot-sdk-node/digitaltwins/samples/device/javascript**. Em seguida, execute o seguinte comando para instalar as bibliotecas necessárias:

    ```cmd/sh
    npm install
    ```
1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

Execute um aplicativo de exemplo no SDK para simular um dispositivo IoT Plug and Play que envia telemetria ao Hub IoT. Para executar o aplicativo de exemplo, use o seguinte comando:

```cmd\sh
    node sample_device.js
```

Você vê a saída a seguir, indicando que o dispositivo começou a enviar dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade.

   ![Mensagens de confirmação do dispositivo](media/quickstart-connect-pnp-device/device-confirmation-node.png)

 Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para garantir que a ferramenta possa ler as definições de modelo de interface do seu dispositivo, selecione **Configurações**. No menu Configurações, **No dispositivo conectado** pode já aparecer nas configurações de Plug and Play; se não aparecer, selecione **+ Adicionar fonte de definição de módulo** e, em seguida, **No dispositivo conectado** para adicioná-la.

1. Novamente na página de visão geral **Dispositivos**, localize a identidade do dispositivo criada anteriormente. Com o aplicativo do dispositivo ainda em execução no prompt de comando, verifique se o **Estado da conexão** do dispositivo no Azure IoT Explorer está sendo relatado como _Conectado_ (caso contrário, clique em **Atualizar** até que ele esteja). Selecione o dispositivo para exibir mais detalhes.

1. Expanda a interface com a ID **urn:contoso:com:EnvironmentalSensor:1** para revelar a interface e os primitivos do IoT Plug and Play: propriedades, comandos e telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a um Hub IoT. Para saber mais sobre como criar uma solução que interage com os dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Instruções: Conectar-se a um dispositivo de IoT Plug and Play Preview e interagir com ele](howto-develop-solution.md)
