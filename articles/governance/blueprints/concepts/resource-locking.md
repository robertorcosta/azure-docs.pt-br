---
title: Compreender o bloqueio de recursos
description: Conheça as opções de bloqueio no Azure Blueprints para proteger os recursos ao atribuir um projeto.
ms.date: 03/25/2020
ms.topic: conceptual
ms.openlocfilehash: 9c4e2f4c6fd8f5fb574002217ca71d1e7d130ff7
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676745"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Entenda o bloqueio de recursos nos Blueprints do Azure

A criação de ambientes consistentes em escala só é realmente valiosa se houver um mecanismo para manter essa consistência. Este artigo explica como o bloqueio de recursos funciona em Blueprints do Azure. Para ver um exemplo de bloqueio de recursos e aplicação de _atribuições_de negação, consulte o tutorial [de proteção de novos recursos.](../tutorials/protect-new-resources.md)

> [!NOTE]
> Os bloqueios de recursos implantados pelo Azure Blueprints são aplicados apenas aos recursos implantados pela atribuição do projeto. Os recursos existentes, como os de grupos de recursos que já existem, não têm bloqueios adicionados a eles.

## <a name="locking-modes-and-states"></a>Estados e modos de bloqueio

O modo de bloqueio se aplica à atribuição do projeto e tem três opções: **Não bloquear,** **Ler apenas**ou Não **Excluir**. O modo de bloqueio é configurado durante a implantação de artefato em uma atribuição de blueprint. Um modo de bloqueio diferente pode ser definido pela atualização da atribuição de blueprint.
Os modos de bloqueio, no entanto, não podem ser alterados fora do Azure Blueprints.

Os recursos criados por artefatos em uma atribuição de projeto têm quatro estados: **Não bloqueado,** **Somente leitura,** **não pode editar / excluir**ou não **pode excluir**. Cada tipo de artefato pode estar no estado **Não Bloqueado**. A seguinte tabela pode ser usada para determinar o estado de um recurso:

|Mode|Tipo de recurso do artefato|Estado|Descrição|
|-|-|-|-|
|Não Bloquear|*|Não Bloqueado|Os recursos não são protegidos pelo Azure Blueprints. Esse estado também é usado para recursos adicionados a um artefato do grupo de recursos **Somente Leitura** ou **Não Excluir** fora de uma atribuição de blueprint.|
|Somente leitura|Resource group|Não é Possível Editar/Excluir|O grupo de recursos é somente leitura e as marcas no grupo de recursos não podem ser modificadas. Os recursos **Não Bloqueados** podem ser adicionados, movidos, alterados ou excluídos desse grupo de recursos.|
|Somente leitura|Não grupo de recursos|Somente leitura|O recurso não pode ser alterado de forma alguma – sem alterações e não pode ser excluído.|
|Não exclua|*|Não é Possível Excluir|Os recursos podem ser alterados, mas não podem ser excluídos. Os recursos **Não Bloqueados** podem ser adicionados, movidos, alterados ou excluídos desse grupo de recursos.|

## <a name="overriding-locking-states"></a>Substituindo os estados de bloqueio

Normalmente, é possível que alguém com o [controle de acesso baseado em função](../../../role-based-access-control/overview.md) (RBAC) apropriado na assinatura, como a função de 'Proprietário', possa alterar ou excluir qualquer recurso. Esse acesso não é o caso quando o Azure Blueprints aplica o bloqueio como parte de uma atribuição implantada. Se a atribuição foi definida com a opção **Somente Leitura** ou **Não Excluir**, nem mesmo o proprietário da assinatura pode executar a ação bloqueada no recurso protegido.

Isso protege a consistência do plano gráfico em definido e o ambiente em que ele foi projetado para criar a partir de exclusão acidental ou através de programação ou de alteração.

### <a name="assign-at-management-group"></a>Atribuir no grupo de gestão

Uma opção adicional para impedir que os proprietários de assinatura removessem uma atribuição de projeto é atribuir o projeto a um grupo de gerenciamento. Nesse cenário, apenas **os proprietários** do grupo de gestão têm as permissões necessárias para remover a atribuição do projeto.

Para atribuir o projeto a um grupo de gerenciamento em vez de uma assinatura, a chamada da API REST muda para ficar assim:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

O grupo de `{assignmentMG}` gerenciamento definido por deve estar dentro da hierarquia do grupo de gerenciamento ou ser o mesmo grupo de gerenciamento onde a definição do projeto é salva.

O corpo de solicitação da atribuição do projeto é assim:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
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

A principal diferença neste órgão de solicitação e um `properties.scope` sendo atribuído a uma assinatura é a propriedade. Essa propriedade necessária deve ser definida na assinatura a que a atribuição do projeto se aplica. A assinatura deve ser um filho direto da hierarquia do grupo de gerenciamento onde a atribuição do projeto é armazenada.

> [!NOTE]
> Um projeto atribuído ao escopo do grupo de gerenciamento ainda funciona como uma atribuição de projeto de nível de assinatura. A única diferença é quando a atribuição do projeto é armazenada para evitar que os proprietários de assinatura removessem a atribuição e os bloqueios associados.

## <a name="removing-locking-states"></a>Removendo os estados de bloqueio

Se for necessário modificar ou excluir um recurso protegido por uma atribuição, haverá duas maneiras de fazer isso.

- Atualizar a atribuição de blueprint para um modo de bloqueio igual a **Não Bloquear**
- Excluir a atribuição de blueprint

Quando a atribuição é removida, as fechaduras criadas pelo Azure Blueprints são removidas. No entanto, o recurso é deixado para trás e precisaria ser excluído por meios normais.

## <a name="how-blueprint-locks-work"></a>Como o plano gráfico bloqueios trabalho

Uma ação de negação [negar atribuições](../../../role-based-access-control/deny-assignments.md) do RBAC é aplicada aos recursos de artefato durante a atribuição de um blueprint se a atribuição selecionou a opção **Somente Leitura** ou **Não Excluir**. A ação de negação é adicionada pela identidade gerenciada da atribuição de blueprint e só pode ser removida dos recursos de artefato pela mesma identidade gerenciada. Esta medida de segurança impõe o mecanismo de bloqueio e impede a remoção do bloqueio do projeto fora do Azure Blueprints.

![Projeto nega atribuição em grupo de recursos](../media/resource-locking/blueprint-deny-assignment.png)

As propriedades de atribuição de [negação](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) de cada modo são as seguintes:

|Mode |Permissões.Ações |Permissões.NotActions |Diretores[i]. Tipo |ExcluaDiretores[i]. Id | NãoaplicarApplyToChildScopes |
|-|-|-|-|-|-|
|Somente leitura |**\*** |**\*/leia** |Sistemadefinido (Todos) |atribuição de projeto e definidos pelo usuário em **excluídosPrincipais** |Grupo de recursos - _verdadeiro_; Recurso - _falso_ |
|Não exclua |**\*/delete** | |Sistemadefinido (Todos) |atribuição de projeto e definidos pelo usuário em **excluídosPrincipais** |Grupo de recursos - _verdadeiro_; Recurso - _falso_ |

> [!IMPORTANT]
> O Gerenciador de Recursos do Azure armazena em cache os detalhes da atribuição de função por até 30 minutos. Como resultado, a ação de negação das atribuições de negação nos recursos de blueprint pode não entrar imediatamente em vigor. Durante esse período, talvez seja possível excluir um recurso destinado a ser protegido por bloqueios de blueprint.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Exclua um diretor de uma atribuição de negação

Em alguns cenários de design ou segurança, pode ser necessário excluir um principal da atribuição de [negação](../../../role-based-access-control/deny-assignments.md) que a atribuição de projeto cria. Esta etapa é feita na API REST, somando até cinco valores à matriz **excludedPrincipals** na propriedade **locks** ao [criar a atribuição](/rest/api/blueprints/assignments/createorupdate). A seguinte definição de atribuição é um exemplo de um órgão de solicitação que inclui **diretores excluídos:**

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

## <a name="exclude-an-action-from-a-deny-assignment"></a>Exclua uma ação de uma atribuição de negação

Semelhante à [exclusão de um principal](#exclude-a-principal-from-a-deny-assignment) em uma [atribuição de negação](../../../role-based-access-control/deny-assignments.md) em uma atribuição de projeto, você pode excluir [operações específicas de RBAC](../../../role-based-access-control/resource-provider-operations.md). Dentro do bloco **properties.locks,** no mesmo lugar que **excluiPrincipais,** pode ser adicionada uma **excludedActions:**

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

Embora **os excluídosPrincipais** devem ser explícitos, as entradas **excluídas de ações** podem fazer uso para a correspondência curinga `*` das operações rbac.

## <a name="next-steps"></a>Próximas etapas

- Siga o tutorial [de proteção de novos recursos.](../tutorials/protect-new-resources.md)
- Conheça o [ciclo de vida](lifecycle.md)do projeto .
- Saiba como usar [parâmetros estáticos e dinâmicos](parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](sequencing-order.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).