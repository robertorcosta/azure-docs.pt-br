---
title: Como proteger sua hierarquia de recursos – Governança do Azure
description: Saiba como proteger sua hierarquia de recursos com configurações de hierarquia que incluem a definição do grupo de gerenciamento padrão.
ms.date: 08/10/2020
ms.topic: conceptual
ms.openlocfilehash: 2dd6791e152ba3ef02f6e6f710589cbe7d3442bc
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056611"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Como proteger a hierarquia de recursos

Seus recursos, grupos de recursos, assinaturas, grupos de gerenciamento e locatários compõem sua hierarquia de recursos. As configurações no grupo de gerenciamento raiz, como funções personalizadas do Azure ou atribuições de política de Azure Policy, podem afetar todos os recursos em sua hierarquia de recursos. É importante proteger a hierarquia de recursos contra alterações que possam afetar negativamente todos os recursos.

Os grupos de gerenciamento agora têm configurações de hierarquia que permitem ao administrador de locatários controlar esses comportamentos. Este artigo aborda cada uma das configurações de hierarquia disponíveis e como defini-las.

## <a name="rbac-permissions-for-hierarchy-settings"></a>Permissões RBAC para configurações de hierarquia

A configuração de qualquer uma das configurações de hierarquia requer as duas operações RBAC a seguir no grupo de gerenciamento raiz:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Essas operações só permitem que um usuário leia e atualize as configurações de hierarquia. As operações não fornecem nenhum outro acesso à hierarquia do grupo de gerenciamento ou aos recursos na hierarquia. Ambas as operações estão disponíveis no **administrador de configurações de hierarquia**de funções internas do Azure.

## <a name="setting---default-management-group"></a>Configuração – Grupo de gerenciamento padrão

Por padrão, uma nova assinatura adicionada dentro de um locatário é adicionada como um membro do grupo de gerenciamento raiz. Se atribuições de política, controle de acesso baseado em função (RBAC) e outros constructos de governança forem atribuídos ao grupo de gerenciamento raiz, eles afetarão imediatamente essas novas assinaturas. Por esse motivo, muitas organizações não aplicam esses constructos no grupo de gerenciamento raiz, embora esse seja o local recomendado para atribuí-los. Em outros casos, um conjunto mais restritivo de controles é o mais indicado para novas assinaturas, mas não deve ser atribuído a todas as assinaturas. Essa configuração dá suporte a ambos os casos de uso.

Ao permitir que o grupo de gerenciamento padrão para novas assinaturas seja definido, as construções de governança de toda a organização podem ser aplicadas no grupo de gerenciamento raiz e um grupo de gerenciamento separado com atribuições de política ou atribuições de função do Azure mais adequadas para uma nova assinatura pode ser definido.

Para definir essa configuração, o ponto de extremidade da API REST de [Configurações de Hierarquia](/rest/api/resources/hierarchysettings) é chamado. Para fazer isso, use os seguintes URI da API REST e formato de corpo. Substitua `{rootMgID}` pela ID do grupo de gerenciamento raiz e `{defaultGroupID}` pela ID do grupo de gerenciamento para se tornar o grupo de gerenciamento padrão:

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Corpo da solicitação

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Para definir o grupo de gerenciamento padrão de volta para o grupo de gerenciamento raiz, use o mesmo ponto de extremidade e defina **defaultManagementGroup** como um valor de `/providers/Microsoft.Management/managementGroups/{rootMgID}`.

## <a name="setting---require-authorization"></a>Configuração – Requer autorização

Qualquer usuário, por padrão, pode criar novos grupos de gerenciamento dentro de um locatário. Os administradores de um locatário talvez queiram apenas fornecer essas permissões a usuários específicos para manter a consistência e a conformidade na hierarquia do grupo de gerenciamento. Se habilitada, um usuário solicita a operação `Microsoft.Management/managementGroups/write` no grupo de gerenciamento raiz para criar novos grupos de gerenciamento filho.

Para definir essa configuração, o ponto de extremidade da API REST de [Configurações de Hierarquia](/rest/api/resources/hierarchysettings) é chamado. Para fazer isso, use os seguintes URI da API REST e formato de corpo. Esse valor é um _booliano_, portanto, forneça **true** ou **false** para o valor. Um valor de **true** habilita esse método de proteção da hierarquia do grupo de gerenciamento:

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Corpo da solicitação

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Para desativar a configuração, use o mesmo ponto de extremidade e defina **requireAuthorizationForGroupCreation** com o valor **false**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre grupos de gerenciamento, consulte:

- [Criar grupos de gerenciamento para organizar recursos do Azure](../create.md)
- [Como alterar, excluir ou gerenciar seus grupos de gerenciamento](../manage.md)
