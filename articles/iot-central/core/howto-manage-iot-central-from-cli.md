---
title: Gerenciar IoT Central da Azure CLI | Microsoft Docs
description: Este artigo descreve como criar e gerenciar seu aplicativo IoT Central usando CLI. Você pode visualizar, modificar e remover o aplicativo usando CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: df24a2dc6e9bd058a2f8b1355b8760653ed3128a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365516"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gerenciar IoT Central da Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar aplicativos IoT Central no site do gerenciador de [aplicativos Azure IoT Central,](https://aka.ms/iotcentral) você pode usar [o Azure CLI](/cli/azure/) para gerenciar seus aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se preferir executar o Azure CLI em sua máquina local, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli). Quando você executar o Azure CLI localmente, use o comando **de login az** para entrar no Azure antes de experimentar os comandos neste artigo.

> [!TIP]
> Se você precisar executar seus comandos CLI em uma assinatura diferente do Azure, consulte [Alterar a assinatura ativa](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription).

## <a name="install-the-extension"></a>Instalar a extensão

Os comandos deste artigo fazem parte da extensão **cli azure-iot.** Execute o seguinte comando para instalar a extensão:

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>Criar um aplicativo

Use o [comando az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create) para criar um aplicativo IoT Central em sua assinatura do Azure. Por exemplo: 

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview@1.0.0" \
  --display-name "My Custom Display Name"
```

Esses comandos primeiro criam um grupo de recursos na região leste dos EUA para a aplicação. A tabela a seguir descreve os parâmetros usados com o comando **az iotcentral app create:**

| Parâmetro         | Descrição |
| ----------------- | ----------- |
| resource-group    | O grupo de recursos que contém o aplicativo. Esse grupo de recursos já precisa existir na sua assinatura. |
| local          | Por padrão, este comando usa a localização do grupo de recursos. Atualmente, você pode criar uma aplicação IoT Central nas geografias **Austrália**, **Ásia-Pacífico**, **Europa**, **Estados Unidos,** **Reino Unido**e **Japão.** |
| name              | Digite o nome do aplicativo no portal do Azure. |
| subdomain         | O subdomínio na URL do aplicativo. No exemplo, a URL do aplicativo é `https://mysubdomain.azureiotcentral.com`. |
| sku               | Atualmente, você pode usar **ST1** ou **ST2**. Confira [Preço do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | O modelo de aplicativo a usar. Para obter mais informações, confira a tabela a seguir. |
| nome de exibição      | O nome do aplicativo, conforme exibido na interface do usuário. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Exibir seus aplicativos

Use o comando [az iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list) para listar seus aplicativos IoT Central e exibir metadados.

## <a name="modify-an-application"></a>Modificar um aplicativo

Use o comando [az iotcentral app update](/cli/azure/iotcentral/app#az-iotcentral-app-update) para atualizar os metadados de um aplicativo IoT Central. Por exemplo, para alterar o nome de exibição do aplicativo:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Remover um aplicativo

Use o comando [az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) para excluir um aplicativo IoT Central. Por exemplo: 

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a gerenciar aplicativos Azure IoT Central da Azure CLI, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Administrar seu aplicativo](howto-administer.md)
