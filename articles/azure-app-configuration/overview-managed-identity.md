---
title: Configure identidades gerenciadas com a configuração do aplicativo Azure
description: Saiba como as identidades gerenciadas funcionam na configuração do aplicativo Azure e como configurar uma identidade gerenciada
author: jpconnock
ms.topic: article
ms.date: 02/25/2020
ms.author: jeconnoc
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: fe66466395a100221e6a3cdebdef870bdf195afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623038"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Como usar identidades gerenciadas para configuração do aplicativo Azure

Este tópico mostra como criar uma identidade gerenciada para a configuração do aplicativo Azure. Uma identidade gerenciada do Azure Active Directory (AAD) permite que a configuração do aplicativo Azure acesse facilmente outros recursos protegidos por AAD, como o Azure Key Vault. A identidade é gerenciada pela plataforma Azure. Não exige que você provimento ou gire quaisquer segredos. Para obter mais informações sobre identidades gerenciadas no AAD, veja [Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

Seu aplicativo pode receber dois tipos de identidades:

- Uma **identidade atribuída ao sistema** está vinculada ao seu armazenamento de configuração. É excluído se sua configuração de armazenamento for excluída. Um armazenamento de configuração só pode ter uma identidade atribuída ao sistema.
- Uma **identidade atribuída pelo usuário** é um recurso autônomo do Azure que pode ser atribuído à sua loja de configuração. Uma loja de configuração pode ter várias identidades atribuídas pelo usuário.

## <a name="adding-a-system-assigned-identity"></a>Adicionando uma identidade designada pelo sistema

Criar uma loja de configuração de aplicativos com uma identidade atribuída ao sistema requer uma propriedade adicional a ser definida na loja.

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

Para configurar uma identidade gerenciada usando o Azure CLI, use o comando [az appconfig identity atribuição] contra um armazenamento de configuração existente. Você tem três opções para executar os exemplos nesta seção:

- Usar o [Azure Cloud Shell](../cloud-shell/overview.md) do portal do Azure.
- Use o Azure Cloud Shell incorporado através do botão "Try It", localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.1 ou posterior) se preferir usar um console CLI local.

As etapas a seguir irão levá-lo através da criação de uma loja de configuração de aplicativos e atribuindo-lhe uma identidade usando o CLI:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az]. Use uma conta associada à sua assinatura do Azure:

    ```azurecli-interactive
    az login
    ```

1. Crie uma loja de configuração de aplicativos usando o CLI. Para obter mais exemplos de como usar o CLI com a configuração do aplicativo Azure, consulte [amostras de CLI de configuração do aplicativo](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Execute o comando [az appconfig identity atribuição] de identidade para criar a identidade atribuída ao sistema para esta configuração:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Adicionando uma identidade atribuída pelo usuário

Criar uma loja de configuração de aplicativos com uma identidade atribuída pelo usuário requer que você crie a identidade e, em seguida, atribua seu identificador de recursos à sua loja.

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

Para configurar uma identidade gerenciada usando o Azure CLI, use o comando [az appconfig identity atribuição] contra um armazenamento de configuração existente. Você tem três opções para executar os exemplos nesta seção:

- Usar o [Azure Cloud Shell](../cloud-shell/overview.md) do portal do Azure.
- Use o Azure Cloud Shell incorporado através do botão "Try It", localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 ou mais recente) se você preferir usar um console da CLI local.

As etapas a seguir o acompanharão através da criação de uma identidade atribuída pelo usuário e de uma loja de configuração de aplicativos e, em seguida, atribuirá a identidade à loja usando o CLI:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az]. Use uma conta associada à sua assinatura do Azure:

    ```azurecli-interactive
    az login
    ```

1. Crie uma loja de configuração de aplicativos usando o CLI. Para obter mais exemplos de como usar o CLI com a configuração do aplicativo Azure, consulte [amostras de CLI de configuração do aplicativo](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Crie uma identidade atribuída `myUserAssignedIdentity` pelo usuário chamada usando o CLI.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Na saída deste comando, observe o `id` valor da propriedade.

1. Execute o comando [az appconfig identity atribuir] para atribuir a nova identidade atribuída pelo usuário a esta configuração. Use o valor `id` do imóvel que você anotou na etapa anterior.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Removendo uma identidade

Uma identidade atribuída ao sistema pode ser removida desativando o recurso usando o comando [az appconfig identity remove](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) no Azure CLI. As identidades atribuídas pelo usuário podem ser removidas individualmente. Remover uma identidade atribuída pelo sistema dessa maneira também a excluirá do AAD. As identidades atribuídas pelo sistema também são automaticamente removidas do AAD quando o recurso do aplicativo é excluído.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Crie um aplicativo ASP.NET Core com a configuração do aplicativo Azure](quickstart-aspnet-core-app.md)

[az appconfig identidade atribuir]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[login az]: /cli/azure/reference-index#az-login
