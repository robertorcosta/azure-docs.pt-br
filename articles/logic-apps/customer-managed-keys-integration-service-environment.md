---
title: Configure chaves gerenciadas pelo cliente para criptografar dados em repouso em ISEs
description: Crie e gerencie suas próprias chaves de criptografia para proteger dados em repouso para ambientes de serviço de integração (ISEs) em Aplicativos de Lógica Do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127639"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Configure chaves gerenciadas pelo cliente para criptografar dados em repouso para ambientes de serviço de integração (ISEs) em Aplicativos de Lógica Do Azure

O Azure Logic Apps conta com o Azure Storage para armazenar e [criptografar](../storage/common/storage-service-encryption.md)automaticamente dados em repouso . Essa criptografia protege seus dados e ajuda você a cumprir seus compromissos de segurança organizacional e conformidade. Por padrão, o Azure Storage usa chaves gerenciadas pela Microsoft para criptografar seus dados. Para obter mais informações sobre como funciona a criptografia do Azure Storage, consulte [a criptografia de armazenamento do Azure para obter dados em repouso](../storage/common/storage-service-encryption.md) e a criptografia de dados do [Azure em repouso](../security/fundamentals/encryption-atrest.md).

Quando você cria um [ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) para hospedar seus aplicativos lógicos e deseja mais controle sobre as chaves de criptografia usadas pelo Azure Storage, você pode configurar, usar e gerenciar sua própria chave usando o [Azure Key Vault](../key-vault/key-vault-overview.md). Esse recurso também é conhecido como "Traga sua própria chave" (BYOK), e sua chave é chamada de "chave gerenciada pelo cliente".

Este tópico mostra como configurar e especificar sua própria chave de criptografia para usar quando você criar seu ISE usando a API Logic Apps REST. Para obter as etapas gerais para criar um ISE através da API Logic Apps REST, consulte [Criar um ambiente de serviço de integração (ISE) usando a API Logic Apps REST](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Considerações

* Neste momento, o suporte-chave gerenciado pelo cliente para um ISE está disponível apenas nestas regiões azure: West US 2, East US e South Central US

* Você só pode especificar uma chave gerenciada pelo cliente *quando criar seu ISE,* não depois. Você não pode desativar essa chave depois que seu ISE for criado. Atualmente, não existe suporte para rodar uma chave gerenciada pelo cliente para um ISE.

* Para suportar as chaves gerenciadas pelo cliente, o ISE requer que sua [identidade gerenciada pelo sistema](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) seja ativada. Essa identidade permite que o ISE autentique o acesso aos recursos em outros inquilinos do Azure Active Directory (Azure AD) para que você não precise fazer login com suas credenciais.

* Atualmente, para criar uma ISE que suporte chaves gerenciadas pelo cliente e tenha sua identidade atribuída ao sistema ativada, você tem que chamar a API Logic Apps REST usando uma solicitação HTTPS PUT.

* Dentro *de 30 minutos* após enviar a solicitação HTTPS PUT que cria o SEU ISE, você deve [dar acesso ao cofre-chave à identidade atribuída ao sistema do seu ISE](#identity-access-to-key-vault). Caso contrário, a criação do ISE falha e lança um erro de permissões.

## <a name="prerequisites"></a>Pré-requisitos

* Os [mesmos pré-requisitos](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisitos para habilitar o acesso ao seu ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) como quando você cria um ISE no portal Azure

* Um cofre de chaves Do Zure que tenha as propriedades **Soft Delete** e **Não Expurgo** ativadas

  Para obter mais informações sobre como habilitar essas propriedades, consulte [a visão geral do Azure Key Vault](../key-vault/key-vault-ovw-soft-delete.md) e [configure chaves gerenciadas pelo cliente com o Azure Key Vault](../storage/common/storage-encryption-keys-portal.md). Se você é novo no Azure Key Vault, [aprenda a criar um cofre de chaves](../key-vault/quick-create-portal.md#create-a-vault) usando o portal Azure ou usando o comando Azure PowerShell, [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault).

* No cofre principal, uma chave criada com esses valores de propriedade:

  | Propriedade | Valor |
  |----------|-------|
  | **Tipo de chave** | RSA |
  | **Tamanho da chave RSA** | 2.048 |
  | **Habilitado** | Sim |
  |||

  ![Crie sua chave de criptografia gerenciada pelo cliente](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Para obter mais informações, consulte [Configure chaves gerenciadas pelo cliente com o Azure Key Vault](../storage/common/storage-encryption-keys-portal.md) ou com o comando Azure PowerShell, [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey).

* Uma ferramenta que você pode usar para criar seu ISE chamando a API Logic Apps REST com uma solicitação HTTPS PUT. Por exemplo, você pode usar [o Carteiro](https://www.getpostman.com/downloads/), ou você pode construir um aplicativo lógico que executa essa tarefa.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Crie ISE com cofre de chaves e suporte de identidade gerenciado

Para criar seu ISE chamando a API de logic apps REST, faça esta solicitação HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> A versão da API REST 2019-05-01 do Logic Apps requer que você faça sua própria solicitação HTTP PUT para conectores ISE.

A implantação geralmente leva duas horas para ser concluída. Ocasionalmente, a implantação pode levar até quatro horas. Para verificar o status de implantação, no [portal Azure,](https://portal.azure.com)na barra de ferramentas do Azure, selecione o ícone notificações, que abre o painel de notificações.

> [!NOTE]
> Se a implantação falhar ou se você excluir o ISE, o Azure poderá levar até uma hora para liberar as sub-redes. Este atraso significa que você pode ter que esperar antes de reutilizar essas sub-redes em outro ISE.
>
> Se você excluir sua rede virtual, o Azure geralmente leva até duas horas antes de liberar suas sub-redes, mas essa operação pode levar mais tempo. 
> Ao excluir redes virtuais, certifique-se de que nenhum recurso ainda esteja conectado. 
> Consulte [Excluir rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Cabeçalho da solicitação

No cabeçalho de solicitação, inclua estas propriedades:

* `Content-type`: Definir este `application/json`valor de propriedade para .

* `Authorization`: Defina esse valor de propriedade para o token do portador para o cliente que tiver acesso à assinatura do Azure ou ao grupo de recursos que você deseja usar.

### <a name="request-body"></a>Corpo da solicitação

No órgão de solicitação, habilite o suporte a esses itens adicionais fornecendo suas informações na sua definição ISE:

* A identidade gerenciada atribuída ao sistema que seu ISE usa para acessar seu cofre de chaves
* Seu cofre de chaves e a chave gerenciada pelo cliente que você deseja usar

#### <a name="request-body-syntax"></a>Sintaxe de corpo da solicitação

Aqui está a sintaxe do corpo de solicitação, que descreve as propriedades a serem usados quando você cria seu ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Solicitar exemplo de corpo

Este corpo de solicitação de exemplo mostra os valores da amostra:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Permitir acesso ao cofre de chaves

Dentro de *30 minutos* após enviar a solicitação HTTP PUT para criar seu ISE, você deve adicionar uma política de acesso ao cofre principal para a identidade atribuída ao sistema do seu ISE. Caso contrário, a criação do seu ISE falha e você recebe um erro de permissões. 

Para esta tarefa, você pode usar o comando Azure PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) ou pode seguir essas etapas no portal Azure:

1. No [portal Azure,](https://portal.azure.com)abra seu cofre de chaves Do Zure.

1. No menu do cofre principal, selecione **Políticas** > de acesso**Adicionar política de acesso,** por exemplo:

   ![Adicionar política de acesso para identidade gerenciada atribuída ao sistema](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Depois que o **painel de diretiva** de acesso adicionar for aberto, siga estas etapas:

   1. Selecione estas opções:

      | Configuração | Valores |
      |---------|--------|
      | **Configurar a partir da lista de modelos (opcional)** | Gerenciamento de chaves |
      | **Permissões-chave** | - **Principais operações de gerenciamento**: Obter, Lista <p><p>- **Operações criptográficas**: Desembrulhar chave, envoltório de chave |
      |||

      ![Selecione "Gerenciamento de chaves" > "Permissões-chave"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Para **Selecionar principal,** selecione **Nenhum selecionado**. Depois **que o painel Principal** for aberto, na caixa de pesquisa, encontre e selecione seu ISE. Quando terminar, escolha **Selecionar** > **Adicionar**.

      ![Selecione seu ISE para usar como principal](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Quando terminar com o painel **de políticas de acesso,** selecione **Salvar**.

Para obter mais informações, consulte [Fornecer autenticação do Key Vault com uma identidade gerenciada](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Azure Key Vault](../key-vault/key-vault-overview.md)