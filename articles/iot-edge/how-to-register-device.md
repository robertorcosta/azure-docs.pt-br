---
title: Registrar um novo dispositivo do Azure IoT Edge | Microsoft Docs
description: Use a extensão de IoT para a CLI do Azure para registrar um novo dispositivo IoT Edge e recuperar a cadeia de conexão
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 85a5e2f6b28b9332e5ad3a38cdad3fb02bb3da87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450148"
---
# <a name="register-an-azure-iot-edge-device"></a>Registrar um dispositivo do Azure IoT Edge

Antes de poder usar os dispositivos do IoT com Azure IoT Edge, você deve registrá-los no hub IoT. Depois que um dispositivo é registrado, você pode recuperar uma cadeia de conexão para configurar seu dispositivo para cargas de trabalho de IoT Edge.

Você tem a opção de registrar-se usando uma das seguintes ferramentas:

* [Registre um dispositivo no portal do Azure](#register-in-the-azure-portal), se você preferir uma interface gráfica do usuário para criar, exibir e gerenciar os recursos do Azure.
* [Registre um dispositivo com o Visual Studio Code](#register-with-visual-studio-code), se você preferir gerenciar os recursos de IoT do Azure no mesmo local em que desenvolve as soluções de IoT.
* [Registre um dispositivo com a CLI do Azure](#register-with-the-azure-cli), se você preferir ferramentas de linha de comando para gerenciar os recursos do Azure ou se pretender automatizar as tarefas.

## <a name="register-in-the-azure-portal"></a>Registrar no portal do Azure

Você pode realizar todas as tarefas de registro no portal do Azure.

### <a name="prerequisites-for-the-azure-portal"></a>Pré-requisitos do portal do Azure

Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou padrão na assinatura do Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Criar um dispositivo do IoT Edge no portal do Azure

No hub IoT do portal do Azure, os dispositivos do IoT Edge são criados e gerenciados separadamente dos dispositivos de IoT que não são habilitados para a borda.

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.
2. No painel esquerdo, selecione **IoT Edge** no menu.
3. Selecione **Adicionar um dispositivo IoT Edge**.
4. Forneça um ID de dispositivo descritivo. Use as configurações padrão para gerar automaticamente as chaves de autenticação e conectar o novo dispositivo ao hub.
5. Clique em **Salvar**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Exibir dispositivos do IoT Edge no portal do Azure

Todos os habilitado borda dispositivos que se conectam ao seu hub IoT são listados no **IoT Edge** página.

![Exibir todos os dispositivos do IoT Edge no hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recuperar a cadeia de conexão no portal do Azure

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT.

1. Na página **IoT Edge** no portal, clique na ID de dispositivo na lista de dispositivos do IoT Edge.
2. Copie o valor da **Cadeia de conexão primária** ou **Cadeia de conexão secundária**.

## <a name="register-with-visual-studio-code"></a>Registrar com o Visual Studio Code

Há múltiplas maneiras de fazer mais operações no VS Code. Este artigo usa o Explorer, mas você pode também usar a Paleta de Comandos para executar as etapas.

### <a name="prerequisites-for-visual-studio-code"></a>Pré-requisitos do Visual Studio Code

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Ferramentas de IoT do Microsoft Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

É possível usar as extensões do Azure IoT para Visual Studio Code para executar operações com o Hub IoT. Para que essas operações funcionem, é necessário entrar na conta do Azure e selecionar o Hub IoT.

1. No Visual Studio Code, abra a exibição do **Explorer**.
1. Na parte inferior do Explorer, expanda a seção **Hub IoT**.

   ![Expanda a seção de Dispositivos do Azure Hub IoT](./media/how-to-register-device/azure-iot-hub-devices.png)

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

![Exibir todos os dispositivos do IoT Edge no hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recuperar a cadeia de conexão com o Visual Studio Code

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT.

1. Clique com o botão direito na ID do seu dispositivo na seção **Hub IoT**.
1. Selecione **Copiar cadeia de conexão de dispositivo**.

   A cadeia de conexão é copiada à sua área de transferência.

Você também pode selecionar **Pegar Informação do Dispositivo** do menu de botão direito para ver todas as informações do dispositivo, incluindo a cadeia de conexão, na janela de saída.

## <a name="register-with-the-azure-cli"></a>Registrar com a CLI do Azure

A [CLI do Azure](/cli/azure) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure como o IoT Edge. Isso permite que você gerencie instantaneamente recursos, instâncias de serviço de provisionamento de dispositivos e hubs vinculados do Hub IoT. A extensão de IoT enriquece a CLI do Azure com recursos como gerenciamento de dispositivos e recursos completos de IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Pré-requisitos da CLI do Azure

* Um [Hub IoT](../iot-hub/iot-hub-create-using-cli.md) na assinatura do Azure.
* [CLI do Azure](/cli/azure/install-azure-cli) em seu ambiente. No mínimo, a versão da CLI do Azure deve ser 2.0.70 ou superior. Use `az --version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack.
* A [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Criar um dispositivo do IoT Edge com a CLI do Azure

Use o comando [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) para criar uma nova identidade de dispositivo no hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* **device-id**: forneça um nome descritivo exclusivo para o hub IoT.
* **hub-name**: forneça o nome do hub IoT.
* **edge-enabled**: este parâmetro declara que o dispositivo é para uso com o IoT Edge.

   ![Saída da criação de identidade do dispositivo de hub IoT do Azure](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Exibir dispositivos do IoT Edge com a CLI do Azure

Use o comando [az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) para exibir todos os dispositivos no hub IoT. Por exemplo:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo registrado como um dispositivo do IoT Edge terá a propriedade **capabilities.iotEdge** definida para **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recuperar a cadeia de conexão com a CLI do Azure

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT. Use o comando [AZ IOT Hub Device-identidade Connection-String show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) para retornar a cadeia de conexão para um único dispositivo:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

O valor para o parâmetro `device-id` diferencia maiúsculas de minúsculas. Não copie as aspas em torno da cadeia de caracteres de conexão.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma identidade de dispositivo registrada no hub IoT, está pronto para instalar o runtime do IoT Edge nos dispositivos. Instale o runtime de acordo com o sistema operacional do dispositivo:

* [Instalar o Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md)
* [Instalar o runtime do Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)
