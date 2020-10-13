---
title: Provisionar com chaves simétricas-Azure IoT Edge | Microsoft Docs
description: Após a instalação, provisione um dispositivo IoT Edge com chaves simétricas usando sua cadeia de conexão e autentique no Hub IoT
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 9e288bcbebe4118bfc8cfa7cff46c79d7075555a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979375"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>Configurar um dispositivo Azure IoT Edge com autenticação de chave simétrica

Este artigo fornece as etapas para registrar um novo dispositivo IoT Edge no Hub IoT e configurar o dispositivo para autenticar com chaves simétricas.

As etapas neste artigo percorrem um processo chamado provisionamento manual, no qual você conecta cada dispositivo ao Hub IoT manualmente. A alternativa é o provisionamento automático usando o serviço de provisionamento de dispositivos no Hub IoT, que é útil quando você tem muitos dispositivos para provisionar.

<!--TODO: Add auto-provision info/links-->

Para o provisionamento manual, você tem duas opções para autenticar dispositivos IoT Edge:

* **Chave simétrica**: quando você cria uma nova identidade de dispositivo no Hub IOT, o serviço cria duas chaves. Você coloca uma das chaves no dispositivo e apresenta a chave para o Hub IoT durante a autenticação.

  Esse método de autenticação é mais rápido para começar, mas não tão seguro.

* **X. 509 auto-assinado**: você cria dois certificados de identidade x. 509 e os coloca no dispositivo. Ao criar uma nova identidade de dispositivo no Hub IoT, você fornece impressões digitais de ambos os certificados. Quando o dispositivo é autenticado no Hub IoT, ele apresenta seus certificados e o Hub IoT pode verificar se eles correspondem às impressões digitais.

  Esse método de autenticação é mais seguro e recomendado para cenários de produção.

Este artigo percorre o processo de registro e provisionamento com a autenticação de chave simétrica. Se você quiser saber como configurar um dispositivo com certificados X. 509, confira [configurar um dispositivo Azure IOT Edge com a autenticação de certificado x. 509](how-to-manual-provision-x509.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as etapas neste artigo, você deve ter um dispositivo com o IoT Edge Runtime instalado. Caso contrário, siga as etapas em [instalar ou desinstalar o Azure IOT Edge Runtime](how-to-install-iot-edge.md).

## <a name="register-a-new-device"></a>Registrar um novo dispositivo

Cada dispositivo que se conecta a um hub IoT tem uma ID de dispositivo que é usada para controlar as comunicações da nuvem para o dispositivo ou do dispositivo para a nuvem. Você configura um dispositivo com suas informações de conexão, o que inclui o nome de host do Hub IoT, a ID do dispositivo e as informações que o dispositivo usa para autenticar no Hub IoT.

Para a autenticação de chave simétrica, essas informações são coletadas em uma *cadeia de conexão* que você pode recuperar do Hub IOT e, em seguida, colocá-las em seu dispositivo IOT Edge.

Você pode usar várias ferramentas para registrar um novo dispositivo IoT Edge no Hub IoT e recuperar sua cadeia de conexão, dependendo de sua preferência.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Pré-requisitos do portal do Azure

Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou padrão na assinatura do Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Criar um dispositivo do IoT Edge no portal do Azure

No hub IoT do portal do Azure, os dispositivos do IoT Edge são criados e gerenciados separadamente dos dispositivos de IoT que não são habilitados para a borda.

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.

1. No painel esquerdo, selecione **IOT Edge** no menu e, em seguida, selecione **Adicionar um dispositivo de IOT Edge**.

   ![Adicionar um dispositivo de IoT Edge da portal do Azure](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Na página **criar um dispositivo** , forneça as seguintes informações:

   * Crie uma ID de dispositivo descritiva.
   * Selecione **Chave simétrica** como o tipo de autenticação.
   * Use as configurações padrão para gerar automaticamente as chaves de autenticação e conectar o novo dispositivo ao hub.

1. Clique em **Salvar**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Exibir dispositivos do IoT Edge no portal do Azure

Todos os habilitado borda dispositivos que se conectam ao seu hub IoT são listados no **IoT Edge** página.

![Usar o portal do Azure para exibir todos os dispositivos de IoT Edge em seu hub IoT](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recuperar a cadeia de conexão no portal do Azure

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT.

1. Na página **IoT Edge** no portal, clique na ID de dispositivo na lista de dispositivos do IoT Edge.
2. Copie o valor da **Cadeia de conexão primária** ou **Cadeia de conexão secundária**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>Pré-requisitos do Visual Studio Code

* Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou Standard em sua assinatura do Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas de IoT do Microsoft Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

É possível usar as extensões do Azure IoT para Visual Studio Code para executar operações com o Hub IoT. Para que essas operações funcionem, é necessário entrar na conta do Azure e selecionar o Hub IoT.

1. No Visual Studio Code, abra a exibição do **Explorer**.
1. Na parte inferior do Explorer, expanda a seção **Hub IoT**.

   ![Expanda a seção de Dispositivos do Azure Hub IoT](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

1. Clique em **...** no cabeçalho da seção **Hub IoT**. Se você não visualizar as reticências, clique ou passe o mouse sobre o cabeçalho.
1. Escolha **Selecionar Hub IoT**.
1. Se não estiver conectado à conta do Azure, siga as instruções para fazer isso.
1. Selecione sua assinatura do Azure.
1. Selecione seu Hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Criar um dispositivo do IoT Edge com o Visual Studio Code

1. No explorador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**.
1. Clique em **...** no cabeçalho da seção **Dispositivos do Hub IoT**. Se você não visualizar as reticências, clique ou passe o mouse sobre o cabeçalho.
1. Selecione **Criar dispositivo IoT Edge**.
1. Na caixa de texto que abrir, dê a seu dispositivo um ID.

Na tela de saída, você verá o resultado do comando. A informação do dispositivo é impressa, o que inclui o **deviceId** que você forneceu e o **connectionString** que você consegue usar para conectar seu dispositivo físico ao Hub IoT.

Na tela de saída, você verá o resultado do comando. A informação do dispositivo é impressa, o que inclui o **deviceId** que você forneceu e o **connectionString** que você consegue usar para conectar seu dispositivo físico ao Hub IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Exibir dispositivos do IoT Edge com o Visual Studio Code

Todos os dispositivos que conectam ao seu Hub IoT estão listados na seção **Azure IoT Hub** do Visual Studio Code Explorer. Os dispositivos do IoT Edge são distinguidos dos dispositivos que não são de borda por um ícone diferente e pelo fato de que os módulos **$edgeAgent** e **$edgeHub** são implantados em cada dispositivo do IoT Edge.

![Usar VS Code para exibir todos os dispositivos de IoT Edge em seu hub IoT](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recuperar a cadeia de conexão com o Visual Studio Code

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT.

1. Clique com o botão direito na ID do seu dispositivo na seção **Hub IoT**.
1. Selecione **Copiar cadeia de conexão de dispositivo**.

   A cadeia de conexão é copiada à sua área de transferência.

Você também pode selecionar **Pegar Informação do Dispositivo** do menu de botão direito para ver todas as informações do dispositivo, incluindo a cadeia de conexão, na janela de saída.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Pré-requisitos da CLI do Azure

* Um [Hub IoT](../iot-hub/iot-hub-create-using-cli.md) na assinatura do Azure.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, sua versão de CLI do Azure deve ser 2.0.70 ou mais recente. Use `az --version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack.
* A [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Criar um dispositivo do IoT Edge com a CLI do Azure

Use o comando [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) para criar uma nova identidade de dispositivo no hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* `--device-id` ou `-d` : forneça um nome descritivo que seja exclusivo para o Hub IOT.
* `hub-name` ou `-n` : forneça o nome do seu hub IOT.
* `--edge-enabled` ou `--ee` : declare que o dispositivo é um dispositivo IOT Edge.

   ![Saída da criação de identidade do dispositivo de hub IoT do Azure](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Exibir dispositivos do IoT Edge com a CLI do Azure

Use o comando [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) para exibir todos os dispositivos no hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo registrado como um dispositivo do IoT Edge terá a propriedade **capabilities.iotEdge** definida para **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recuperar a cadeia de conexão com a CLI do Azure

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT. Use o comando [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) para retornar a cadeia de conexão para um único dispositivo:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

O valor para o parâmetro `device-id` diferencia maiúsculas de minúsculas. Não copie as aspas em torno da cadeia de caracteres de conexão.

---

## <a name="provision-an-iot-edge-device"></a>Provisionar um dispositivo IoT Edge

Depois que o dispositivo IoT Edge tiver uma identidade no Hub IoT e uma cadeia de conexão que possa usar para autenticação, você precisará provisionar o próprio dispositivo com essas informações.

Em um dispositivo Linux, você fornece a cadeia de conexão editando um arquivo config. YAML. Em um dispositivo Windows, você fornece a cadeia de conexão executando um script do PowerShell.

# <a name="linux"></a>[Linux](#tab/linux)

No dispositivo IoT Edge, abra o arquivo de configuração.

```bash
sudo nano /etc/iotedge/config.yaml
```

Localize as configurações de provisionamento do arquivo e remova a marca de comentário da **configuração de provisionamento manual usando uma seção de cadeia de conexão** . 

Atualizar o valor de **device_connection_string** com a cadeia de caracteres de conexão do dispositivo IoT Edge. Verifique se qualquer outra seção de provisionamento foi comentada. Verifique se o **provisionamento:** linha não tem espaço em branco precedente e se os itens aninhados são recuados em dois espaços.

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

Para colar o conteúdo da área de transferência no nano `Shift+Right Click` ou pressione `Shift+Insert` .

Salve e feche o arquivo.

   `CTRL + X`, `Y`, `Enter`

Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. No dispositivo IoT Edge, execute o PowerShell como administrador.

2. Use o comando [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) para configurar o tempo de execução de IOT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Se você estiver usando contêineres do Linux, adicione o `-ContainerOs` parâmetro ao sinalizador. Seja consistente com a opção de contêiner que você escolheu com o `Deploy-IoTEdge` comando que você executou anteriormente.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * Se você baixou o script IoTEdgeSecurityDaemon.ps1 em seu dispositivo para uma instalação de versão específica ou offline, certifique-se de fazer referência à cópia local do script.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Quando solicitado, forneça a cadeia de conexão do dispositivo que você recuperou na seção anterior. A cadeia de conexão do dispositivo associa o dispositivo físico a uma ID do dispositivo no Hub IoT e fornece informações de autenticação.

   A cadeia de conexão do dispositivo usa o seguinte formato e não deve incluir aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

Ao provisionar um dispositivo manualmente, você pode usar parâmetros adicionais para modificar o processo, incluindo:

* Tráfego direto para passar por um servidor proxy
* Declare uma imagem de contêiner edgeAgent específica e forneça credenciais se ela estiver em um registro privado

Para obter mais informações sobre esses parâmetros adicionais, consulte [scripts do PowerShell para IOT Edge no Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Próximas etapas

Continue a [implantar módulos IOT Edge](how-to-deploy-modules-portal.md) para aprender a implantar módulos em seu dispositivo.
