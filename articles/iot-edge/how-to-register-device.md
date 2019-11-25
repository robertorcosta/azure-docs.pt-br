---
title: Register a new Azure IoT Edge device | Microsoft Docs
description: Use a extensão de IoT para a CLI do Azure para registrar um novo dispositivo IoT Edge e recuperar a cadeia de conexão
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/21/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 793ddcb9f218248c396e10f23201dfe905545ceb
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456858"
---
# <a name="register-an-azure-iot-edge-device"></a>Register an Azure IoT Edge device

Before you can use your IoT devices with Azure IoT Edge, you must register them with your IoT hub. Once a device is registered, you can retrieve a connection string to set up your device for IoT Edge workloads.

You have the choice of registering by using one of the following tools:

* [Azure portal](https://portal.azure.com) provides a graphical user interface to create, view, and manage Azure resources.
* [Visual Studio Code](https://code.visualstudio.com/) is a source-code editor. Azure IoT extensions make it easy to manage IoT resources from the same tool where you're developing IoT solutions.
* [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is a command-line tool for managing Azure resources. Its reusable commands are helpful for automating tasks.

## <a name="register-in-the-azure-portal"></a>Register in the Azure portal

You can perform all registration tasks in the Azure portal.

### <a name="prerequisites-for-the-azure-portal"></a>Prerequisites for the Azure portal

A free or standard [IoT hub](../iot-hub/iot-hub-create-through-portal.md) in your Azure subscription.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Create an IoT Edge device in the Azure portal

In your IoT Hub in the Azure portal, IoT Edge devices are created and managed separately from IOT devices that are not edge enabled.

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
2. In the left pane, select **IoT Edge** from the menu.
3. Selecione **Adicionar um dispositivo IoT Edge**.
4. Forneça um ID de dispositivo descritivo. Use the default settings to auto-generate authentication keys and connect the new device to your hub.
5. Clique em **Salvar**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>View IoT Edge devices in the Azure portal

Todos os habilitado borda dispositivos que se conectam ao seu hub IoT são listados no **IoT Edge** página.

![Exibir todos os dispositivos do IoT Edge no hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Retrieve the connection string in the Azure portal

Quando você estiver pronto para configurar o seu dispositivo, você precisará de uma cadeia de caracteres de conexão que conecte o seu dispositivo físico com a sua identidade no Hub IoT.

1. From the **IoT Edge** page in the portal, click on the device ID from the list of IoT Edge devices.
2. Copie o valor da **Cadeia de conexão (chave primária)** ou **Cadeia de conexão (chave secundária)** .

## <a name="register-with-visual-studio-code"></a>Register with Visual Studio Code

Há múltiplas maneiras de fazer mais operações no VS Code. This article uses the Explorer, but you can also use the Command Palette to run the steps.

### <a name="prerequisites-for-visual-studio-code"></a>Prerequisites for Visual Studio Code

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas de IoT do Microsoft Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

You can use the Azure IoT extensions for Visual Studio Code to perform operations with your IoT Hub. For these operations to work, you need to sign in to your Azure account and select your IoT Hub.

1. No Visual Studio Code, abra a exibição do **Explorer**.
1. At the bottom of the Explorer, expand the **Azure IoT Hub** section.

   ![Expanda a seção de Dispositivos do Azure Hub IoT](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Click on the **...** in the **Azure IoT Hub** section header. Se você não visualizar as reticências, clique ou passe o mouse sobre o cabeçalho.
1. Escolha **Selecionar Hub IoT**.
1. If you aren't signed in to your Azure account, follow the prompts to do so.
1. Selecione sua assinatura do Azure.
1. Selecione seu Hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Create an IoT Edge device with Visual Studio Code

1. No explorador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**.
1. Clique em **...** no cabeçalho da seção **Dispositivos do Hub IoT**. Se você não visualizar as reticências, clique ou passe o mouse sobre o cabeçalho.
1. Selecione **Criar dispositivo IoT Edge**.
1. Na caixa de texto que abrir, dê a seu dispositivo um ID.

Na tela de saída, você verá o resultado do comando. A informação do dispositivo é impressa, o que inclui o **deviceId** que você forneceu e o **connectionString** que você consegue usar para conectar seu dispositivo físico ao Hub IoT.

Na tela de saída, você verá o resultado do comando. A informação do dispositivo é impressa, o que inclui o **deviceId** que você forneceu e o **connectionString** que você consegue usar para conectar seu dispositivo físico ao Hub IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>View IoT Edge devices with Visual Studio Code

All the devices that connect to your IoT hub are listed in the **Azure IoT Hub** section of the Visual Studio Code Explorer. IoT Edge devices are distinguishable from non-Edge devices with a different icon, and the fact that the **$edgeAgent** and **$edgeHub** modules are deployed to each IoT Edge device.

![Exibir todos os dispositivos do IoT Edge no hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Retrieve the connection string with Visual Studio Code

Quando você estiver pronto para configurar o seu dispositivo, você precisará de uma cadeia de caracteres de conexão que conecte o seu dispositivo físico com a sua identidade no Hub IoT.

1. Right-click on the ID of your device in the **Azure IoT Hub** section.
1. Selecione **Copiar cadeia de conexão de dispositivo**.

   A cadeia de conexão é copiada à sua área de transferência.

Você também pode selecionar **Pegar Informação do Dispositivo** do menu de botão direito para ver todas as informações do dispositivo, incluindo a cadeia de conexão, na janela de saída.

## <a name="register-with-the-azure-cli"></a>Register with the Azure CLI

The [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is an open-source cross platform command-line tool for managing Azure resources such as IoT Edge. Isso permite que você gerencie instantaneamente recursos, instâncias de serviço de provisionamento de dispositivos e hubs vinculados do Hub IoT. A nova extensão de IoT enriquece a CLI do Azure com recursos como gerenciamento de dispositivos e funcionalidade completa do IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Prerequisites for the Azure CLI

* Um [Hub IoT](../iot-hub/iot-hub-create-using-cli.md) na assinatura do Azure.
* A [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a versão da CLI do Azure deve ser 2.0.24 ou superior. Use `az --version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack.
* A [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Create an IoT Edge device with the Azure CLI

Use the [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) command to create a new device identity in your IoT hub. Por exemplo:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* **device-id**: fornece um nome descritivo exclusivo para o hub IoT.
* **hub-name**: fornece o nome do hub IoT.
* **edge-enabled**: esse parâmetro declara que o dispositivo é para uso com IoT Edge.

   ![Saída da criação de identidade do dispositivo de hub IoT do Azure](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>View IoT Edge devices with the Azure CLI

Use the [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) command to view all devices in your IoT hub. Por exemplo:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo registrado como um dispositivo do IoT Edge terá a propriedade **capabilities.iotEdge** definida para **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Retrieve the connection string with the Azure CLI

Quando você estiver pronto para configurar o seu dispositivo, você precisará de uma cadeia de caracteres de conexão que conecte o seu dispositivo físico com a sua identidade no Hub IoT. Use the [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) command to return the connection string for a single device:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

O valor para o parâmetro `device-id` diferencia maiúsculas de minúsculas. Não copie as aspas em torno da cadeia de caracteres de conexão.

## <a name="next-steps"></a>Próximos passos

Now that you have a device identity registered in your IoT hub, you're ready to install the IoT Edge runtime on your devices. Install the runtime according to the device's operating system:

* [Install Azure IoT Edge on Windows](how-to-install-iot-edge-windows.md)
* [Install the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)
