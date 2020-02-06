---
title: Conectar o código do dispositivo de exemplo do IoT Plug and Play Preview ao Hub IoT   Microsoft Docs
description: Usando Java, compile e execute o código do dispositivo de exemplo do IoT Plug and Play Preview que se conecta a um Hub IoT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo ao hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: aa676dd374eccf2a4b5c4622689ed402c8679e5a
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964797"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>Início Rápido: Conectar um aplicativo de exemplo de dispositivo IoT Plug and Play Preview ao Hub IoT (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Este guia de início rápido mostra como criar um aplicativo de exemplo de dispositivo IoT Plug and Play, conectá-lo ao Hub IoT e usar a ferramenta Azure IoT Explorer para exibir as informações que ele envia ao Hub. O aplicativo de exemplo é escrito em Java e é fornecido como parte dos exemplos de IoT do Azure para a coleção Java. Um desenvolvedor de soluções pode usar a ferramenta do Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de exibir nenhum código de dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisites

Para concluir este início rápido, você precisa do Java SE 8 em seu computador de desenvolvimento. Você também precisa instalar o Maven 3.

Para obter detalhes sobre como configurar essas informações, confira [Preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) no SDK do dispositivo IoT do Microsoft Azure para Java.

### <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Baixe e instale a versão mais recente do **Explorador do Azure IoT** na página do [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o arquivo .msi em "Assets" (Ativos) para obter a atualização mais recente.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o seguinte comando para obter a _cadeia de conexão do hub IoT_ para o seu hub (nota para usar mais tarde):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste início rápido, você preparará um ambiente de desenvolvimento que pode ser usado para clonar e compilar as Amostras de IoT do Azure para Java.

Abra uma janela de terminal no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub das [Amostras de IoT do Azure para Java](https://github.com/Azure-Samples/azure-iot-samples-java) nesta localização:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

Essa operação pode levar vários minutos para ser concluída.

## <a name="build-the-code"></a>Compilar o código

Você usa o código de exemplo clonado para criar um aplicativo que simula um dispositivo que se conecta a um hub IoT. O aplicativo envia a telemetria e as propriedades e recebe comandos.

1. Em uma janela de terminal local, vá para a pasta do repositório clonado e navegue até a pasta **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample** folder. Em seguida, execute os seguintes comandos para instalar as bibliotecas necessárias e compilar o aplicativo de dispositivo simulado:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>Executar o exemplo de dispositivo

Execute um aplicativo de exemplo para simular um dispositivo IoT Plug and Play que envia telemetria ao Hub IoT. Para executar o aplicativo de exemplo, use o seguinte comando:

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

Você vê mensagens dizendo que o dispositivo está conectado, executando várias etapas de configuração e aguardando atualizações de serviço, seguidas pelos logs de telemetria. Isso indica que o dispositivo agora está pronto para receber comandos e atualizações de propriedade e começou a enviar dados telemétricos para o hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para garantir que a ferramenta possa ler as definições de modelo de interface do seu dispositivo, selecione **Configurações**. No menu Configurações, **No dispositivo conectado** pode já aparecer nas configurações de Plug and Play; se não aparecer, selecione **+ Adicionar fonte de definição de módulo** e, em seguida, **No dispositivo conectado** para adicioná-la.

1. Novamente na página de visão geral **Dispositivos**, localize a identidade do dispositivo criada anteriormente. Com o aplicativo do dispositivo ainda em execução no prompt de comando, verifique se o **Estado da conexão** do dispositivo no Azure IoT Explorer está sendo relatado como _Conectado_ (caso contrário, clique em **Atualizar** até que ele esteja). Selecione o dispositivo para exibir mais detalhes.

1. Expanda a interface com a ID **urn:java:EnvironmentalSensor:1** para revelar a interface e os primitivos do IoT Plug and Play: propriedades, comandos e telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a um Hub IoT. Para saber mais sobre como criar uma solução que interage com os dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Instruções: Conectar-se a um dispositivo de IoT Plug and Play Preview e interagir com ele](howto-develop-solution.md)
