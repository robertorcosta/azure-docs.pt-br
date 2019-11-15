---
title: Conectar o código do dispositivo de exemplo do IoT Plug and Play Preview ao Hub IoT (Windows) | Microsoft Docs
description: Criar e executar um código do dispositivo de exemplo da versão prévia do IoT Plug and Play no Windows que se conecta a um Hub IoT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo ao hub.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 2dd5d197851b0090ac1af7bbde5a1ad1b951c785
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569906"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub"></a>Início Rápido: Conectar um aplicativo do dispositivo de exemplo da versão prévia do IoT Plug and Play com Windows ao Hub IoT

Este guia de início rápido mostra como criar um aplicativo de exemplo de dispositivo IoT Plug and Play, conectá-lo ao Hub IoT e usar a ferramenta Azure IoT Explorer para exibir as informações que ele envia ao Hub. O aplicativo de exemplo é escrito em C e está incluído no SDK do dispositivo IoT do Azure para C. Um desenvolvedor de soluções pode usar a ferramenta do Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de exibir nenhum código de dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa instalar o seguinte software no computador local:

* [Ferramentas de Build para o Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) com **Ferramentas de build de C++** e cargas de trabalho do **componente do gerenciador de pacotes NuGet**. Ou se você já tem o [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 com as mesmas cargas de trabalho instaladas.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Baixe e instale a ferramenta Azure IoT Explorer na página da [última versão](https://github.com/Azure/azure-iot-explorer/releases).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Preparar um hub IoT

Você também precisará de um Hub IoT do Azure em sua assinatura do Azure para concluir este início rápido. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> Durante a versão prévia pública, os recursos de IoT Plug and Play estão disponíveis apenas em hubs IoT criados nas regiões **Centro dos EUA**, **Europa Setentrional** e **Leste do Japão**.

Adicione a Extensão IoT do Microsoft Azure para a CLI do Azure:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Use o comando a seguir para criar uma identidade do dispositivo no Hub IoT. Substitua os espaços reservados **YourIoTHubName** e **YourDevice** pelos nomes reais. Caso não tenha um Hub IoT, siga [estas instruções para criar um](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Execute os seguintes comandos para obter a _cadeia de conexão de dispositivo_ para a dispositivo recém-registrado:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Execute os seguintes comandos para obter a _cadeia de conexão do Hub IoT_ para o hub:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

### <a name="get-azure-iot-device-sdk-for-c"></a>Obter o SDK do dispositivo IoT do Azure para C

Neste início rápido, você preparará um ambiente de desenvolvimento que pode ser usado para clonar e compilar o SDK do dispositivo IoT do Azure para C.

Abra um prompt de comando. Execute o seguinte comando para clonar o repositório do GitHub [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c):

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Essa operação deve demorar alguns minutos.

## <a name="build-the-code"></a>Compilar o código

O aplicativo criado simula um dispositivo que se conecta a um Hub IoT. O aplicativo envia a telemetria e as propriedades e recebe comandos.

1. Crie um subdiretório `cmake` na pasta raiz do SDK do dispositivo e navegue até essa pasta:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para compilar o SDK do dispositivo e o stub do código gerado:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Se o CMake não conseguir localizar o compilador C++, você obterá erros de build ao executar o comando anterior. Se isso acontecer, tente executar este comando no [prompt de comando do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Executar o exemplo de dispositivo

Execute o aplicativo passando a cadeia de conexão do dispositivo do Hub IoT como parâmetro.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

O aplicativo do dispositivo iniciará o envio de dados ao Hub IoT.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

1. Abra o Azure IoT Explorer e você verá a página **Configurações de aplicativos**.

1. Insira a cadeia de conexão do Hub IoT e clique em **Conectar**.

1. Depois de se conectar, você verá a página de visão geral do dispositivo.

1. Para adicionar o repositório de sua empresa, selecione **Configurações**, **+Novo** e, em seguida, **No dispositivo conectado**.

1. Na página de visão geral do dispositivo, localize a identidade do dispositivo criada anteriormente e selecione-a para exibir mais detalhes.

1. Expanda a interface com a ID **urn:NOME_DA_SUA_EMPRESA_AQUI:EnvironmentalSensor:1** para ver os primitivos do IoT Plug and Play – propriedades, comandos e telemetria.

1. Selecione a página **Telemetria** para exibir os dados telemétricos que o dispositivo está enviando.

1. Selecione a página **Propriedades (não graváveis)** para exibir as propriedades não graváveis relatadas pelo dispositivo.

1. Selecione a página **Propriedades (graváveis)** para exibir as propriedades graváveis que podem ser atualizadas.

1. Expanda o **nome** da propriedade, atualize-o com um novo nome e selecione **atualizar propriedade gravável**. 

1. Para ver o novo nome exibido na coluna **Propriedade Relatada**, clique no botão **Atualizar** na parte superior da página.

1. Selecione a página **Comando** para exibir todos os comandos aos quais o dispositivo dá suporte.

1. Expanda o comando **piscar** e defina um novo intervalo de tempo de intermitência. Selecione **Enviar Comando** para chamar o comando no dispositivo.

1. Acesse o dispositivo simulado para verificar se o comando foi executado conforme o esperado.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a um Hub IoT. Para saber mais sobre como criar uma solução que interage com os dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Instruções: Conectar-se a um dispositivo de IoT Plug and Play Preview e interagir com ele](howto-develop-solution.md)
