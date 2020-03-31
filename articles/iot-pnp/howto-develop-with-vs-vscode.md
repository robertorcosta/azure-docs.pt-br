---
title: Use visual studio e visual studio code para construir dispositivos IoT Plug e Play Preview | Microsoft Docs
description: Use o Visual Studio e o Visual Studio Code para acelerar a autoria dos modelos de dispositivos IoT Plug and Play e implementar o código do dispositivo.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159227"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Use visual studio e visual studio code para construir dispositivos IoT Plug and Play

O Azure IoT Tools for Visual Studio Code fornece um ambiente integrado aos modelos de capacidade do dispositivo autor (DCM) e interfaces, publica para modelar repositórios e gerar código Esqueleto C para implementar o aplicativo do dispositivo.

Este artigo mostra como:

- Gerar código do dispositivo e projeto de aplicativo.
- Use o código gerado no projeto do seu dispositivo.
- Iterar regenerando o código do esqueleto.

Para saber mais sobre como usar o Código VS [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)para desenvolver dispositivos IoT, consulte .

## <a name="prerequisites"></a>Pré-requisitos

Instale [o Visual Studio Code](https://code.visualstudio.com/).

Use as seguintes etapas para instalar o pacote de extensão em CÓDIGO VS.

1. Em CÓDIGO VS, selecione a guia **Extensões.**
1. Procure e **instale ferramentas Azure IoT** do mercado.

## <a name="generate-device-code-and-project"></a>Gerar código e projeto do dispositivo

Em CÓDIGO VS, use **Ctrl+Shift+P** para abrir a paleta de comandos, **digite IoT Plug and Play**e selecione **Gerar código de dispositivo** para configurar o código do esqueleto e o tipo de projeto. A lista a seguir descreve cada etapa em detalhes:

- **Arquivo DCM a ser usado para gerar o código**. Para gerar o código do dispositivo esqueleto, abra a pasta que contém os arquivos de DCM e interface que ele implementa. Se o gerador de código não conseguir encontrar uma interface que um DCM exija, ele o baixa do repositório do modelo conforme necessário.

- **Linguagem de código do dispositivo**. Atualmente, o gerador de código suporta apenas ANSI C.

- **Nome do projeto**. O nome do projeto é usado como o nome da pasta para o código gerado e outros arquivos do projeto. A pasta é, por padrão, colocada ao lado do arquivo DCM. Para evitar ter que copiar manualmente a pasta de código gerada sempre que atualizar seu DCM e regenerar o código do dispositivo, mantenha seu arquivo DCM na mesma pasta que a pasta do projeto.

- **Método para se conectar ao Azure IoT**. Os arquivos gerados também contêm código para configurar o dispositivo para se conectar ao Azure IoT Hub. Você pode optar por se conectar diretamente ao [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) ou usar o [Serviço de Provisionamento de Dispositivos](https://docs.microsoft.com/azure/iot-dps).

    - **Via cadeia de conexão do dispositivo IoT Hub**: especifique a seqüência de conexão do dispositivo para que o aplicativo do dispositivo se conecte diretamente ao IoT Hub.
    - **Via chave simétrica DPS**: especifique o **Escopo de ID,** **chave simétrica** e **iD** do dispositivo para o aplicativo do dispositivo que são necessários para se conectar ao IoT Hub ou IoT Central usando DPS.

- **Tipo de projeto**. O gerador de código também gera um projeto CMake ou Arduino. Atualmente, os tipos de projeto suportados são:

    - **CMake Project no Windows**: para um projeto de dispositivo que usa [CMake](https://cmake.org/) como sistema de compilação no Windows. Essa opção `CMakeLists.txt` gera com configurações de SDK do dispositivo na mesma pasta que o código C.
    - **CMake Project no Linux**: para um projeto de dispositivo que usa [CMake](https://cmake.org/) como sistema de construção no Linux. Essa opção `CMakeLists.txt` gera com configurações de SDK do dispositivo na mesma pasta que o código C.
    - **Projeto MXChip IoT DevKit**: para um projeto de dispositivo que é executado em um dispositivo [MXChip IoT DevKit.](https://aka.ms/iot-devkit) Esta opção gera um projeto Arduino que você pode [usar no CÓDIGO VS](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) ou no Arduino IDE para construir e executar em um dispositivo IoT DevKit.

- **Tipo SDK do dispositivo**. Se você selecionar CMake como tipo de projeto, esta é a etapa para configurar como o `CMakeLists.txt`código gerado incluirá o SDK do dispositivo Azure IoT C no:

    - **Via Código Fonte**: o código gerado depende do código fonte do [dispositivo SDK](https://github.com/Azure/azure-iot-sdk-c) para incluir e construir em conjunto com ele. Isso é recomendado quando você personalizou o código fonte do dispositivo SDK.
    - **Via Vcpkg**: o código gerado depende do [dispositivo SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) para incluir e construir junto com ele. Esta é a maneira recomendada para dispositivos que executam Windows, Linux ou macOS.

    > [!NOTE]
    > suporte ao macOS para dispositivo Azure IoT C O SDK Vcpkg está funcionando em andamento.

O gerador de código tenta usar arquivos de DCM e interface localizados na pasta local. Se os arquivos de interface não estão na pasta local, o gerador de código os procura no repositório de modelos públicos ou repositório de modelos da empresa. [Os arquivos de interface comuns](./concepts-common-interfaces.md) são armazenados no repositório de modelos públicos.

Após o término da geração de código, a extensão abre uma nova janela DE CÓDIGO VS com o código. Se você abrir um arquivo gerado como **main.c,** você pode descobrir que o IntelliSense informa que ele não pode abrir os arquivos de origem do C SDK. Para habilitar a navegação de código e IntelliSense correta, use as seguintes etapas para incluir a fonte C SDK:

1. Em CÓDIGO VS, use **Ctrl+Shift+P** para abrir a paleta de comando, digite e selecione **C/C++: Editar configurações (JSON)** para abrir o arquivo **c_cpp_properties.json.**

1. Adicione o caminho do dispositivo SDK na seção: `includePath`

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Salve o arquivo.

## <a name="use-the-generated-code"></a>Use o código gerado

As instruções a seguir descrevem como usar o código gerado em seu próprio projeto de dispositivo em diferentes plataformas de máquinas de desenvolvimento. As instruções supõem que você está usando uma seqüência de conexão do dispositivo IoT Hub com o código gerado:

### <a name="linux"></a>Linux

Para construir o código do dispositivo juntamente com o dispositivo C SDK Vcpkg usando CMake em um ambiente Linux como Ubuntu ou Debian:

1. Abra uma aplicação de terminal.

1. Instale **GCC,** `cmake` **Git**e todas `apt-get` as dependências usando o comando:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Verifique se a versão do `cmake` é superior a **2.8.12** e se a versão do **GCC** é superior a **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Instalar o Vcpkg:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Em seguida, para conectar a [integração](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)em todo o usuário, execute:

    ```bash
    ./vcpkg integrate install
    ```

1. Instalar o Vcpkg do SDK do dispositivo do Azure IoT C:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Criar `cmake` um subdiretório na pasta contém o stub de código gerado e navegar até essa pasta:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para usar o CMake para construir o SDK do dispositivo e o stub de código gerado:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Após o sucesso da compilação, execute o aplicativo especificando a seqüência de conexão do dispositivo IoT Hub como parâmetro.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Para construir o código do dispositivo juntamente com o dispositivo C SDK no Windows usando CMake e os compiladores Do Visual Studio C/C++ na linha de comando, consulte o [IoT Plug and Play quickstart](./quickstart-create-pnp-device-windows.md). As etapas a seguir mostram como construir o código do dispositivo juntamente com o dispositivo C SDK Vcpkg como projeto CMake no Visual Studio.

1. Siga os passos na [partida rápida](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) para instalar o dispositivo Azure IoT SDK para C via Vcpkg.

1. Instale [o Visual Studio 2019 (Comunidade, Profissional ou Empresa)](https://visualstudio.microsoft.com/downloads/) - certifique-se de incluir o componente **do gerenciador de pacotes NuGet** e o Desenvolvimento de Desktop com carga de trabalho **C++.**

1. Abra o Visual Studio, escolha Arquivo > `CMakeLists.txt` Abra > **CMake...** para abrir o na pasta contém código gerado.

1. Na barra de ferramentas **Geral,** localize a **parada Configurações.** Selecione **Gerenciar configuração** para adicionar a configuração CMake para o seu projeto.

    ![Gerenciar configuração](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. Nas **Configurações CMake,** adicione uma nova configuração e selecione **x86-Debug** como destino.

1. Em **CMake Argumentos de comando,** adicione a seguinte linha:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Salve o arquivo.

1. Mude para **x86-Debug** na **queda de configurações.** Ele precisa de alguns segundos para o CMake gerar o cache para ele. Veja a janela Saída para ver o progresso.

    ![Saída CMake](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. No **Solution Explorer**, clique `CMakeLists.txt` com o botão direito do mouse na pasta 'na raiz' e selecione **Build** no menu de contexto para construir o stub de código gerado com o dispositivo SDK.

1. Depois que a compilação for bem sucedida, no prompt de comando, execute o aplicativo especificando a seqüência de conexão do dispositivo IoT Hub como parâmetro.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Para saber mais sobre como usar o CMake no Visual Studio, consulte [o projeto Build CMake](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

As etapas a seguir mostram como construir o código do dispositivo juntamente com o código-fonte C SDK do dispositivo no macOS usando CMake:

1. Abra a aplicação do terminal.

1. Use [o Homebrew](https://homebrew.sh) para instalar todas as dependências:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Verifique se [o CMake](https://cmake.org/) é pelo menos a versão **2.8.12**:

    ```bash
    cmake --version
    ```

1. [Patch CURL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) para a versão mais recente disponível.

1. Na pasta que contém o código gerado, clone o repositório [Azure IoT C SDK:](https://github.com/Azure/azure-iot-sdk-c)

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Essa operação deve demorar alguns minutos.

1. Crie uma `cmake` pasta chamada a pasta que contém o código gerado e navegue até essa pasta.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para usar o CMake para construir o SDK do dispositivo e o stub de código gerado:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Após o sucesso da compilação, execute o aplicativo especificando a seqüência de conexão do dispositivo IoT Hub como parâmetro.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iterado regenerando o código do esqueleto

O gerador de código pode regenerar o código se você atualizar seus arquivos DCM ou interface. Supondo que você já tenha gerado o código do dispositivo a partir de um arquivo DCM, para regenerar o código:

1. Com a pasta que contém os arquivos DCM aberta, use **Ctrl+Shift+P** para abrir a paleta de comandos, insira **IoT Plug and Play** e selecione **Gerar Stub do Código do Dispositivo**.

1. Escolha o arquivo DCM que você atualizou.

1. Selecione **Re-gerar código para {nome do projeto}**.

1. O gerador de código usa a configuração anterior que você configurou e regenera o código. No entanto, ele não sobreescreve os arquivos `main.c` que `{project_name}_impl.c`podem conter código de usuário, como e .

> [!NOTE]
> Se você atualizar o id URN em seu arquivo de interface, ele será tratado como uma nova interface. Quando você re-gera o código, o gerador de código gera código para `{project_name}_impl.c` interface, mas não sobreescreve o original no arquivo.

## <a name="problems-and-feedback"></a>Problemas e Feedback

O Azure IoT Tools é um projeto de código aberto no GitHub. Para quaisquer problemas e solicitações de recursos, você pode [criar um problema no GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Próximas etapas

Neste artigo de como fazer, você aprendeu a usar o Visual Studio e o Visual Studio Code para gerar o código Esqueleto C para implementar o aplicativo do dispositivo. Um próximo passo sugerido é aprender como instalar e usar a ferramenta [explorador Azure IoT.](./howto-install-iot-explorer.md)
