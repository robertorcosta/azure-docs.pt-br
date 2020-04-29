---
title: Implantar uma política que possa ser corrigida
description: Saiba como integrar um cliente ao gerenciamento de recursos delegado do Azure, permitindo que seus recursos sejam acessados e gerenciados por meio de seu próprio locatário.
ms.date: 10/11/2019
ms.topic: conceptual
ms.openlocfilehash: b625e9e3c96866cfbc655a55b770c9ac07a626bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985160"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Implantar uma política que pode ser corrigida em uma assinatura delegada

O [Azure Lighthouse](../overview.md) permite que os provedores de serviços criem e editem definições de política em uma assinatura delegada. No entanto, para implantar políticas que usam uma [tarefa de correção](../../governance/policy/how-to/remediate-resources.md) (ou seja, políticas com o efeito [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) ou [Modificar](../../governance/policy/concepts/effects.md#modify) ), você precisará criar uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) no locatário do cliente. Essa identidade gerenciada pode ser usada pelo Azure Policy para implantar o modelo na política. Há etapas necessárias para habilitar esse cenário, não apenas quando você integra o cliente ao gerenciamento de recursos delegados do Azure, mas também quando implanta a política propriamente dita.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Criar um usuário que pode atribuir funções a uma identidade gerenciada no locatário do cliente

Ao integrar um cliente para o gerenciamento de recursos delegados do Azure, você usa um [modelo do Azure Resource Manager](onboard-customer.md#create-an-azure-resource-manager-template) junto com um arquivo de parâmetros que define os usuários, grupos de usuários e entidades de serviço no seu locatário de gerenciamento que poderá acessar os recursos delegados no locatário do cliente. No arquivo de parâmetros, cada um desses usuários (**principalId**) é atribuído a uma [função interna](../../role-based-access-control/built-in-roles.md) (**roleDefinitionId**) que define o nível de acesso.

Para permitir que um **principalId** crie uma identidade gerenciada no locatário do cliente, você deve definir a respectiva **roleDefinitionId** para **Administrador de Acesso do Usuário**. Embora em geral essa função não seja compatível, ela pode ser usada nesse cenário específico, permitindo que os usuários com essa permissão atribuam uma ou mais funções internas específicas a identidades gerenciadas. Essas funções são definidas na propriedade **delegatedRoleDefinitionIds**. Você pode incluir qualquer função interna aqui, exceto Proprietário ou Administrador de Acesso do Usuário.

Depois que o cliente for integrado, o **principalId** criado nessa autorização poderá atribuir essas funções internas a identidades gerenciadas no locatário do cliente. No entanto, elas não terão nenhuma outra permissão normalmente associada à função de Administrador de Acesso do Usuário.

O exemplo a seguir mostra um **principalId** que terá a função de Administrador de Acesso do Usuário. Esse usuário poderá atribuir duas funções internas a identidades gerenciadas no locatário do cliente: colaborador e colaborador de Log Analytics.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Implantar políticas que possam ser corrigidas

Depois de criar o usuário com as permissões necessárias, conforme descrito acima, esse usuário pode implantar políticas no locatário do cliente que usam tarefas de correção.

Por exemplo, digamos que você queria habilitar o diagnóstico em Azure Key Vault recursos no locatário do cliente, conforme ilustrado neste [exemplo](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring). Um usuário no locatário de gerenciamento com as permissões apropriadas (conforme descrito acima) implantaria um [modelo do Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) para habilitar esse cenário.

Observe que a criação da atribuição de política a ser usada com uma assinatura delegada deve ser feita atualmente por meio de APIs, não no portal do Azure. Ao fazer isso, a **apiVersion** deve ser definida para **2019-04-01-preview**, que inclui a nova propriedade **delegatedManagedIdentityResourceId**. Essa propriedade permite incluir uma identidade gerenciada que reside no locatário do cliente (em uma assinatura ou grupo de recursos que foi integrado ao gerenciamento de recursos delegados do Azure).

O exemplo a seguir mostra uma atribuição de função com um **delegatedManagedIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> Um [exemplo similar](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) está disponível para demonstrar como implantar uma política que adiciona ou remove uma tag (usando o efeito modificar) para uma assinatura delegada.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Azure Policy](../../governance/policy/index.yml).
- Saiba mais sobre as [identidades gerenciadas dos recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).
