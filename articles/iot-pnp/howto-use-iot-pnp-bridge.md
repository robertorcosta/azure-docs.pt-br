---
title: Como conectar um exemplo de ponte IoT Plug and Play em execução no Linux ou Windows a um hub IoT | Microsoft Docs
description: Compile e execute o IoT Plug and Play Bridge no Linux ou no Windows que se conecta a um hub IoT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo ao hub.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4c84fbb131809c92d597b46d18c53e46b636f8ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613468"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Como conectar um exemplo de ponte IoT Plug and Play em execução no Linux ou Windows para o Hub IoT

Este "como" mostra como criar o adaptador ambiental de exemplo do IoT Plug and Play Bridge, conectá-lo ao Hub IoT e usar a ferramenta do Azure IoT Explorer para exibir a telemetria que ele envia. A ponte de Plug and Play IoT é escrita em C e inclui o SDK do dispositivo IoT do Azure para C. Ao final deste tutorial, você deverá ser capaz de executar a ponte de Plug and Play IoT e ver a telemetria de relatório no Azure IoT Explorer: :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="uma captura de tela mostrando o Azure IOT Explorer com uma tabela de telemetria relatada (umidade, temperatura) da ponte de plug and Play IOT.":::

## <a name="prerequisites"></a>Pré-requisitos

Você pode executar este guia de início rápido no Linux ou no Windows. Os comandos do Shell neste guia de instruções seguem a Convenção do Windows para separadores de caminho ' `\` ', se você estiver acompanhando o Linux, certifique-se de trocar esses separadores por ' `/` '.

Os pré-requisitos diferem por sistema operacional:

### <a name="linux"></a>Linux

Este guia de início rápido pressupõe que você esteja usando o Ubuntu Linux. As etapas deste início rápido foram testadas com o Ubuntu 18.04.

Para concluir este início rápido no Linux, instale o seguinte software em um ambiente Linux local:

Instale o **GCC**, o **Git**, o **CMake** e todas as dependências exigidas usando o comando `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verifique se a versão do `cmake` é superior a **2.8.12** e se a versão do **GCC** é superior a **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Para concluir este início rápido no Windows, instale o seguinte software em um ambiente do Windows local:

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) – inclua a carga de trabalho **Desenvolvimento para Desktop com C++** ao [instalar](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true) o Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Para interagir com o dispositivo de exemplo na segunda parte deste guia de início rápido, use a ferramenta **Azure IoT Explorer**. [Baixe e instale a versão mais recente do Azure IoT Explorer](./howto-use-iot-explorer.md) para o seu sistema operacional.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o comando a seguir para obter a _cadeia de conexão do Hub IOT_ para o Hub. Anote essa cadeia de conexão, pois você a usará posteriormente neste guia de início rápido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Você também pode usar a ferramenta Azure IoT Explorer para localizar a cadeia de conexão do hub IoT.

Execute o comando a seguir para obter a _cadeia de conexão de dispositivo_ do dispositivo que você adicionou ao hub. Anote essa cadeia de conexão, pois você a usará posteriormente neste guia de início rápido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>Exibir o modelo

Você usará o Azure IoT Explorer em etapas posteriores para exibir o dispositivo quando ele se conectar ao Hub IoT. O Azure IoT Explorer precisará de uma cópia local do arquivo de modelo que corresponda à **ID de modelo** enviada pelo seu dispositivo. O arquivo de modelo permite que o IoT Explorer exiba a telemetria, as propriedades e os comandos que seu dispositivo implementa.

Quando você baixa o código na etapa abaixo, ele inclui os arquivos de modelo de exemplo na `pnpbridge/docs/schema` pasta. Para preparar o Gerenciador de IoT do Azure:

1. Crie uma pasta chamada *models* no computador local.
1. Exibir [EnvironmentalSensor.js](https://aka.ms/iot-pnp-bridge-env-model) e salvar o arquivo JSON na pasta *modelos*
1. Exiba [RootBridgeSampleDevice.js](https://aka.ms/iot-pnp-bridge-root-model) e salve o arquivo JSON na pasta *modelos* .

## <a name="download-the-code"></a>Baixar o código

Abra um prompt de comando no diretório de sua escolha. Execute o comando a seguir para clonar o repositório GitHub do [IoT plug and Play Bridge](https://aka.ms/iotplugandplaybridge) neste local:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Depois de clonar o repositório de ponte IoT Plug and Play para seu computador, abra um prompt de comando administrativo e navegue até o diretório do repositório clonado:

```cmd
cd pnpbridge
git submodule update --init --recursive
```

Essa operação deve demorar alguns minutos.

>[!NOTE]
> Se você tiver problemas com a falha na atualização do submódulo git clone, esse é um problema conhecido com caminhos de arquivo do Windows e git consulte: https://github.com/msysgit/git/pull/110 . Você pode tentar o seguinte comando para resolver o problema: `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>Configurando o JSON de configuração

Depois de clonar o repositório de ponte IoT Plug and Play para o seu computador, navegue até o `pnpbridge/docs/schema` diretório do repositório clonado onde você pode encontrar o [JSON de configuração](https://aka.ms/iot-pnp-bridge-env-config) ou `config.json` para o exemplo de sensor ambiental da ponte. Você pode saber mais sobre os arquivos de configuração no [documento conceitos de ponte do IoT plug and Play](concepts-iot-pnp-bridge.md).

Para o `root-_interface_model_id` campo, você precisará copiar a ID do modelo de plug and Play IOT que identifica o modelo do seu dispositivo. Neste exemplo, é `dtmi:com:example:SampleDevice;1`. Modifique os seguintes parâmetros em **pnp_bridge_parameters** nó no `config.json` arquivo na seção ':

  Usando a cadeia de conexão (Observação: a symmetric_key deve corresponder à chave SAS na cadeia de conexão):

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 Uma vez preenchido, o `config.json` arquivo deve ser semelhante a:

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 Depois de criar a ponte, você precisará colocá-la `config.json` no mesmo diretório que a ponte ou especificar o caminho quando ela for executada.

## <a name="build-the-iot-plug-and-play-bridge"></a>Compilar a ponte de Plug and Play IoT

Navegue até a pasta *pnpbridge* no diretório do repositório.

Para Windows, execute o seguinte em um [prompt de comando do desenvolvedor para o Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs):

```cmd
cd scripts\windows
build.cmd
```

Da mesma forma, para Linux, execute o seguinte:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>No Windows, você pode abrir a solução gerada pelo comando CMake no Visual Studio 2019. Abra o arquivo de projeto *azure_iot_pnp_bridge. sln* no diretório CMake e defina o projeto *pnpbridge_bin* como o projeto de inicialização na solução. Agora você pode compilar o exemplo no Visual Studio e executá-lo no modo de depuração.

## <a name="start-the-iot-plug-and-play-bridge"></a>Iniciar a ponte de Plug and Play IoT

 Inicie o exemplo de ponte IoT Plug and Play para sensores ambientais navegando até a pasta *pnpbridge* e executando o seguinte em um prompt de comando:

```bash
 cd cmake/pnpbridge_x86/src/adaptors/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adaptors\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

Após iniciar o exemplo de cliente do dispositivo, use a ferramenta Azure IoT Explorer para verificar se ele está funcionando.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a um Hub IoT. Para saber mais sobre como criar uma solução que interage com os dispositivos IoT Plug and Play, confira:

* [O que é o IoT Plug and Play Bridge](./concepts-iot-pnp-bridge.md)
* [Consulte a referência do desenvolvedor do GitHub para o IoT Plug and Play Bridge](https://aka.ms/iot-pnp-bridge-dev-doc)
* [Ponte de Plug and Play IoT no GitHub](https://aka.ms/iotplugandplaybridge)
