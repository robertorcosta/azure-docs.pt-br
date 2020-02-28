---
title: Funções personalizadas para recursos do Azure | Microsoft Docs
description: Saiba como criar as funções personalizadas com RBAC (controle de acesso baseado em função) para o gerenciamento de acesso refinado de recursos do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8c5db13b343783a86dc04b84e09746bc4406186b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660692"
---
# <a name="custom-roles-for-azure-resources"></a>Funções personalizadas para recursos do Azure

Se as [funções internas dos recursos do Azure](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias funções personalizadas. Assim como as funções internas, é possível atribuir funções personalizadas a usuários, grupos e entidades de serviço na assinatura, no grupo de recursos e nos escopos de recurso.

As funções personalizadas podem ser compartilhadas entre assinaturas que confiam no mesmo diretório do Azure AD. Há um limite de **5.000** funções personalizadas por diretório. (Para nuvens especializadas, como Azure governamental, Azure Alemanha e Azure China 21Vianet, o limite é de 2.000 funções personalizadas.) As funções personalizadas podem ser criadas usando o portal do Azure (visualização), Azure PowerShell, CLI do Azure ou a API REST.

## <a name="custom-role-example"></a>Exemplo de função personalizada

A seguir, mostra como uma função personalizada se parece, conforme exibida no formato JSON. Essa função personalizada pode ser usada para monitorar e reiniciar máquinas virtuais.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/subscriptions/{subscriptionId3}"
  ]
}
```

Quando você cria uma função personalizada, ela aparece no portal do Azure com um ícone de recurso laranja.

![Ícone personalizado da função](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Etapas para criar uma função personalizada

1. Decidir como você deseja criar a função personalizada

    Você pode criar funções personalizadas usando [portal do Azure](custom-roles-portal.md) (visualização), [Azure PowerShell](custom-roles-powershell.md), [CLI do Azure](custom-roles-cli.md)ou a [API REST](custom-roles-rest.md).

1. Determinar as permissões necessárias

    Ao criar uma função personalizada, você precisa conhecer as operações do provedor de recursos que estão disponíveis para definir suas permissões. Para exibir a lista de operações, consulte o [Azure Resource Manager operações do provedor de recursos](resource-provider-operations.md). Você adicionará as operações às propriedades `Actions` ou `NotActions` da definição de [função](role-definitions.md). Se houver operações de dados, você as adicionará às propriedades `DataActions` ou `NotDataActions`.

1. Criar a função personalizada

    Normalmente, você começa com uma função interna existente e depois a modifica conforme suas necessidades. Depois, use os comandos [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) ou [az role definition create](/cli/azure/role/definition#az-role-definition-create) para criar a função personalizada. Para criar uma função personalizada, você precisa ter a permissão `Microsoft.Authorization/roleDefinitions/write` em todo `AssignableScopes`, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso de Usuário](built-in-roles.md#user-access-administrator).

1. Testar a função personalizada

    Quando tiver sua função personalizada, será necessário testá-la para verificar se funciona como o esperado. Se você precisar fazer ajustes mais tarde, você pode atualizar a função personalizada.

Para obter um tutorial passo a passo sobre como criar uma função personalizada, consulte [Tutorial: criar uma função personalizada usando o Azure PowerShell](tutorial-custom-role-powershell.md) ou [Tutorial: criar uma função personalizada usando a CLI do Azure](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Propriedades da função personalizada

Uma função personalizada tem as seguintes propriedades.

| Propriedade | Obrigatório | Tipo | Descrição |
| --- | --- | --- | --- |
| `Name` | Sim | String | O nome de exibição da função personalizada. Embora a definição de função seja um recurso no nível da assinatura, ela pode ser usada em várias assinaturas que compartilham o mesmo diretório do Azure AD. Esse nome de exibição precisa ser exclusivo no escopo do diretório do Azure AD. Pode incluir letras, números, espaços e caracteres especiais. O número máximo de caracteres é 128. |
| `Id` | Sim | String | A ID exclusiva da função personalizada. Para o Azure PowerShell e a CLI do Azure, essa ID é gerada automaticamente ao criar uma nova função. |
| `IsCustom` | Sim | String | Indica se esta é uma função personalizada. Defina como `true` para funções personalizadas. |
| `Description` | Sim | String | A descrição da função personalizada. Pode incluir letras, números, espaços e caracteres especiais. O número máximo de caracteres é 1024. |
| `Actions` | Sim | String[] | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento permitidas pela função. Para obter mais informações, consulte [Ações](role-definitions.md#actions). |
| `NotActions` | Não | String[] | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento que são excluídas do `Actions` permitido. Para obter mais informações, consulte [NotActions](role-definitions.md#notactions). |
| `DataActions` | Não | String[] | Uma matriz de cadeias de caracteres que especifica as operações de dados permitidas pela função em seus dados dentro desse objeto. Para obter mais informações, consulte [Dataactions](role-definitions.md#dataactions). |
| `NotDataActions` | Não | String[] | Uma matriz de cadeias de caracteres que especifica as operações de dados excluídas do `DataActions` permitido. Para obter mais informações, consulte [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Sim | String[] | Uma matriz de cadeias de caracteres que especifica os escopos para os quais a função personalizada está disponível para atribuição. Para funções personalizadas, atualmente não é possível definir `AssignableScopes` para o escopo raiz (`"/"`) ou um escopo de grupo de gerenciamento. Para obter mais informações, consulte [AssignableScopes](role-definitions.md#assignablescopes) e [Organize seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Quem pode criar, excluir, atualizar ou exibir uma função personalizada

Assim como funções internas, a propriedade `AssignableScopes` especifica os escopos para os quais a função está disponível para atribuição. A propriedade `AssignableScopes` de uma função personalizada também controla quem pode criar, excluir, atualizar ou exibir a função personalizada.

| {1&gt;Tarefa&lt;1} | Operação | Descrição |
| --- | --- | --- |
| Criar/excluir uma função personalizada | `Microsoft.Authorization/ roleDefinitions/write` | Os usuários que recebem essa operação em todos os `AssignableScopes` da função personalizada podem criar (ou excluir) funções personalizadas para uso nesses escopos. Por exemplo, os [Proprietários](built-in-roles.md#owner) e os [Administradores de Acesso do Usuário](built-in-roles.md#user-access-administrator) das assinaturas, grupos de recursos e recursos. |
| Atualizar uma função personalizada | `Microsoft.Authorization/ roleDefinitions/write` | Os usuários que recebem essa operação em todos os `AssignableScopes` da função personalizada podem atualizar as funções personalizadas nesses escopos. Por exemplo, os [Proprietários](built-in-roles.md#owner) e os [Administradores de Acesso do Usuário](built-in-roles.md#user-access-administrator) das assinaturas, grupos de recursos e recursos. |
| Exibir uma função personalizada | `Microsoft.Authorization/ roleDefinitions/read` | Os usuários que recebem essa operação em um escopo podem exibir as funções personalizadas que estão disponíveis para atribuição nesse escopo. Todas as funções internas permitem que funções personalizadas estejam disponíveis para atribuição. |

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
- [Criar ou atualizar funções personalizadas do Azure usando o portal do Azure (versão prévia)](custom-roles-portal.md)
- [Noções básicas sobre definições de função para recursos do Azure](role-definitions.md)
- [Solução de problemas com o RBAC para recursos do Azure](troubleshooting.md)
