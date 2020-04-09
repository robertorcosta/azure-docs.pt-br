---
title: Conecte o IoT DevKit AZ3166 a um Hub IoT azure
description: Neste tutorial, aprenda a configurar e conectar o Kit de Desenvolvimento da IoT AZ3166 ao Hub IoT do Azure para enviar dados para a plataforma de nuvem do Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 326b79e1aca6fa82b3275249401c755428a8b71d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878554"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Como conectar o IoT DevKit AZ3166 ao Hub IoT do Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Você pode usar o [Kit de Desenvolvimento da IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) para desenvolver e criar protótipos de soluções de Internet das Coisas (IoT) que usufruem dos serviços do Microsoft Azure. Inclui uma placa compatível com Arduino com periféricos e sensores ricos, um pacote de placa de código aberto e uma rica [galeria de amostras.](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)

## <a name="what-you-learn"></a>O que você aprenderá

* Como criar um Hub IoT e registrar um dispositivo para o Kit de Desenvolvimento da IoT MXChip.
* Como conectar o IoT DevKit ao Wi-Fi e configurar a cadeia de conexão IoT Hub.
* Como enviar os dados de telemetria do sensor DevKit para o seu hub de IoT.
* Como preparar o ambiente de desenvolvimento e desenvolver a aplicação para o IoT DevKit.

Você ainda não tem um DevKit? Experimente o [simulador DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) ou [compre um kit de desenvolvimento](https://aka.ms/iot-devkit-purchase).

Você pode encontrar o código-fonte para todos os tutoriais do DevKit da [galeria amostras](https://docs.microsoft.com/samples/browse/?term=mxchip)de código .

## <a name="what-you-need"></a>O que você precisa

* Uma placa MXChip IoT DevKit com um cabo Micro-USB. [Obtenha agora](https://aka.ms/iot-devkit-purchase).
* Um computador com Windows 10, macOS 10.10+ ou Ubuntu 18.04+.
* Uma assinatura ativa do Azure. [Ative uma conta de avaliação do Microsoft Azure por 30 dias](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Prepare seu hardware

Conecte o hardware a seguir no seu computador:

* Placa DevKit
* Cabo micro USB

![Requisitos de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Conecte o Kit de Desenvolvimento ao computador, siga essas etapas:

1. Conecte a extremidade USB ao computador.

2. Conecte a extremidade micro USB ao Kit de Desenvolvimento.

3. O LED verde perto do botão liga/desliga confirma a conexão.

   ![Conexões de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Quickstart: Envie telemetria do DevKit para um Hub ioT

O quickstart usa firmware DevKit pré-compilado para enviar a telemetria para o IoT Hub. Antes de executá-lo, você cria um hub de IoT e registra um dispositivo com o hub.

### <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o Hub IoT.

   **MyNodeDevice**: O nome do dispositivo que você está registrando. Use **MyNodeDevice** como mostrado. Se você escolher um nome diferente para seu dispositivo, será necessário usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Se você tiver `device-identity`um erro em execução, instale a [extensão Azure IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Execute o comando a seguir para adicionar a Extensão do Microsoft Azure IoT para a CLI do Azure à instância do Cloud Shell. A Extensão IoT adiciona comandos específicos ao IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) para o Azure CLI.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Execute os seguintes comandos no Azure Cloud Shell para obter a _seqüência de conexão do dispositivo_ para o dispositivo que você acabou de registrar:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o Hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

### <a name="send-devkit-telemetry"></a>Enviar telemetria DevKit

O DevKit conecta-se a um ponto final específico do dispositivo em seu hub de IoT e envia telemetria de temperatura e umidade.

1. Baixe a versão mais recente do [firmware GetStarted](https://aka.ms/devkit/prod/getstarted/latest) para IoT DevKit.

1. Certifique-se de que o IoT DevKit se conecte ao seu computador via USB. Abrir o File Explorer há um dispositivo de armazenamento em massa USB chamado **AZ3166**.

    ![Abra o Windows Explorer](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Arraste e solte o firmware apenas baixado para o dispositivo de armazenamento em massa e ele piscará automaticamente.

    ![Copiar firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. No DevKit, segure o botão **B,** pressione e solte o botão **Reset** e, em seguida, solte o botão **B**. Seu DevKit entra no modo AP. Para confirmar, a tela exibe o identificador do conjunto de serviços (SSID) do DevKit e o endereço IP do portal de configuração.

    ![Botão de reinicialização, botão B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Definir o modo AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Use um navegador da Web em um dispositivo habilitado para Wi-Fi diferente (computador ou celular) para se conectar ao IoT DevKit SSID exibido na etapa anterior. Se ele pedir uma senha, deixe-a vazia.

    ![Conectar SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Abra **192.168.0.1** no navegador. Selecione o Wi-Fi ao que deseja que o IoT DevKit se conecte, digite a senha wi-fi e cole a seqüência de conexão do dispositivo que você fez nota anteriormente. Em seguida, clique em Salvar.

    ![UI de configuração](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > O IoT DevKit só suporta rede de 2.4GHz. Confira as [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) para obter mais detalhes.

1. A seqüência de informações WiFi e conexão do dispositivo será armazenada no IoT DevKit quando você vir a página de resultados.

    ![Resultado de configuração](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Após a configuração do Wi-Fi, suas credenciais persistirão no dispositivo para essa conexão, mesmo que o dispositivo esteja desconectado.

1. O IoT DevKit é reiniciado em poucos segundos. Na tela de DevKit, você vê o endereço IP do DevKit seguido pelos dados de telemetria, incluindo valor de temperatura e umidade com contagem de mensagens enviadas para o Azure IoT Hub.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Enviar dados](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Para verificar os dados de telemetria enviados ao Azure, execute o seguinte comando no Azure Cloud Shell:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Siga estas etapas para preparar o ambiente de desenvolvimento para o DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Instale o Visual Studio Code com o pacote de extensão Azure IoT Tools

1. Instale [o Arduino IDE](https://www.arduino.cc/en/Main/Software). Ele fornece a cadeia de ferramentas necessária para compilar e carregar o código do Arduino.
    * **Windows**: use a versão do Windows Installer. Não instale na App Store.
    * **macOS**: arraste e solte o **Arduino.app** extraído na pasta `/Applications`.
    * **Ubuntu**: descompacte-o na pasta, como `$HOME/Downloads/arduino-1.8.8`

2. Instale [o Visual Studio Code](https://code.visualstudio.com/), um editor de código-fonte de plataforma cruzada com poderoso suporte a intellisense, conclusão de código e depuração, bem como extensões ricas podem ser instaladas a partir do marketplace.

3. Inicie o VS Code, procure o **Arduino** no marketplace de extensões e instale-o. Essa extensão fornece experiências aprimoradas para desenvolvimento na plataforma do Arduino.

    ![Instalar Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Procure as [Ferramentas do Azure IoT](https://aka.ms/azure-iot-tools) no marketplace de extensões e instale-as.

    ![Instalar as Ferramentas do Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Ou use este link direto:
    > [!div class="nextstepaction"]
    > [Instale o pacote de extensão Azure IoT Tools](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > O pacote de extensão Azure IoT Tools contém o [Azure IoT Device Workbench,](https://aka.ms/iot-workbench) que é usado para desenvolver e depurar em vários dispositivos de devkit IoT. A [extensão Azure IoT Hub](https://aka.ms/iot-toolkit), também incluída no pacote de extensão Azure IoT Tools, é usada para gerenciar e interagir com os Hubs Azure IoT.

5. Configurar o VS Code com configurações do Arduino.

    Em Visual Studio Code, clique em **Configurações > preferências de arquivo >** (no macOS, **preferências de código > > configurações).** Em seguida, clique no ícone **Configurações abertas (JSON)** no canto superior direito da página *Configurações.*

    ![Instalar as Ferramentas do Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Adicione as seguintes linhas para configurar o Arduino, dependendo de sua plataforma: 

    * **Janelas:**

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS:**

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        Substitua o espaço reservado **{username}** abaixo por seu nome de usuário.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Arduino: Gerenciador de Placas**. Pesquise **AZ3166** e instale a versão mais recente.

    ![Instalar o SDK do Kit de Desenvolvimento](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Instalar drivers do ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) é a interface USB que o kit de desenvolvimento de IoT usa para comunicar-se com seu computador de desenvolvimento. Você precisa instalá-lo no Windows para mostrar o código do dispositivo compilado no DevKit. Siga as etapas específicas do sistema operacional para permitir o acesso do computador ao dispositivo.

* **Windows**: baixe e instale o driver USB do [site STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: não é necessário nenhum driver para o macOS.
* **Ubuntu**: Execute os comandos no terminal e faça login e faça login para que a mudança de grupo entre em vigor:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Agora a preparação e a configuração do ambiente de desenvolvimento estão concluídas. Vamos construir a amostra GetStarted que você acabou de executar.

## <a name="build-your-first-project"></a>Criar seu primeiro projeto

### <a name="open-sample-code-from-sample-gallery"></a>Abra o código de amostra da galeria de amostras

O IoT DevKit contém uma rica galeria de amostras que você pode usar para aprender conectar o DevKit a vários serviços do Azure.

1. Verifique se o kit de desenvolvimento de IoT **não está conectado** ao computador. Inicie o VS Code primeiro e, em seguida, conecte o kit de desenvolvimento ao computador.

1. Clique `F1` para abrir a paleta de comando, digite e selecione **Azure IoT Device Workbench: Open Examples...**. Em seguida, selecione **IoT DevKit** como placa.

1. Na página de Exemplos do IoT Workbench, localize **Introdução** e clique em **Abrir Exemplo**. Em seguida, selecione o caminho padrão para baixar o código de exemplo.

    ![Abrir exemplo](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Provisão Azure IoT Hub e dispositivo

Em vez de provisionar o Azure IoT Hub e o dispositivo do portal Azure, você pode fazê-lo no Código VS sem sair do ambiente de desenvolvimento.

1. Na nova janela de `F1` projeto aberta, clique para abrir a paleta de comando, digite e selecione **Azure IoT Device Workbench: Provision Azure Services...**. Siga o guia passo a passo para terminar o provisionamento do seu Hub IoT Do Azure e criar o dispositivo IoT Hub.

    ![Comando de provisão](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Se você não assinou no Azure. Siga a notificação pop-up para fazer login.

1. Selecione a assinatura que deseja usar.

    ![Selecionar sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Em seguida, selecione ou crie um novo [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).

    ![Escolha o grupo de recursos](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. No grupo de recursos especificado, siga o guia para selecionar ou criar um novo Hub IoT do Azure.

    ![Selecione etapas do IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Selecione o IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Hub iot selecionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Na janela de saída, você verá o Azure IoT Hub provisionado.

    ![Hub IoT provisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selecione ou crie um novo dispositivo no Azure IoT Hub que você provisionou.

    ![Selecione as etapas do dispositivo IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selecione dispositivo IoT provisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Agora você tem o Azure IoT Hub provisionado e dispositivo criado nele. Além disso, a seqüência de conexão do dispositivo será salva em VS Code para configurar o IoT DevKit mais tarde.

    ![Provisão feita](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Configurar e compilar o código do dispositivo

1. Na barra de status inferior direita, verifique se **MXCHIP AZ3166** é mostrado como a placa selecionada e se a porta serial com **STMicroelectronics** está sendo usada.

    ![Selecionar a placa e o COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Clique `F1` para abrir a paleta de comando, digite e selecione **Azure IoT Device Workbench: Configure Configurações do dispositivo...** e selecione **Config Device Connection String > Select IoT Hub Device Connection String**.

1. No DevKit, segure o **botão A,** pressione e solte o botão **de reset** e, em seguida, solte **o botão A**. Seu DevKit entra no modo de configuração e salva a seqüência de conexões.

    ![Cadeia de conexão](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Clique `F1` novamente, digite e selecione **Azure IoT Device Workbench: Upload Device Code**. Ele começa a compilar e carregar o código para o Kit de desenvolvimento.

    ![Upload do Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

O Kit de Desenvolvimento reinicia e começa a execução do código.

> [!NOTE]
> Sempre que há erros ou interrupções, é possível recuperar executando o comando novamente.

## <a name="test-the-project"></a>Teste do projeto

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Exibir a telemetria enviada ao Hub IoT do Azure

Clique no ícone de tomada na barra de status para abrir o Monitor Serial:

![Monitor serial](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

O aplicativo de exemplo é executado com êxito quando você vê os seguintes resultados:

* O Monitor de Serial exibe a mensagem enviada ao Hub IoT.
* O LED no Kit de Desenvolvimento de IoT MXChip está piscando.

![Saída do monitor de serial](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Exibir a telemetria recebida pelo Hub IoT do Azure

Você pode usar as [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para monitorar mensagens D2C (do dispositivo para a nuvem) no Hub IoT.

1. Entre no [portal do Azure](https://portal.azure.com/) e encontre o Hub IoT que você criou.

    ![Portal do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. No painel **Políticas de acesso compartilhado**, clique na **política iothubowner** e anote a cadeia de conexão do Hub IoT.

    ![Cadeia de conexão do Hub IoT do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. Em VS Code, clique, `F1`digite e selecione **Azure IoT Hub: Set IoT Hub Connection String**. Copie a cadeia de conexão.

    ![Defina a cadeia de conexão do Hub IoT do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Expanda o painel **Dispositivos De HUB IOT AZURE** à direita, clique com o botão direito do mouse no nome do dispositivo que você criou e selecione Iniciar monitoramento de **ponto final de evento incorporado**.

    ![Monitorar a mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. No painel **SAÍDA**, veja as mensagens D2C de entrada do Hub IoT.

    ![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Examine o código

O `GetStarted.ino` é o principal arquivo de esboço de Arduino.

![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Para ver como a telemetria do dispositivo é enviada para `utility.cpp` o Azure IoT Hub, abra o arquivo na mesma pasta. Veja [a Referência da API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) para aprender a usar sensores e periféricos no IoT DevKit.

O `DevKitMQTTClient` usado é um invólucro do **iothub_client** dos [SDKs e bibliotecas Microsoft Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) interagir com o Azure IoT Hub.

## <a name="problems-and-feedback"></a>Comentários e problemas

Se houver problemas, procure uma solução nas [Perguntas frequentes sobre o kit de desenvolvimento de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou fale conosco por meio do [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Você também pode nos fornecer comentários fazendo-os nesta página.

## <a name="next-steps"></a>Próximas etapas

Você conectou um Kit de Desenvolvimento de IoT MXChip ao seu Hub IoT e enviou os dados do sensor de captura ao seu Kit de Desenvolvimento de IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
