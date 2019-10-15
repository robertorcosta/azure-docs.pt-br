---
title: Enviar telemetria ao Hub IoT do Azure (C) | Microsoft Docs
description: Neste início rápido, você executará dois aplicativos C de exemplo para enviar telemetria simulada a um Hub IoT e ler a telemetria do Hub IoT para processamento na nuvem.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 4ccfa45c56a7e59024ce0639f218861054e32395
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166954"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Início Rápido: Enviar a telemetria de um dispositivo para um hub IoT e lê-la com um back-end (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que permite a ingestão de grandes volumes de telemetria de seus dispositivos IoT pela nuvem para processamento ou armazenamento. Neste início rápido, você envia telemetria de um aplicativo de dispositivo simulado, por meio do Hub IoT, para um aplicativo de back-end para processamento.

O início rápido usa um aplicativo de exemplo do C do [SDK do dispositivo IoT do Azure para C](iot-hub-device-sdk-c-intro.md) para enviar telemetria a um hub IoT. Os SDKs do dispositivo IoT do Azure são escritos em [ANSI C (C99)](https://wikipedia.org/wiki/C99) para portabilidade e compatibilidade geral com a plataforma. Antes de executar o código de exemplo, você criará um hub IoT e registrará o dispositivo simulado nesse hub.

Este artigo foi escrito para Windows, mas você pode concluir este início rápido no Linux também.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [Visual Studio 2019](https://www.visualstudio.com/vs/) com a carga de trabalho ["Desenvolvimento de área de trabalho com C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada.
* Instale a versão mais recente do [Git](https://git-scm.com/download/).
* Execute o comando a seguir para adicionar a Extensão do Microsoft Azure IoT para a CLI do Azure à instância do Cloud Shell. A Extensão de IoT adiciona comandos específicos do Hub IoT, do IoT Edge e do DPS (Serviço de Provisionamento de Dispositivos) no IoT à CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste início rápido, você usará o [SDK do dispositivo IoT do Azure para C](iot-hub-device-sdk-c-intro.md). 

Para os ambientes a seguir, você pode usar o SDK instalando esses pacotes e bibliotecas:

* **Linux**: pacotes apt-get estão disponíveis para Ubuntu 16.04 e 18.04 que usam as seguintes arquiteturas de CPU: amd64, arm64, armhf e i386. Para saber mais, confira [Usar o apt-get para criar um projeto cliente para dispositivo C no Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: Para os desenvolvedores que criam aplicativos de dispositivo na plataforma mbed, publicamos uma biblioteca e amostras que ajudarão você a começar a usar o Hub IoT do Azure em minutos. Para saber mais, confira [Usar a biblioteca mbed](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: Caso esteja fazendo o desenvolvimento no Arduino, aproveite a biblioteca IoT do Azure disponível no gerenciador de bibliotecas do IDE do Arduino. Para saber mais, confira [A biblioteca do Hub IoT do Azure para Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: O SDK do Dispositivo do Hub IoT está disponível como CocoaPods para desenvolvimento de dispositivo Mac e iOS. Para saber mais, confira os [Exemplos do iOS para IoT do Microsoft Azure](https://cocoapods.org/pods/AzureIoTHubClient).

No entanto, neste início rápido, você preparará um ambiente de desenvolvimento usado para clonar e criar o [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) no GitHub. O SDK no GitHub inclui o código de exemplo usado neste início rápido.

1. Baixe o [sistema de build CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho de "Desenvolvimento para Desktop com C++") estejam instalados no computador, **antes** da instalação de `CMake`. Após a instalação dos pré-requisitos e verificação do download, instale o sistema de compilação CMake.

2. Abra um prompt de comando ou um shell do Git Bash e navegue até um diretório de trabalho para o qual você deseja clonar o SDK de C do IoT do Azure. Execute o seguinte comando para clonar o repositório do GitHub [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Essa operação deve demorar alguns minutos.

3. Crie um subdiretório `cmake` no diretório raiz do repositório git e navegue até essa pasta. Insira os seguintes comandos do diretório de trabalho:

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Execute o comando a seguir, que compila uma versão do SDK específica para a plataforma cliente de desenvolvimento. Uma solução do Visual Studio para o dispositivo simulado será gerada no diretório `cmake`.

    ```cmd
    cmake ..
    ```

    Se `cmake` não conseguir encontrar o compilador do C++, você poderá obter erros de build ao executar o comando acima. Se isso acontecer, tente executar esse comando no [prompt de comando do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Após o sucesso da compilação, as últimas linhas de saída serão semelhantes à seguinte saída:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Nesta seção, você usará o Azure Cloud Shell com a [extensão de IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **MyCDevice**: esse é o nome do dispositivo que está sendo registrado. É recomendável usar **MyCDevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

O aplicativo de dispositivo simulado se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT e envia uma cadeia de caracteres como telemetria simulada.

1. Em um editor de texto, abra o arquivo de origem iothub_convenience_sample.c e revise o código de exemplo para enviar a telemetria. O arquivo está localizado no local a seguir, sob o diretório de trabalho em que você clonou o SDK do C de IoT do Azure:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Localize a declaração da constante `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Substitua o valor da constante `connectionString` pela cadeia de conexão do dispositivo que você anotou anteriormente. Em seguida, salve suas alterações em **iothub_convenience_sample.c**.

3. Em uma janela de terminal local, navegue até o diretório do projeto *iothub_convenience_sample* no diretório do CMake que você criou no SDK de C do IoT do Azure. Insira o seguinte comando do diretório de trabalho:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Execute CMake na janela de terminal local para compilar o exemplo com o valor `connectionString` atualizado:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Na janela de terminal local, execute o seguinte comando para executar o aplicativo de dispositivo simulado:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    A captura de tela a seguir mostra o resultado à medida que o aplicativo de dispositivo simulado envia telemetria para o hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria do hub

Nesta seção, você usará o Azure Cloud Shell com a [extensão de IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para monitorar as mensagens de dispositivo enviadas pelo dispositivo simulado.

1. Usando o Azure Cloud Shell, execute o seguinte comando para se conectar e ler mensagens do hub IoT:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Leia as mensagens do dispositivo usando a CLI do Azure](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, enviou telemetria simulada para o hub usando um aplicativo C e leu a telemetria do hub usando o Azure Cloud Shell.

Para saber mais sobre o desenvolvimento com o SDK de C do Hub IoT do Azure, acesse o guia de instruções a seguir:

> [!div class="nextstepaction"]
> [Desenvolver usando o SDK de C do Hub IoT do Azure](iot-hub-devguide-develop-for-constrained-devices.md)