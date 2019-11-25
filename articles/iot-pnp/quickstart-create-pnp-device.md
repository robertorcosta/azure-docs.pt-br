---
title: Criar um dispositivo da versão prévia do IoT Plug and Play (Windows) | Microsoft Docs
description: Use um modelo de funcionalidade do dispositivo para gerar o código do dispositivo. Em seguida, execute o código do dispositivo e veja o dispositivo se conectar ao Hub IoT.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4ee9bf218765ea4c3966e7f0a8b20a8108de7655
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931901"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Início Rápido: usar um modelo de funcionalidade do dispositivo para criar um dispositivo IoT Plug and Play em versão prévia (Windows)

Um DCM _(modelo de funcionalidade do dispositivo)_ descreve as funcionalidades de um dispositivo IoT Plug and Play. Um DCM geralmente é associado a um SKU de produto. As funcionalidades definidas no DCM são organizadas em interfaces reutilizáveis. Você pode gerar um código do dispositivo de esqueleto com base em um DCM. Este início rápido mostra como usar o VS Code no Windows para criar um dispositivo IoT Plug and Play usando um DCM.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa instalar o seguinte software no computador local:

* [Ferramentas de Build para o Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) com **Ferramentas de build de C++** e cargas de trabalho do **componente do gerenciador de pacotes NuGet**. Ou se você já tem o [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 com as mesmas cargas de trabalho instaladas.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Instalar as Ferramentas do Azure IoT

Use as seguintes etapas para instalar o pacote de extensão [Azure IoT Tools para VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools):

1. No VS Code, selecione a guia **Extensões**.
1. Pesquise por **Azure IoT Tools**.
1. Selecione **Instalar**.

### <a name="install-the-azure-iot-explorer"></a>Instalar o Azure IoT Explorer

Baixe e instale a versão mais recente do **Explorador do Azure IoT** na página do [repositório](https://github.com/Azure/azure-iot-explorer/releases) da ferramenta, selecionando o arquivo .msi em "Assets" (Ativos) para obter a atualização mais recente.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Obter a cadeia de conexão para o repositório de modelos da empresa

Encontre a _cadeia de conexão do repositório de modelos da empresa_ no portal do [Azure Certified para IoT](https://preview.catalog.azureiotsolutions.com) quando entrar com uma conta corporativa ou de estudante da Microsoft ou com a ID de Parceiro da Microsoft, caso tenha uma. Depois de entrar, selecione **Repositório da empresa** e, em seguida, **Cadeias de conexão**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Preparar um hub IoT

Você também precisará de um Hub IoT do Azure em sua assinatura do Azure para concluir este início rápido. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Caso não tenha um hub IoT, siga [estas instruções para criar um](../iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Durante a versão prévia pública, os recursos de IoT Plug and Play estão disponíveis apenas em hubs IoT criados nas regiões **Centro dos EUA**, **Europa Setentrional** e **Leste do Japão**.

Execute o seguinte comando para adicionar a Extensão de IoT do Microsoft Azure para a CLI do Azure à instância do Cloud Shell:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Use o comando a seguir para criar uma identidade do dispositivo no Hub IoT. Substitua os espaços reservados **YourIoTHubName** e **YourDevice** pelos nomes reais.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDevice>
```

Execute o seguinte comando para obter a _cadeia de conexão de dispositivo_ do dispositivo que você acabou de registrar:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

Execute o seguinte comando para obter a _cadeia de conexão do hub IoT_ para o seu hub:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

### <a name="get-azure-iot-device-sdk-for-c"></a>Obter o SDK do dispositivo IoT do Azure para C

Neste início rápido, você preparará um ambiente de desenvolvimento ao instalar o SDK do dispositivo Azure IoT C por meio de [Vcpkg](https://github.com/microsoft/vcpkg).

1. Abra um prompt de comando. Execute o comando a seguir para instalar o Vcpkg:

    ```cmd/sh
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Em seguida, para conectar a [integração](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md) dos usuários, execute o seguinte (observação: requer credenciais de administrador no primeiro uso):

    ```cmd/sh
    .\vcpkg.exe integrate install
    ```

1. Instalar o Vcpkg do SDK do dispositivo do Azure IoT C:

    ```cmd/sh
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Criar o modelo

Neste início rápido, você usará um modelo de funcionalidade do dispositivo de exemplo existente e as interfaces associadas.

1. Crie o diretório `pnp_app` na unidade local. Você usará essa pasta para os arquivos de modelo de dispositivo e o stub do código do dispositivo.

1. Baixe o [os arquivos de amostra de interface e do modelo de funcionalidade do dispositivo](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) e a [amostra de interface](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) e salve os arquivos na pasta `pnp_app`.

    > [!TIP]
    > Para baixar um arquivo no GitHub, navegue até o arquivo, clique com o botão direito do mouse em **Bruto** e, em seguida, selecione **Salvar link como**.

1. Abra a pasta `pnp_app` com o VS Code. É possível exibir os arquivos com o IntelliSense:

    ![Modelo de funcionalidade do dispositivo](media/quickstart-create-pnp-device/dcm.png)

1. Nos arquivos baixados, substitua `<YOUR_COMPANY_NAME_HERE>` nos campos `@id` e `schema` por um valor exclusivo. Use somente os caracteres a-z, A-Z, 0-9 e sublinhado. Para obter mais informações, confira [Formato do identificador do Gêmeo Digital](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>Gerar o stub do código C

Agora que você tem um DCM e suas interfaces associadas, é possível gerar o código do dispositivo que implementa o modelo. Para gerar o stub do código C no VS Code:

1. Com a pasta `pnp_app` aberta no VS Code, use o comando **Ctrl+Shift+P** para abrir a paleta de comandos, digite **IoT Plug and Play** e selecione **Gerar Stub do Código do Dispositivo**.

    > [!NOTE]
    > Na primeira vez que você usar a CLI do CodeGen do IoT Plug and Play, serão necessários alguns segundos para baixá-la e instalá-la automaticamente.

1. Escolha o arquivo **SampleDevice.capabilitymodel.json** a ser usado para gerar o stub do código do dispositivo.

1. Insira o nome do projeto **sample_device**. Esse será o nome do aplicativo do dispositivo.

1. Escolha **ANSI C** como a linguagem.

1. Escolha **Por meio da cadeia de conexão de dispositivo do Hub IoT** como o método de conexão.

1. Escolha **Projeto CMake no Windows** como modelo do projeto.

1. Escolha **Via Vcpkg** como a maneira de incluir o SDK do dispositivo.

1. A nova pasta **sample_device** é criada no mesmo local do arquivo DCM, no qual são gerados os arquivos stub de código de dispositivo. O VS Code abre uma nova janela para exibi-los.
    ![Código do dispositivo](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>Criar e executar o código

Use o código-fonte do SDK do dispositivo para criar o stub do código do dispositivo gerado. O aplicativo criado simula um dispositivo que se conecta a um Hub IoT. O aplicativo envia a telemetria e as propriedades e recebe comandos.

1. Crie o subdiretório `cmake` na pasta `sample_device` e navegue até essa pasta:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para compilar o stub de código gerado (substituindo o espaço reservado pelo diretório do seu repositório Vcpkg):

    ```cmd\sh
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Se você estiver usando o Visual Studio 2017 ou 2015, precisará especificar o gerador de CMake com base nas ferramentas de compilação em uso:
    >```cmd\sh
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Se o CMake não conseguir localizar o compilador C++, você obterá erros de build ao executar o comando anterior. Se isso acontecer, tente executar este comando no [prompt de comando do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Depois que o build for concluído com êxito, execute o aplicativo passando a cadeia de conexão do dispositivo do hub IoT como parâmetro.

    ```cmd\sh
    .\Debug\sample_device.exe "<device connection string>"
    ```

1. O aplicativo do dispositivo iniciará o envio de dados ao Hub IoT.

    ![Aplicativo do dispositivo em execução](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Validar o código

### <a name="publish-device-model-files-to-model-repository"></a>Publicar arquivos de modelo do dispositivo no repositório de modelos

Para validar o código do dispositivo com o **Azure IoT Explorer**, você precisará publicar os arquivos no repositório de modelos.

1. Com a pasta `pnp_app` aberta no VS Code, use o comando **Ctrl+Shift+P** para abrir a paleta de comandos, digite e selecione **IoT Plug and Play: Enviar arquivos para o Repositório de Modelos**.

1. Selecione os arquivos `SampleDevice.capabilitymodel.json` e `EnvironmentalSensor.interface.json`.

1. Insira a cadeia de conexão do repositório de modelos da empresa.

    > [!NOTE]
    > A cadeia de conexão só é necessária na primeira vez que você se conecta ao repositório.

1. Na janela de resultados e na notificação do VS Code, verifique se os arquivos foram publicados com êxito.

    > [!NOTE]
    > Se você receber erros ao publicar os arquivos de modelo do dispositivo, tente usar o comando **IoT Plug and Play: Sair do Repositório de Modelos** para sair e passar pelas etapas novamente.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

1. Abra o Explorador de IoT do Azure. Você verá a página **Configurações do aplicativo**.

1. Insira a _cadeia de conexão do Hub IoT_ e selecione **Conectar**.

1. Depois de se conectar, você verá a página de visão geral do dispositivo.

1. Para adicionar o repositório de sua empresa, selecione **Configurações**, **+ Adicionar fonte de definição do módulo** e, em seguida, **Repositório da empresa**. Adicione a cadeia de conexão do repositório de modelos da empresa e selecione **Salvar e Conectar**.

1. Na página de visão geral do dispositivo, localize a identidade do dispositivo criada anteriormente e selecione-a para exibir mais detalhes.

1. Expanda a interface com a ID **urn:<NOME_SUA_INTERFACE>:EnvironmentalSensor:1** para ver os primitivos do IoT Plug and Play, como propriedades, comandos e telemetria. O nome da interface que aparecerá é o nome que você colocou ao criar seu modelo.

1. Selecione a página **Telemetria** e pressione _Iniciar_ para exibir os dados telemétricos que o dispositivo está enviando.

1. Selecione a página **Propriedades (não graváveis)** para exibir as propriedades não graváveis relatadas pelo dispositivo.

1. Selecione a página **Propriedades (graváveis)** para exibir as propriedades graváveis que podem ser atualizadas.

1. Expanda o **nome** da propriedade, atualize-o com um novo nome e selecione **Atualizar propriedade gravável**.

1. Para ver o novo nome exibido na coluna **Propriedade Relatada**, selecione o botão **Atualizar** na parte superior da página.

1. Selecione a página **Comandos** para exibir todos os comandos compatíveis com o dispositivo.

1. Expanda o comando **piscar** e defina um novo intervalo de tempo de intermitência. Selecione **Enviar comando** para chamar o comando no dispositivo.

1. Vá para o prompt de comando do dispositivo simulado e leia as mensagens de confirmação impressas a fim de verificar se os comandos foram executados conforme esperado.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar um dispositivo IoT Plug and Play usando um DCM.

Para saber mais sobre DCMs e como criar modelos personalizados, avance para o tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Criar e testar um modelo de funcionalidade do dispositivo usando o Visual Studio Code](tutorial-pnp-visual-studio-code.md)
