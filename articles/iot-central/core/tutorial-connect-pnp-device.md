---
title: Tutorial – Conectar um dispositivo do IoT Plug and Play (versão prévia) ao Azure IoT Central
description: Este tutorial mostra como usar um modelo de funcionalidade do dispositivo para gerar o código do dispositivo. Em seguida, execute o código do dispositivo, veja o dispositivo se conectar ao aplicativo do IoT Central e use as exibições geradas automaticamente.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: e22a9ae2888187dc877876ee5d4d4ec4ecb7c6e5
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78329435"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>Tutorial: Usar um modelo de funcionalidade do dispositivo para criar um dispositivo IoT Plug and Play (versão prévia) e conectá-lo ao aplicativo do IoT Central

Um DCM _(modelo de funcionalidade do dispositivo)_ descreve as funcionalidades de um dispositivo [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) (versão prévia). O IoT Central pode usar um DCM para criar um modelo de dispositivo e visualizações para um dispositivo quando o dispositivo se conecta pela primeira vez.

O suporte para [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) está em versão prévia e só tem suporte em regiões selecionadas.

> [!NOTE]
> Entre em contato com o suporte técnico para obter acesso para criar um aplicativo de versão prévia com suporte para dispositivos IoT Plug and Play.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Use o Visual Studio Code para criar um dispositivo IoT Plug and Play (versão prévia) usando um DCM.
> * Execute o código do dispositivo no Windows e veja ele se conectar ao aplicativo do IoT Central.
> * Exiba a telemetria simulada enviada pelo dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md) para criar um aplicativo do IoT Central usando o modelo de **App personalizado > Aplicativo personalizado**.

Para concluir este tutorial, você precisa instalar o seguinte software no computador local:

* [Ferramentas de Build para o Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) com **Ferramentas de build de C++** e cargas de trabalho do **componente do gerenciador de pacotes NuGet**. Ou se você já tem o [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 ou 2015 com as mesmas cargas de trabalho instaladas.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/) – ao instalar o **CMake**, selecione a opção **Adicionar o CMake ao CAMINHO do sistema**.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* O utilitário `dps-keygen`:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Instalar as Ferramentas do Azure IoT

Use as seguintes etapas para instalar o pacote de extensão Azure IoT Tools no VS Code:

1. No VS Code, selecione a guia **Extensões**.
1. Pesquise por **Azure IoT Tools**.
1. Selecione **Instalar**.

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste tutorial, você usa o gerenciador de biblioteca [Vcpkg](https://github.com/microsoft/vcpkg) para instalar o SDK de dispositivo C do Azure IoT em seu ambiente de desenvolvimento.

1. Abra um prompt de comando. Execute o comando a seguir para instalar o Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Em seguida, para conectar a [integração](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md) de todo o usuário, execute o comando a seguir. Na primeira vez que você executar esse comando, ele exigirá direitos administrativos:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Instalar o Vcpkg do SDK do dispositivo do Azure IoT C:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Gerar uma chave do dispositivo

Para conectar um dispositivo a um aplicativo do IoT Central, é necessário ter uma chave de dispositivo. Para gerar uma chave de dispositivo:

1. Entre no aplicativo IoT Central que você criou usando o modelo de **Aplicativo personalizado** no início rápido [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md).

1. Acesse a página **Administração** e selecione **Conexão do Dispositivo**.

1. Anote a **ID do Escopo** e a **Chave Primária** você vê ao selecionar **Exibir Chaves**. Você usará esses valores mais adiante neste tutorial.

    ![Conexão de dispositivo](./media/tutorial-connect-pnp-device/device-connection.png)

1. Abra um prompt de comando e execute o seguinte comando para gerar uma chave de dispositivo:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Anote a _chave do dispositivo_ gerada, você usará esse valor em uma etapa posterior neste tutorial.

## <a name="download-your-model"></a>Baixar o modelo

Neste tutorial, você usará o DCM público para um dispositivo MxChip IoT DevKit. Você não precisa de um dispositivo DevKit real para executar o código; neste tutorial, você compilará o código para ser executado no Windows.

1. Crie uma pasta chamada `central_app` e abra-a no VS Code.

1. Use **Ctrl+Shift+P** para abrir a paleta de comandos, insira **IoT Plug and Play** e selecione **Abrir Repositório de Modelos**. Selecione **Repositório público**. O VS Code mostra uma lista de DCMs no repositório de modelos públicos.

1. Selecione o DCM **MXChip IoT DevKit** com a ID `urn:mxchip:mxchip_iot_devkit:1`. Em seguida, selecione **Baixar**. Agora você tem uma cópia do DCM na pasta `central_app`.

![Repositório de modelos e DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Para trabalhar com o IoT Central, o modelo de funcionalidade do dispositivo precisa ter todas as interfaces definidas embutidas no mesmo arquivo.

## <a name="generate-the-c-code-stub"></a>Gerar o stub do código C

Agora que você tem o DCM **MXChip IoT DevKit** e suas interfaces associadas, gere o código do dispositivo que implementa o modelo. Para gerar o stub do código C no VS Code:

1. Com a pasta que contém os arquivos DCM aberta, use **Ctrl+Shift+P** para abrir a paleta de comandos, insira **IoT Plug and Play** e selecione **Gerar Stub do Código do Dispositivo**.

    > [!NOTE]
    > Na primeira vez que você usar o utilitário Gerador de Código do IoT Plug and Play, serão necessários alguns segundos para baixá-lo.

1. Selecione o arquivo DCM **MXChip IoT DevKit** recém-baixado.

1. Insira o nome do projeto **devkit_device**.

1. Escolha **ANSI C** como a linguagem.

1. Escolha **Por meio da chave simétrica do DPS (Serviço de Provisionamento de Dispositivos)** como o método de conexão.

1. Escolha **Projeto CMake no Windows** como seu tipo de projeto. Não escolha **Projeto MXChip IoT DevKit**; essa opção é usada quando você tem um dispositivo DevKit real.

1. Escolha **Via Vcpkg** como a maneira de incluir o SDK.

1. O VS Code abre uma nova janela com os arquivos stub do código do dispositivo gerados na pasta `devkit_device`.

![Código do dispositivo gerado](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Compilar o código

Você usará o SDK do dispositivo para criar o stub do código do dispositivo gerado. O aplicativo criado simula um dispositivo **MXChip IoT DevKit** e se conecta ao aplicativo do IoT Central. O aplicativo envia a telemetria e as propriedades e recebe comandos.

1. Em um prompt de comando, crie um subdiretório `cmake` na pasta `devkit_device` e navegue até essa pasta:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Execute os comandos a seguir para compilar o stub de código gerado. Substitua o espaço reservado `<directory of your Vcpkg repo>` pelo caminho para sua cópia do repositório **Vcpkg**:

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Se você estiver usando o Visual Studio 2017 ou 2015, precisará especificar o gerador de CMake com base nas ferramentas de build em uso:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Depois que o build for concluído com êxito, no mesmo prompt de comando, execute o aplicativo. Substitua `<scopeid>` e `<devicekey>` pelos valores que você anotou anteriormente:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. O aplicativo do dispositivo iniciará o envio de dados ao Hub IoT. Às vezes, você vê o erro `Error registering device for DPS` na primeira vez que executa o comando anterior. Se você vir esse erro, repita o comando.

## <a name="view-the-device"></a>Exibir o dispositivo

Depois que o código do dispositivo se conectar ao IoT Central, você poderá exibir as propriedades e a telemetria enviadas por ele:

1. No aplicativo do IoT Central, acesse a página **Dispositivos** e selecione o dispositivo **mxchip-01**. Este dispositivo foi adicionado automaticamente quando o código do dispositivo foi conectado:

    ![Página de visão geral](./media/tutorial-connect-pnp-device/overview-page.png)

    Após alguns minutos, essa página mostra gráficos da telemetria que estão sendo enviados pelo dispositivo.

1. Selecione a página **Sobre** para ver os valores de propriedade enviados pelo dispositivo.

1. Selecione a página **Comandos** para chamar comandos no dispositivo. Você poderá ver o dispositivo respondendo no prompt de comando que está executando o código do dispositivo.

1. Acesse a página **Modelos de dispositivo** para ver o modelo que o IoT Central criou com base no DCM no repositório público:

    ![Página Modelos de dispositivo](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a conectar um dispositivo IoT Plug and Play (versão prévia) que foi gerado de um DCM no repositório de modelos públicos.

Para saber mais sobre DCMs e como criar seus próprios modelos, prossiga para o guia de instruções:

> [!div class="nextstepaction"]
> [Definir um novo tipo de dispositivo de IoT](./howto-set-up-template.md)
