---
title: Compreender o bloqueio de recursos
description: Saiba mais sobre as opções de bloqueio em plantas do Azure para proteger recursos ao atribuir um plano gráfico.
ms.date: 04/24/2019
ms.topic: conceptual
ms.openlocfilehash: e042a4d117e28a2fd2228ce36f1be98a1da31e91
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057338"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Entenda o bloqueio de recursos nos Blueprints do Azure

A criação de ambientes consistentes em escala só é realmente valiosa se houver um mecanismo para manter essa consistência. Este artigo explica como o bloqueio de recursos funciona em Blueprints do Azure. Para ver um exemplo de bloqueio de recursos e aplicação de _atribuições de negação_, consulte o tutorial [protegendo novos recursos](../tutorials/protect-new-resources.md) .

## <a name="locking-modes-and-states"></a>Estados e modos de bloqueio

O modo de bloqueio se aplica à atribuição Blueprint e tem três opções: **não bloquear**, **somente leitura**ou **não excluir**. O modo de bloqueio é configurado durante a implantação de artefato em uma atribuição de blueprint. Um modo de bloqueio diferente pode ser definido pela atualização da atribuição de blueprint.
No entanto, os modos de bloqueio não podem ser alterados fora dos blueprints.

Os recursos criados por artefatos em uma atribuição Blueprint têm quatro Estados: **não bloqueado**, **somente leitura**, **não é possível editar/Excluir**ou **não pode excluir**. Cada tipo de artefato pode estar no estado **Não Bloqueado**. A seguinte tabela pode ser usada para determinar o estado de um recurso:

|Mode|Tipo de recurso do artefato|Estado|DESCRIÇÃO|
|-|-|-|-|
|Não Bloquear|*|Não Bloqueado|Os recursos não são protegidos pelos blueprints. Esse estado também é usado para recursos adicionados a um artefato do grupo de recursos **Somente Leitura** ou **Não Excluir** fora de uma atribuição de blueprint.|
|Somente leitura|Resource group|Não é Possível Editar/Excluir|O grupo de recursos é somente leitura e as marcas no grupo de recursos não podem ser modificadas. Os recursos **Não Bloqueados** podem ser adicionados, movidos, alterados ou excluídos desse grupo de recursos.|
|Somente leitura|Não grupo de recursos|Somente leitura|O recurso não pode ser alterado de forma alguma – sem alterações e não pode ser excluído.|
|Não exclua|*|Não é Possível Excluir|Os recursos podem ser alterados, mas não podem ser excluídos. Os recursos **Não Bloqueados** podem ser adicionados, movidos, alterados ou excluídos desse grupo de recursos.|

## <a name="overriding-locking-states"></a>Substituindo os estados de bloqueio

Normalmente, é possível que alguém com o [controle de acesso baseado em função](../../../role-based-access-control/overview.md) (RBAC) apropriado na assinatura, como a função de 'Proprietário', possa alterar ou excluir qualquer recurso. Esse acesso não é o caso quando o Blueprints aplica o bloqueio como parte de uma atribuição implantada. Se a atribuição foi definida com a opção **Somente Leitura** ou **Não Excluir**, nem mesmo o proprietário da assinatura pode executar a ação bloqueada no recurso protegido.

Isso protege a consistência do plano gráfico em definido e o ambiente em que ele foi projetado para criar a partir de exclusão acidental ou através de programação ou de alteração.

## <a name="removing-locking-states"></a>Removendo os estados de bloqueio

Se for necessário modificar ou excluir um recurso protegido por uma atribuição, haverá duas maneiras de fazer isso.

- Atualizar a atribuição de blueprint para um modo de bloqueio igual a **Não Bloquear**
- Excluir a atribuição de blueprint

Quando a atribuição é removida, os bloqueios criados por planos gráficos são removidos. No entanto, o recurso é deixado para trás e precisaria ser excluído por meios normais.

## <a name="how-blueprint-locks-work"></a>Como o plano gráfico bloqueios trabalho

Uma ação de negação [negar atribuições](../../../role-based-access-control/deny-assignments.md) do RBAC é aplicada aos recursos de artefato durante a atribuição de um blueprint se a atribuição selecionou a opção **Somente Leitura** ou **Não Excluir**. A ação de negação é adicionada pela identidade gerenciada da atribuição de blueprint e só pode ser removida dos recursos de artefato pela mesma identidade gerenciada. Essa medida de segurança impõe o mecanismo de bloqueio e impede a remoção do bloqueio do blueprint fora do Blueprints.

![Atribuição de negação de plano gráfico no grupo de recursos](../media/resource-locking/blueprint-deny-assignment.png)

As [Propriedades de atribuição de negação](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) de cada modo são as seguintes:

|Mode |Permissões. ações |Permissões. \ ações |Entidades de segurança [i]. Escreva |ExcludePrincipals [i]. Sessão | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Somente leitura |**\*** |**\*/Read** |SystemDefined (todos) |atribuição de Blueprint e definida pelo usuário em **excludedPrincipals** |Grupo de recursos- _verdadeiro_; Recurso- _falso_ |
|Não exclua |**\*/Delete** | |SystemDefined (todos) |atribuição de Blueprint e definida pelo usuário em **excludedPrincipals** |Grupo de recursos- _verdadeiro_; Recurso- _falso_ |

> [!IMPORTANT]
> O Gerenciador de Recursos do Azure armazena em cache os detalhes da atribuição de função por até 30 minutos. Como resultado, a ação de negação das atribuições de negação nos recursos de blueprint pode não entrar imediatamente em vigor. Durante esse período, talvez seja possível excluir um recurso destinado a ser protegido por bloqueios de blueprint.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Excluir uma entidade de segurança de uma atribuição de negação

Em alguns cenários de design ou segurança, pode ser necessário excluir uma entidade da atribuição de [negação](../../../role-based-access-control/deny-assignments.md) criada pela atribuição Blueprint. Isso é feito na API REST adicionando até cinco valores à matriz **excludedPrincipals** na propriedade **Locks** ao [criar a atribuição](/rest/api/blueprints/assignments/createorupdate). Este é um exemplo de um corpo de solicitação que inclui **excludedPrincipals**:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>Excluir uma ação de uma atribuição de negação

Semelhante à [exclusão de uma entidade de segurança](#exclude-a-principal-from-a-deny-assignment) em uma [atribuição de negação](../../../role-based-access-control/deny-assignments.md) em uma atribuição de Blueprint, você pode excluir [operações RBAC](../../../role-based-access-control/resource-provider-operations.md)específicas. No bloco **Properties. Locks** , no mesmo lugar em que **excludedPrincipals** é, um **excludedActions** pode ser adicionado:

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

Embora **excludedPrincipals** deva ser explícito, as entradas de **excludedActions** podem fazer uso de `*` para correspondência de curingas de operações de RBAC.

## <a name="next-steps"></a>Próximas etapas

- Siga o tutorial [proteger novos recursos](../tutorials/protect-new-resources.md) .
- Saiba mais sobre o [ciclo de vida do blueprint](lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](sequencing-order.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).