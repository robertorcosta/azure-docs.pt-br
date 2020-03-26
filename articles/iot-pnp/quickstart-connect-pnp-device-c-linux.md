---
title: Conectar o código do dispositivo de exemplo da versão prévia do IoT Plug and Play ao Hub IoT (Linux) | Microsoft Docs
description: Criar e executar um código do dispositivo de exemplo da versão prévia do IoT Plug and Play que se conecta a um Hub IoT. Use a CLI do Azure para exibir as informações enviadas pelo dispositivo ao hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75531242"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Início Rápido: Conectar um aplicativo do dispositivo de exemplo da versão prévia do IoT Plug and Play com Linux ao Hub IoT (Linux para C)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

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

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

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
