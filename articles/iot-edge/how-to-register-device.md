---
title: Registre um novo dispositivo Azure IoT Edge | Microsoft Docs
description: Use a extensão de IoT para a CLI do Azure para registrar um novo dispositivo IoT Edge e recuperar a cadeia de conexão
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fb776b4c1ff537401a23eb272526b3043fdb1e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235719"
---
# <a name="register-an-azure-iot-edge-device"></a>Registre um dispositivo Azure IoT Edge

Antes de usar seus dispositivos IoT com o Azure IoT Edge, você deve registrá-los no seu hub IoT. Uma vez que um dispositivo é registrado, você pode recuperar uma seqüência de conexão para configurar seu dispositivo para cargas de trabalho IoT Edge.

Você tem a opção de se registrar usando uma das seguintes ferramentas:

* [Registre um dispositivo no portal Azure](#register-in-the-azure-portal) se preferir uma interface gráfica de usuário para criar, visualizar e gerenciar os recursos do Azure.
* [Registre um dispositivo com o Visual Studio Code](#register-with-visual-studio-code) se preferir gerenciar recursos DeI do Azure no mesmo lugar onde você desenvolve suas soluções de IoT.
* [Registre um dispositivo com o Cli do Azure](#register-with-the-azure-cli) se preferir ferramentas de linha de comando para gerenciar os recursos do Azure ou pretende automatizar tarefas.

## <a name="register-in-the-azure-portal"></a>Cadastre-se no portal Azure

Você pode executar todas as tarefas de registro no portal Azure.

### <a name="prerequisites-for-the-azure-portal"></a>Pré-requisitos para o portal Azure

Um hub [de IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou padrão em sua assinatura do Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Crie um dispositivo IoT Edge no portal Azure

Em seu IoT Hub no portal Azure, os dispositivos IoT Edge são criados e gerenciados separadamente de dispositivos IOT que não estão habilitados para borda.

1. Faça login no [portal Azure](https://portal.azure.com) e navegue até o seu hub de IoT.
2. No painel esquerdo, selecione **IoT Edge** no menu.
3. Selecione **Adicionar um dispositivo IoT Edge**.
4. Forneça um ID de dispositivo descritivo. Use as configurações padrão para gerar automaticamente as chaves de autenticação e conecte o novo dispositivo ao seu hub.
5. Selecione **Salvar**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Veja dispositivos IoT Edge no portal Azure

Todos os habilitado borda dispositivos que se conectam ao seu hub IoT são listados no **IoT Edge** página.

![Exibir todos os dispositivos do IoT Edge no hub IoT](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Recupere a seqüência de conexão no portal Azure

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT.

1. Na página **IoT Edge** no portal, clique no ID do dispositivo da lista de dispositivos IoT Edge.
2. Copie o valor da **seqüência** de conexão primária ou **string de conexão secundária**.

## <a name="register-with-visual-studio-code"></a>Registre-se com o Visual Studio Code

Há múltiplas maneiras de fazer mais operações no VS Code. Este artigo usa o Explorer, mas você também pode usar a Paleta de Comando para executar as etapas.

### <a name="prerequisites-for-visual-studio-code"></a>Pré-requisitos para Visual Studio Code

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure
* [Código visual do estúdio](https://code.visualstudio.com/)
* [Ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para código visual do estúdio

### <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

Você pode usar as extensões Azure IoT para Visual Studio Code para executar operações com seu IoT Hub. Para que essas operações funcionem, você precisa entrar na sua conta do Azure e selecionar o seu IoT Hub.

1. No Visual Studio Code, abra a exibição do **Explorer**.
1. Na parte inferior do Explorer, expanda a seção **Azure IoT Hub.**

   ![Expanda a seção de Dispositivos do Azure Hub IoT](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Clique no **cabeçalho da** seção **Azure IoT Hub.** Se você não visualizar as reticências, clique ou passe o mouse sobre o cabeçalho.
1. Escolha **Selecionar Hub IoT**.
1. Se você não estiver conectado à sua conta do Azure, siga as instruções para fazê-lo.
1. Selecione sua assinatura do Azure.
1. Selecione seu Hub IoT.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Crie um dispositivo IoT Edge com código visual do estúdio

1. No VS Code Explorer, expanda a seção **Dispositivos de Hub Azure IoT.**
1. Clique em **...** no cabeçalho da seção **Dispositivos do Hub IoT**. Se você não visualizar as reticências, clique ou passe o mouse sobre o cabeçalho.
1. Selecione **Criar dispositivo IoT Edge**.
1. Na caixa de texto que abrir, dê a seu dispositivo um ID.

Na tela de saída, você verá o resultado do comando. A informação do dispositivo é impressa, o que inclui o **deviceId** que você forneceu e o **connectionString** que você consegue usar para conectar seu dispositivo físico ao Hub IoT.

Na tela de saída, você verá o resultado do comando. A informação do dispositivo é impressa, o que inclui o **deviceId** que você forneceu e o **connectionString** que você consegue usar para conectar seu dispositivo físico ao Hub IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Exibir dispositivos IoT Edge com visual studio code

Todos os dispositivos que se conectam ao seu hub IoT estão listados na seção **Azure IoT Hub** do Visual Studio Code Explorer. Os dispositivos IoT Edge são distinguíveis de dispositivos não-Edge com um ícone diferente, e o fato de que os módulos **$edgeAgent** e **$edgeHub** são implantados em cada dispositivo IoT Edge.

![Exibir todos os dispositivos do IoT Edge no hub IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Recupere a seqüência de conexão com visual studio code

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT.

1. Clique com o botão direito do mouse no ID do seu dispositivo na seção **Hub IoT do Azure.**
1. Selecione **Copiar cadeia de conexão de dispositivo**.

   A cadeia de conexão é copiada à sua área de transferência.

Você também pode selecionar **Pegar Informação do Dispositivo** do menu de botão direito para ver todas as informações do dispositivo, incluindo a cadeia de conexão, na janela de saída.

## <a name="register-with-the-azure-cli"></a>Registre-se na CLI do Azure

O [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando cross platform de código aberto para gerenciar recursos do Azure, como o IoT Edge. Isso permite que você gerencie instantaneamente recursos, instâncias de serviço de provisionamento de dispositivos e hubs vinculados do Hub IoT. A extensão de IoT enriquece a CLI do Azure com recursos como gerenciamento de dispositivos e recursos completos de IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Pré-requisitos para a CLI azure

* Um [hub de IoT](../iot-hub/iot-hub-create-using-cli.md) em sua assinatura do Azure.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) em seu ambiente. No mínimo, sua versão Azure CLI deve ser 2.0.70 ou superior. Use `az --version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack.
* A [extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Crie um dispositivo IoT Edge com o Azure CLI

Use o comando [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) para criar uma nova identidade de dispositivo em seu hub IoT. Por exemplo: 

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* **device-id**: fornece um nome descritivo exclusivo para o hub IoT.
* **hub-name**: fornece o nome do hub IoT.
* **edge-enabled**: esse parâmetro declara que o dispositivo é para uso com IoT Edge.

   ![Saída da criação de identidade do dispositivo de hub IoT do Azure](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Veja dispositivos IoT Edge com o Azure CLI

Use o comando [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) para visualizar todos os dispositivos em seu hub IoT. Por exemplo: 

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo registrado como um dispositivo do IoT Edge terá a propriedade **capabilities.iotEdge** definida para **true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Recupere a seqüência de conexão com o Cli do Azure

Quando estiver pronto para configurar o dispositivo, você precisará da cadeia de conexão que vincula o dispositivo físico à identidade no hub IoT. Use o comando [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) para retornar a seqüência de conexões para um único dispositivo:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

O valor para o parâmetro `device-id` diferencia maiúsculas de minúsculas. Não copie as aspas em torno da cadeia de caracteres de conexão.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma identidade de dispositivo registrada em seu hub ioT, você está pronto para instalar o tempo de execução do IoT Edge em seus dispositivos. Instale o tempo de execução de acordo com o sistema operacional do dispositivo:

* [Instale o Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md)
* [Instale o tempo de execução do Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)
