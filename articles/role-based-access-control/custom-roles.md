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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062179"
---
# <a name="custom-roles-for-azure-resources"></a>Funções personalizadas para recursos do Azure

> [!IMPORTANT]
> A adição `AssignableScopes` de um grupo de gerenciamento está atualmente em pré-visualização.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se as [funções incorporadas para os recursos do Azure](built-in-roles.md) não atenderem às necessidades específicas da sua organização, você poderá criar suas próprias funções personalizadas. Assim como as funções incorporadas, você pode atribuir funções personalizadas a usuários, grupos e diretores de serviços nos escopos de grupo de gerenciamento, assinatura e grupo de recursos.

As funções personalizadas podem ser compartilhadas entre assinaturas que confiam no mesmo diretório Azure AD. Há um limite de **5.000** funções personalizadas por diretório. (Para a Azure Germany e a Azure China 21Vianet, o limite é de 2.000 funções personalizadas.) As funções personalizadas podem ser criadas usando o portal Azure (Preview), Azure PowerShell, Azure CLI ou a API REST.

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
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Quando você cria uma função personalizada, ela aparece no portal do Azure com um ícone de recurso laranja.

![Ícone personalizado da função](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Etapas para criar uma função personalizada

1. Decida como você quer criar o papel personalizado

    Você pode criar funções personalizadas usando [o portal Azure](custom-roles-portal.md) (Preview), [Azure PowerShell,](custom-roles-powershell.md) [Azure CLI](custom-roles-cli.md)ou a [API REST](custom-roles-rest.md).

1. Determinar as permissões necessárias

    Ao criar uma função personalizada, você precisa conhecer as operações do provedor de recursos que estão disponíveis para definir suas permissões. Para visualizar a lista de operações, consulte as operações do provedor de [recursos do Azure Resource Manager](resource-provider-operations.md). Você adicionará as `Actions` operações às ou `NotActions` propriedades da [definição de função](role-definitions.md). Se você tiver operações de dados, você irá adicioná-los às `DataActions` propriedades ou. `NotDataActions`

1. Criar a função personalizada

    Normalmente, você começa com uma função interna existente e depois a modifica conforme suas necessidades. Depois, use os comandos [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) ou [az role definition create](/cli/azure/role/definition#az-role-definition-create) para criar a função personalizada. Para criar uma função personalizada, você precisa ter a permissão `Microsoft.Authorization/roleDefinitions/write` em todo `AssignableScopes`, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso de Usuário](built-in-roles.md#user-access-administrator).

1. Testar a função personalizada

    Quando tiver sua função personalizada, será necessário testá-la para verificar se funciona como o esperado. Se você precisar fazer ajustes mais tarde, você pode atualizar a função personalizada.

Para obter um tutorial passo a passo sobre como criar uma função personalizada, consulte [Tutorial: criar uma função personalizada usando o Azure PowerShell](tutorial-custom-role-powershell.md) ou [Tutorial: criar uma função personalizada usando a CLI do Azure](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Propriedades da função personalizada

Uma função personalizada tem as seguintes propriedades.

| Propriedade | Obrigatório | Type | Descrição |
| --- | --- | --- | --- |
| `Name` | Sim | String | O nome de exibição da função personalizada. Embora uma definição de função seja um grupo de gerenciamento ou recurso de nível de assinatura, uma definição de função pode ser usada em várias assinaturas que compartilham o mesmo diretório Azure AD. Esse nome de exibição precisa ser exclusivo no escopo do diretório do Azure AD. Pode incluir letras, números, espaços e caracteres especiais. O número máximo de caracteres é 128. |
| `Id` | Sim | String | A ID exclusiva da função personalizada. Para o Azure PowerShell e a CLI do Azure, essa ID é gerada automaticamente ao criar uma nova função. |
| `IsCustom` | Sim | String | Indica se esta é uma função personalizada. Defina como `true` para funções personalizadas. |
| `Description` | Sim | String | A descrição da função personalizada. Pode incluir letras, números, espaços e caracteres especiais. O número máximo de caracteres é 1024. |
| `Actions` | Sim | String[] | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento permitidas pela função. Para obter mais informações, consulte [Ações](role-definitions.md#actions). |
| `NotActions` | Não | String[] | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento que são excluídas do `Actions` permitido. Para obter mais informações, consulte [NotActions](role-definitions.md#notactions). |
| `DataActions` | Não | String[] | Uma matriz de cadeias de caracteres que especifica as operações de dados permitidas pela função em seus dados dentro desse objeto. Se você criar uma `DataActions`função personalizada com , essa função não poderá ser atribuída no escopo do grupo de gerenciamento. Para obter mais informações, consulte [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | Não | String[] | Uma matriz de cadeias de caracteres que especifica as operações de dados excluídas do `DataActions` permitido. Para obter mais informações, consulte [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Sim | String[] | Uma matriz de cadeias de caracteres que especifica os escopos para os quais a função personalizada está disponível para atribuição. Você só pode definir `AssignableScopes` um grupo de gerenciamento em uma função personalizada. A adição `AssignableScopes` de um grupo de gerenciamento está atualmente em pré-visualização. Para obter mais informações, consulte [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Quem pode criar, excluir, atualizar ou exibir uma função personalizada

Assim como funções internas, a propriedade `AssignableScopes` especifica os escopos para os quais a função está disponível para atribuição. A propriedade `AssignableScopes` de uma função personalizada também controla quem pode criar, excluir, atualizar ou exibir a função personalizada.

| Tarefa | Operação | Descrição |
| --- | --- | --- |
| Criar/excluir uma função personalizada | `Microsoft.Authorization/ roleDefinitions/write` | Os usuários que recebem essa operação em todos os `AssignableScopes` da função personalizada podem criar (ou excluir) funções personalizadas para uso nesses escopos. Por exemplo, [Proprietários](built-in-roles.md#owner) e [Administradores](built-in-roles.md#user-access-administrator) de acesso ao usuário de grupos de gerenciamento, assinaturas e grupos de recursos. |
| Atualizar uma função personalizada | `Microsoft.Authorization/ roleDefinitions/write` | Os usuários que recebem essa operação em todos os `AssignableScopes` da função personalizada podem atualizar as funções personalizadas nesses escopos. Por exemplo, [Proprietários](built-in-roles.md#owner) e [Administradores](built-in-roles.md#user-access-administrator) de acesso ao usuário de grupos de gerenciamento, assinaturas e grupos de recursos. |
| Exibir uma função personalizada | `Microsoft.Authorization/ roleDefinitions/read` | Os usuários que recebem essa operação em um escopo podem exibir as funções personalizadas que estão disponíveis para atribuição nesse escopo. Todas as funções internas permitem que funções personalizadas estejam disponíveis para atribuição. |

## <a name="custom-role-limits"></a>Limites de função personalizados

A lista a seguir descreve os limites para funções personalizadas.

- Cada diretório pode ter até **5000** funções personalizadas.
- Azure Germany e Azure China 21Vianet podem ter até 2000 funções personalizadas para cada diretório.
- Você não `AssignableScopes` pode definir`"/"`para o escopo raiz ().
- Você só pode definir `AssignableScopes` um grupo de gerenciamento em uma função personalizada. A adição `AssignableScopes` de um grupo de gerenciamento está atualmente em pré-visualização.
- Funções `DataActions` personalizadas com não podem ser atribuídas no escopo do grupo de gerenciamento.
- O Azure Resource Manager não valida a existência do grupo de gerenciamento no escopo atribuível à definição de função.

Para obter mais informações sobre funções personalizadas e grupos de gerenciamento, consulte [Organize seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="next-steps"></a>Próximas etapas
- [Criar ou atualizar funções personalizadas do Azure usando o portal Azure (Preview)](custom-roles-portal.md)
- [Noções básicas sobre definições de função para recursos do Azure](role-definitions.md)
- [Solução de problemas com o RBAC para recursos do Azure](troubleshooting.md)
