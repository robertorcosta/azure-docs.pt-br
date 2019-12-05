---
title: Conectar o código do dispositivo de exemplo da versão prévia do IoT Plug and Play ao Hub IoT (Linux) | Microsoft Docs
description: Criar e executar um código do dispositivo de exemplo da versão prévia do IoT Plug and Play que se conecta a um Hub IoT. Use a CLI do Azure para exibir as informações enviadas pelo dispositivo ao hub.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 802578c79fa086c74a56db8d47f83ae96d6b0194
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152145"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Início Rápido: Conectar um aplicativo do dispositivo de exemplo da versão prévia do IoT Plug and Play com Linux ao Hub IoT (Linux para C)

Este guia de início rápido mostra como criar um aplicativo de dispositivo IoT Plug and Play de exemplo no Linux, conectá-lo ao Hub IoT e usar a CLI do Azure para exibir as informações que ele envia ao hub. O aplicativo de exemplo é escrito em C e está incluído no SDK do dispositivo IoT do Azure para C. Um desenvolvedor de soluções pode usar a CLI do Azure para compreender as funcionalidades de um dispositivo IoT Plug and Play, sem a necessidade de exibir nenhum código de dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido pressupõe que você esteja usando o Ubuntu Linux. As etapas deste tutorial foram testadas com o Ubuntu 18.04.

Para concluir este início rápido, instale o seguinte software em um computador Linux local:

Instale o **GCC**, o **Git**, o **cmake** e todas as dependências usando o comando `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verifique se a versão do `cmake` é superior a **2.8.12** e se a versão do **GCC** é superior a **4.4.7**.

```sh
cmake --version
gcc --version
```

## <a name="prepare-an-iot-hub"></a>Preparar um hub IoT

Você também precisará de um Hub IoT do Azure em sua assinatura do Azure para concluir este início rápido. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Caso não tenha um hub IoT, siga [estas instruções para criar um](../iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Durante a versão prévia pública, os recursos de IoT Plug and Play estão disponíveis apenas em hubs IoT criados nas regiões **Centro dos EUA**, **Europa Setentrional** e **Leste do Japão**.

Se estiver usando a CLI do Azure no local, a versão do `az` deverá ser **2.0.73** ou posterior; o Azure Cloud Shell usa a versão mais recente. Use o comando `az --version` para verificar a versão instalada no computador.

Execute o seguinte comando para adicionar a Extensão de IoT do Microsoft Azure para a CLI do Azure à instância do Cloud Shell:
```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

As etapas deste início rápido exigem a versão **0.8.5** ou posterior da extensão. Use o comando `az extension list` para verificar a versão instalada, e o comando `az extension update` para atualizar, caso necessário.

Se estiver usando a CLI no local, entre em sua assinatura do Azure usando o seguinte comando:

```bash
az login
```

Se estiver usando o Azure Cloud Shell, então você já entrou automaticamente.

Use o comando a seguir para criar uma identidade do dispositivo no Hub IoT. Substitua os espaços reservados **YourIoTHubName** e **YourDeviceID** pelo seu próprio _nome do Hub IoT_ e uma _ID do dispositivo_ de sua escolha.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Execute o seguinte comando para obter a _cadeia de conexão de dispositivo_ do dispositivo que você acabou de registrar (nota para usar mais tarde):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste início rápido, você preparará um ambiente de desenvolvimento que pode ser usado para clonar e compilar o SDK do dispositivo Hub IoT do Azure para C.

Abra um prompt de comando no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub dos [SDKs de C e Bibliotecas do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) nesta localização:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Essa operação deve demorar alguns minutos.

## <a name="build-the-code"></a>Compilar o código

Use o SDK do dispositivo para criar o código de exemplo incluído. O aplicativo criado simula um dispositivo que se conecta a um Hub IoT. O aplicativo envia a telemetria e as propriedades e recebe comandos.

1. Crie um subdiretório `cmake` na pasta raiz do SDK do dispositivo e navegue até essa pasta:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para compilar o SDK do dispositivo e o stub do código gerado:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Atualizar o repositório de modelos

Antes de executar o exemplo, adicione o modelo de funcionalidade do dispositivo e as definições de interface ao repositório de modelos da empresa:

1. Entre no portal do [Microsoft Azure Certified para IoT](https://preview.catalog.azureiotsolutions.com) com uma conta corporativa ou de estudante da Microsoft, ou com uma ID de Parceiro da Microsoft, caso tenha uma.

1. Selecione **Repositório da empresa** e, em seguida, **Modelos de funcionalidade**.

1. Selecione **Novo** e **Carregar**.

1. Selecione o arquivo `SampleDevice.capabilitymodel.json` na pasta `digitaltwin_client/samples`, na pasta raiz do SDK do dispositivo. Selecione **Abrir** e **Salvar** para carregar o arquivo do modelo no repositório.

1. Selecione **Repositório da empresa** e **Interfaces**.

1. Selecione **Novo** e **Carregar**.

1. Selecione o arquivo `EnvironmentalSensor.interface.json` na pasta `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor`, na pasta raiz do SDK do dispositivo. Selecione **Abrir** e **Salvar** para carregar o arquivo da interface no repositório.

1. Selecione **Repositório da empresa** e, em seguida, **Cadeias de conexão**. Anote a primeira _cadeia de conexão do repositório de modelos da empresa_, pois você a usará posteriormente neste guia de início rápido.

## <a name="run-the-device-sample"></a>Executar o exemplo de dispositivo

Execute um aplicativo de exemplo no SDK para simular um dispositivo IoT Plug and Play que envia telemetria ao Hub IoT. Para executar o aplicativo de exemplo:

1. Na pasta do `cmake`, navegue até a pasta que contém o arquivo executável:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Execute o arquivo executável:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Agora, o dispositivo está pronto para receber comandos e atualizações de propriedade e começou a enviar dados telemétricos para o hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Usar a CLI do Azure IoT para validar o código

Após iniciar o exemplo de cliente do dispositivo, use a CLI do Azure para verificar se ele está funcionando.

Use o comando a seguir para exibir a telemetria que o dispositivo de exemplo está enviando. Talvez seja necessário aguardar um ou dois minutos para que o recurso exiba alguma telemetria na saída:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Use o seguinte comando para exibir as propriedades enviadas pelo dispositivo:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a um Hub IoT. Para saber mais sobre como criar uma solução que interage com os dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Instruções: Conectar-se a um dispositivo e interagir com ele](howto-develop-solution.md)
