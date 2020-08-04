---
title: Conectar o código da versão prévia do IoT Plug and Play ao Hub IoT – C# | Microsoft Docs
description: Usando C# (.NET), compile e execute o código do dispositivo de exemplo do IoT Plug and Play Preview que se conecta a um Hub IoT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo ao hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d598bbb792c071db281ebdc562402ba0198305ab
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044190"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Início Rápido: Conectar um aplicativo de exemplo de dispositivo IoT Plug and Play Preview ao Hub IoT (C#)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Este guia de início rápido mostra como criar um aplicativo de exemplo de dispositivo IoT Plug and Play, conectá-lo ao Hub IoT e usar a ferramenta Azure IoT Explorer para exibir as informações que ele envia ao Hub. O aplicativo de exemplo é escrito em C# (.NET) e é fornecido como parte dos exemplos de IoT do Azure para a coleção C# (.NET). Um desenvolvedor de soluções pode usar a ferramenta do Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de exibir nenhum código de dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisará instalar o .NET Core 3.0 no computador de desenvolvimento. Baixe esta versão do SDK do .NET Core para várias plataformas em [Baixar o .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).

Você pode verificar a versão do .NET que está em seu computador de desenvolvimento executando o seguinte comando em uma janela do terminal local: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Baixe e instale a versão mais recente do **Explorador do Azure IoT** na página do [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o arquivo .msi em "Assets" (Ativos) para obter a atualização mais recente.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o seguinte comando para obter a _cadeia de conexão do hub IoT_ para o seu hub (nota para usar mais tarde):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste início rápido, você vai preparar um ambiente de desenvolvimento que pode ser usado para clonar e compilar as Amostras de IoT do Azure para C# (.NET).

Abra um prompt de comando no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub das [Amostras de IoT do Azure para C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) nesta localização:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Essa operação pode levar vários minutos para ser concluída.

## <a name="run-the-device-sample"></a>Executar o exemplo de dispositivo

Você usa o código de exemplo clonado para criar um aplicativo que simula um dispositivo que se conecta a um hub IoT. O aplicativo envia a telemetria e as propriedades e recebe comandos.

1. Em uma janela de terminal local, vá para a pasta do repositório clonado e navegue até a pasta **azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample** folder. 

1. Configure a _cadeia de conexão do dispositivo_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Execute um aplicativo de exemplo para simular um dispositivo IoT Plug and Play que envia telemetria ao Hub IoT. Na mesma janela do terminal, para criar os pacotes necessários e executar o aplicativo de exemplo, use o seguinte comando:

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

Você vê mensagens dizendo que o dispositivo foi registrado com êxito e está aguardando atualizações da nuvem. Isso indica que o dispositivo agora está pronto para receber comandos e atualizações de propriedade e começou a enviar dados telemétricos para o hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Para garantir que a ferramenta possa ler as definições de modelo de interface do seu dispositivo, selecione **Configurações**. No menu Configurações, **No dispositivo conectado** pode já aparecer nas configurações de Plug and Play; se não aparecer, selecione **+ Adicionar fonte de definição de módulo** e, em seguida, **No dispositivo conectado** para adicioná-la.

1. Novamente na página de visão geral **Dispositivos**, localize a identidade do dispositivo criada anteriormente. Com o aplicativo do dispositivo ainda em execução no prompt de comando, verifique se o **Estado da conexão** do dispositivo no Azure IoT Explorer está sendo relatado como _Conectado_ (caso contrário, clique em **Atualizar** até que ele esteja). Selecione o dispositivo para exibir mais detalhes.

1. Expanda a interface com a ID **urn:csharp_sdk_sample:EnvironmentalSensor:1** para revelar a interface e os primitivos do IoT Plug and Play: propriedades, comandos e telemetria.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a um Hub IoT. Para saber mais sobre como criar uma solução que interage com os dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Instruções: Conectar-se a um dispositivo de IoT Plug and Play Preview e interagir com ele](howto-develop-solution.md)
