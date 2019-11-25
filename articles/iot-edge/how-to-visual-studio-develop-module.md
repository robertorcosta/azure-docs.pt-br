---
title: Develop and debug modules in Visual Studio - Azure IoT Edge | Microsoft Docs
description: Use Visual Studio 2019 to develop and debug modules for Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 2f8b0fe83e10beb3b65dca08e18b03f4fc11947e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457102"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use Visual Studio 2019 to develop and debug modules for Azure IoT Edge

Você pode transformar sua lógica de negócios em módulos do Azure IoT Edge. This article shows you how to use Visual Studio 2019 as the main tool to develop and debug modules.

As ferramentas do Azure IoT Edge para Visual Studio fornecem os seguintes benefícios:

- Criar, editar, compilar, executar e depurar soluções do Azure IoT Edge e módulos no seu computador de desenvolvimento local.
- Implantar a solução do Azure IoT Edge no dispositivo do Azure IoT Edge por meio do Hub IoT do Azure.
- Code your Azure IoT modules in C or C# while having all of the benefits of Visual Studio development.
- Gerenciar dispositivos do Azure IoT Edge e módulos com interface do usuário.

This article shows you how to use the Azure IoT Edge Tools for Visual Studio 2019 to develop your IoT Edge modules. Você também aprenderá como implantar seu projeto no dispositivo Azure IoT Edge. Currently, Visual Studio 2019 provides support for modules written in C and C#. The supported device architectures are Windows X64 and Linux X64 or ARM32. For more information about supported operating systems, languages, and architectures, see [Language and architecture support](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Pré-requisitos

Este artigo presume que você esteja usando um computador ou uma máquina virtual que executa Windows como seu computador de desenvolvimento. On Windows computers you can develop either Windows or Linux modules. To develop Windows modules, use a Windows computer running version 1809/build 17763 or newer. To develop Linux modules, use a Windows computer that meets the [requirements for Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). 

Because this article uses Visual Studio 2019 as the main development tool, install Visual Studio. Make sure you include the **Azure development** and **Desktop development with C++** workloads in your Visual Studio 2019 installation. You can [Modify Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) to add the required workloads.

After your Visual Studio 2019 is ready, you also need the following tools and components:

- Download and install [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) from the Visual Studio marketplace to create an IoT Edge project in Visual Studio 2019.

> [!TIP]
> If you are using Visual Studio 2017, please download and install [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) for VS 2017 from the Visual Studio marketplace

- Fazer o download e instalar o [Docker Community Edition](https://docs.docker.com/install/) no computador de desenvolvimento para compilar e executar suas imagens de módulo. É necessário definir o Docker CE para ser executado no modo de contêiner do Linux ou no modo de contêiner do Windows.

- Para configurar o ambiente de desenvolvimento local para depurar, executar e testar a solução IoT Edge, instale a [Ferramenta de desenvolvimento do Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Install [Python (2.7/3.6+) and Pip](https://www.python.org/) and then install the **iotedgehubdev** package by running the following command in your terminal. Certifique-se de que sua versão da ferramenta de desenvolvimento do Azure IoT EdgeHub é posterior à versão 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clone the repository and install the Vcpkg library manager, and then install the **azure-iot-sdk-c package** for Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Você pode usar um registro do Docker local, em vez de um registro em nuvem, para fins de protótipo e teste.

- Para testar o módulo em um dispositivo, é necessário um hub IoT ativo com pelo menos um dispositivo do IoT Edge. Para usar seu computador como um dispositivo do IoT Edge, siga as etapas no guia de início rápido para [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Se estiver executando o daemon do IoT Edge no computador de desenvolvimento, talvez seja necessário interromper o EdgeHub e o EdgeAgent antes de iniciar o desenvolvimento no Visual Studio.

### <a name="check-your-tools-version"></a>Verificar a versão de ferramentas

1. No menu **Ferramentas**, selecione **Extensões e atualizações**. Expanda **Instalado > Ferramentas** e você poderá encontrar as **Ferramentas do Azure IoT Edge** e o **Cloud Explorer para Visual Studio**.

1. Observe a versão instalada. Você pode comparar esta versão com a versão mais recente no Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Se a sua versão for mais antiga do que a disponível no Visual Studio Marketplace, atualize suas ferramentas no Visual Studio conforme mostrado na seção a seguir.

### <a name="update-your-tools"></a>Atualize suas ferramentas

1. Na caixa de diálogo **Extensões e Atualizações**, expanda **Atualizações > Visual Studio Marketplace**, escolha **Ferramentas do Azure IoT Edge** ou **Cloud Explorer para Visual Studio** e selecione **Atualizar**.

1. Depois de fazer o download da atualização das ferramentas, feche o Visual Studio para disparar a atualização das ferramentas usando o instalador VSIX.

1. No instalador, selecione **OK** para iniciar e depois **Modificar** para atualizar as ferramentas.

1. Depois que a atualização for concluída, selecione **Fechar** e reinicie o Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Criar um projeto do Azure IoT Edge

O modelo de projeto do Azure IoT Edge no Visual Studio cria um projeto que pode ser implantado em dispositivos do Azure IoT Edge no Hub IoT do Azure. First you create an Azure IoT Edge solution, and then you generate the first module in that solution. Cada solução do IoT Edge pode conter mais de um módulo.

> [!TIP]
> A estrutura do projeto do IoT Edge criada pelo Visual Studio não é a mesma do Visual Studio Code.

1. In Visual Studio new project dialog, search and select **Azure IoT Edge** project and click **Next**. In project configuration window, enter a name for your project and specify the location, and then select **Create**. O nome do projeto padrão é **AzureIoTEdgeApp1**.

   ![Criar um novo projeto](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. In the **Add IoT Edge Application and Module** window, select either **C# Module** or **C Module** and then specify your module name and module image repository. O Visual Studio preenche automaticamente o nome do módulo com **localhost:5000/<your module name\>** . Substitua-o pelas informações de seu registro. Se você usa um registro local do Docker para testes, **localhost** é uma opção adequada. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. The login server looks like **_\<registry name\>_ .azurecr.io**. Only replace the **localhost:5000** part of the string so that the final result looks like **\<*registry name*\>.azurecr.io/ _\<your module name\>_** . The default module name is **IotEdgeModule1**

   ![Add Application and Module](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Select **OK** to create the Azure IoT Edge solution with a module that uses either C# or C.

Now you have an **AzureIoTEdgeApp1.Linux.Amd64** project or an **AzureIoTEdgeApp1.Windows.Amd64** project, and also an **IotEdgeModule1** project in your solution. Each **AzureIoTEdgeApp1** project has a `deployment.template.json` file, which defines the modules you want to build and deploy for your IoT Edge solution, and also defines the routes between modules. The default solution has a **SimulatedTemperatureSensor** module and a **IotEdgeModule1** module. The **SimulatedTemperatureSensor** module generates simulated data to the **IotEdgeModule1** module, while the default code in the **IotEdgeModule1** module directly pipes received messages to Azure IoT Hub.

The **IotEdgeModule1** project is a .NET Core 2.1 console application if it's a C# module. Ele contém os arquivos do Docker que você precisa para seu dispositivo IoT Edge em execução com o contêiner do Windows ou do Linux. The `module.json` file describes the metadata of a module. The actual module code, which takes Azure IoT Device SDK as a dependency, is found in the `Program.cs` or `main.c` file.

## <a name="develop-your-module"></a>Desenvolver seu módulo

The default module code that comes with the solution is located at **IotEdgeModule1** > **Program.cs** (for C#) or **main.c** (C). The module and the `deployment.template.json` file are set up so that you can build the solution, push it to your container registry, and deploy it to a device to start testing without touching any code. The module is built to take input from a source (in this case, the **SimulatedTemperatureSensor** module that simulates data) and pipe it to Azure IoT Hub.

When you're ready to customize the module template with your own code, use the [Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md) to build modules that address the key needs for IoT solutions such as security, device management, and reliability.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicialize o iotedgehubdev com a cadeia de conexão do dispositivo IoT Edge

1. Copie a cadeia de conexão de qualquer dispositivo do IoT Edge da **Cadeia de conexão primária** no Gerenciador de nuvem do Visual Studio. Não copie a sequência de conexão do dispositivo não-Edge, pois o ícone d e um dispositivo IoT Edge é diferente daquele do dispositivo não-Edge.

   ![Copiar Cadeia de Conexão de Dispositivo de Borda](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Go to **Tools** > **Azure IoT Edge Tools** > **Setup IoT Edge Simulator**, paste the connection string and click **OK**.

   ![Abra a janela de cadeia de caracteres de Conexão de borda definida](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Insira a cadeia de caracteres de conexão da primeira etapa e depois selecione **Ok**.

> [!NOTE]
> Siga estas etapas apenas uma vez no computador de desenvolvimento e os resultados serão automaticamente aplicados a todas as soluções do Azure IoT Edge subsequentes. Esse procedimento pode ser seguido novamente se for necessário alterar para uma cadeia de caracteres de conexão diferente.

## <a name="build-and-debug-single-module"></a>Build and debug single module

Normalmente, queremos testar/depurar cada módulo antes de colocá-lo em execução dentro de uma solução com vários módulos.

1. Right-click **IotEdgeModule1** and select **Set as StartUp Project** from the context menu.

   ![Definir o projeto de inicialização](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Pressione **F5** ou clique no botão abaixo para executar o módulo. Pode levar entre 10&ndash;20 segundos na primeira vez.

   ![Executar módulo](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Você deverá ver um aplicativo de console .NET Core iniciado se o módulo tiver sido inicializado com êxito.

   ![Módulo em execução](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. If developing in C#, set a breakpoint in the `PipeMessage()` function in **Program.cs**; if using C, set a breakpoint in the `InputQueue1Callback()` function in **main.c**. You can then test it by sending a message by running the following command in a **Git Bash** or **WSL Bash** shell. (Não é possível executar o comando `curl` no PowerShell ou no Prompt de Comando.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Depurar módulo único](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    O ponto de interrupção deve ser disparado. Você pode assistir as variáveis na janela **Locais** do Visual Studio.

   > [!TIP]
   > Também é possível usar [PostMan](https://www.getpostman.com/) ou outras ferramentas de API para enviar mensagens em vez de `curl`.

1. Pressione **Ctrl + F5** ou clique no botão Parar para parar a depuração.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Compilar e depurar a solução do IoT Edge com vários módulos

Após concluir o desenvolvimento de módulo único, pode-se executar e depurar a solução inteira com vários módulos.

1. Add a second module to the solution by right-clicking **AzureIoTEdgeApp1** and selecting **Add** > **New IoT Edge Module**. The default name of the second module is **IotEdgeModule2** and will act as another pipe module.

1. Open the file `deployment.template.json` and you'll see **IotEdgeModule2** has been added in the **modules** section. Substitua a seção **Rotas** pelo seguinte. Se você tiver personalizado seus nomes de módulo, certifique-se de atualizar esses nomes correspondentes.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Clique com o botão direito em **AzureIoTEdgeApp1** e selecione **Definir como projeto de inicialização** no menu de contexto.

1. Defina os pontos de interrupção e pressione **F5** para executar e depurar vários módulos simultaneamente. You should see multiple .NET Core console app windows, which each window representing a different module.

   ![Depurar módulos múltiplos](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Pressione **Ctrl + F5** ou clique no botão Parar para parar a depuração.

## <a name="build-and-push-images"></a>Compilar e efetuar push de imagens

1. Certifique-se de que **AzureIoTEdgeApp1** é o seu projeto de inicialização. Selecione a configuração **Depurar** ou **Versão** para suas imagens de módulo serem construídas.

    > [!NOTE]
    > Ao escolher **Depurar**, o Visual Studio usará `Dockerfile.(amd64|windows-amd64).debug` para compilar imagens do Docker. Isso inclui o VSDBG, depurador de linha de comando do .NET Core, na imagem de contêiner durante a criação. É recomendável que você use a configuração **Versão** que usa o `Dockerfile.(amd64|windows-amd64)` sem o VSDBG para os módulos do IoT Edge prontos para produção.

1. Se estiver usando um registro privado, como o Registro de Contêiner do Azure, use o seguinte comando do Docker para se conectar a ele. Se estiver usando o registro local, você poderá [executar um registro local](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Se você estiver usando um registro privado como o Registro de Contêiner do Azure, é necessário adicionar informações de login do registro para as configurações de runtime encontradas no arquivo `deployment.template.json`. Substitua os espaços reservados com o nome real de nome de usuário do administrador ACR, senha e registro.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. Clique com o botão direito do mouse em **AzureIoTEdgeApp1** e escolha **Compilar e efetuar push da solução de Edge** para compilar e efetuar push da imagem do Docker para cada módulo.

   ![Compilar e efetuar push de imagens](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Implantar a solução

O artigo de início rápido que você usou para configurar seu dispositivo do IoT Edge, você implantou um módulo usando o portal do Azure. Você também pode implantar módulos usando o Cloud Explorer para o Visual Studio. Um manifesto de implantação já está preparado para o seu cenário, o `deployment.json` arquivo e tudo o que precisa fazer é selecionar um dispositivo para receber a implantação.

1. Abra **Cloud Explorer** clicando em **Exibir** > **Cloud Explorer**. Make sure you've logged in to Visual Studio 2019.

1. No **Cloud Explorer**, expanda sua assinatura, localize o Hub IoT do Azure e o dispositivo Azure IoT Edge que você deseja implantar.

1. Clique com o botão direito do mouse no dispositivo do IoT Edge para criar uma implantação para ele. Você precisa escolher o arquivo de manifesto de implantação sob o `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Você não deverá marcar `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Click the refresh button to see the new modules running along with the **SimulatedTemperatureSensor** module and **$edgeAgent** and **$edgeHub**.

## <a name="view-generated-data"></a>Exibir os dados gerados

1. To monitor the D2C message for a specific device, select the device in the list and then click **Start Monitoring Built-in Event Endpoint** in the **Action** window.

1. To stop monitoring data, select the device in the list and then select **Stop Monitoring Built-in Event Endpoint** in the **Action** window.

## <a name="next-steps"></a>Próximos passos

Para desenvolver módulos padronizados para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
