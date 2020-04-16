---
title: Use uma identidade gerenciada atribuída ao sistema para acessar o Azure Key Vault
description: Saiba como criar uma identidade gerenciada para aplicativos do App Service e como usá-lo para acessar o Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: bb5288d043ab5638bb33c357cea55c64b03fcf1d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432119"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Forneça a autenticação do Key Vault com uma identidade gerenciada

Uma identidade gerenciada do Azure Active Directory permite que seu aplicativo acesse facilmente outros recursos protegidos pelo Azure. A identidade é gerenciada pela plataforma do Azure e não exige provisionamento ou giro de nenhum segredo. Para saber mais, confira [Gerenciar identidades para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). 

Este artigo mostra como criar uma identidade gerenciada para um aplicativo do App Service e usá-lo para acessar o Azure Key Vault. Para aplicativos hospedados em VMs do Azure, consulte [Usar uma identidade gerenciada atribuída ao sistema Windows VM para acessar o Azure Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md).


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos 

Para completar este guia, você deve ter os seguintes recursos. 

- Um cofre de chaves. Você pode usar um cofre de chaves existente ou criar um seguindo as etapas em um destes inícios rápidos:
   - [Criar um cofre de chaves com a CLI do Azure](../secrets/quick-create-cli.md)
   - [Criar um cofre de chaves com o Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Criar um cofre de chaves com o portal do Azure](../secrets/quick-create-portal.md).
- Um aplicativo de serviço de aplicativo existente para conceder acesso ao cofre de chaves. Você pode criar rapidamente um seguindo as etapas da [documentação](../../app-service/overview.md)do App Service .
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview). Como alternativa, você pode usar o [portal do Azure](https://portal.azure.com).


## <a name="adding-a-system-assigned-identity"></a>Adicionando uma identidade designada pelo sistema 

Primeiro, você deve adicionar uma identidade atribuída ao sistema a um aplicativo. 
 
### <a name="azure-portal"></a>Portal do Azure 

Para configurar uma identidade gerenciada no portal, primeiro, crie um aplicativo como normal e, em seguida, habilite o recurso. 

1. Se você estiver usando um aplicativo de funções, navegue até os **recursos da Plataforma**. Para outros tipos de aplicativo, role para baixo até o grupo **Configurações** no painel de navegação à esquerda. 

1. Selecione **Identidade gerenciada**. 

1. Na guia **Sistema atribuído**, alterne o **Status** para **Ligado**. Clique em **Save** (Salvar). 

    ![](../media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>CLI do Azure

Esta partida rápida requer a versão 2.0.4 ou posterior do Azure CLI. Execute `az --version` para localizar a versão atual. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Para fazer login com o Azure CLI, use o comando [de login az:](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Para obter mais informações sobre as opções de login com o Azure CLI, consulte [Entrar com o Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

Para criar a identidade para este aplicativo, use o comando de [atribuição de identidade az webapp azure](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) CLI az ou o comando [de atribuição de identidade az functionapp:](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign)


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Anote o `PrincipalId`, que será necessário na próxima seção.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Conceda ao seu aplicativo acesso ao Key Vault 

### <a name="azure-portal"></a>Portal do Azure

1.  Navegue até o recurso Key Vault. 

1.  Selecione **políticas de acesso** e clique em Adicionar política de **acesso**. 

1.  Em **permissões secretas,** **selecione Get, List**. 

1.  Escolha **Selecionar principal**e no campo de pesquisa digite o nome do aplicativo.  Selecione o aplicativo na lista de resultados e clique em **Selecionar**. 

1.  Clique **em Adicionar** para terminar de adicionar a nova política de acesso.

    ![](../media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>CLI do Azure

Para conceder acesso ao seu cofre de chaves, use o comando [azure CLI az keyvault set-policy,](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) fornecendo o parâmetro **ObjectId** com o **principalId** que você observou acima.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Próximas etapas

- [Segurança do Azure Key Vault: gerenciamento de identidade e acesso](overview-security.md#identity-and-access-management)
- [Fornecer a autenticação do Key Vault com uma política de controle de acesso](group-permissions-for-apps.md)
- [Proteja seu cofre de chaves](secure-your-key-vault.md)).
- [Guia do desenvolvedor do Azure Key Vault](developers-guide.md)
- Examine as [Melhores práticas do Azure Key Vault](best-practices.md)