---
title: Detalhes da estrutura de atribuição de políticas
description: Descreve a definição de atribuição de diretiva usada pela Diretiva Azure para relacionar definições de políticas e parâmetros a recursos para avaliação.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: cdb2fc0c6f057ece44383f68bc79fca54507db9b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683212"
---
# <a name="azure-policy-assignment-structure"></a>Estrutura de atribuição do Azure Policy

As atribuições de políticas são usadas pela Azure Policy para definir quais recursos são atribuídos quais políticas ou iniciativas. A atribuição de políticas pode determinar os valores dos parâmetros para esse grupo de recursos no momento da atribuição, possibilitando a reutilização de definições de políticas que abordam as mesmas propriedades de recursos com diferentes necessidades de conformidade.

Você usa json para criar uma atribuição de política. A atribuição de diretiva contém elementos para:

- nome de exibição
- descrição
- metadata
- modo de aplicação
- escopos excluídos
- definição de política
- parâmetros

Por exemplo, o JSON a seguir mostra uma atribuição de diretiva no modo _DoNotEnforce_ com parâmetros dinâmicos:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Todas as amostras de política do Azure estão em [amostras de Política Do Azure](../samples/index.md).

## <a name="display-name-and-description"></a>Nome de exibição e descrição

Você usa **displayName** e **descrição** para identificar a atribuição de políticas e fornecer contexto para seu uso com o conjunto específico de recursos. **displayName** tem um comprimento máximo de _128_ caracteres e **description** tem um comprimento máximo de _512_ caracteres.

## <a name="enforcement-mode"></a>Modo de execução

A propriedade **enforcementMode** fornece aos clientes a capacidade de testar o resultado de uma política sobre os recursos existentes sem iniciar o efeito de política ou desencadear entradas no registro de atividades do [Azure](../../../azure-monitor/platform/platform-logs-overview.md). Esse cenário é comumente referido como "E se" e se alinha a práticas seguras de implantação. **enforcementMode** é diferente do efeito [Disabled,](./effects.md#disabled) pois esse efeito impede que a avaliação de recursos aconteça.

Esta propriedade tem os seguintes valores:

|Mode |Valor JSON |Type |Remediar manualmente |Entrada de registro de atividades |Descrição |
|-|-|-|-|-|-|
|habilitado |Padrão |string |Sim |Sim |O efeito de política é aplicado durante a criação ou atualização de recursos. |
|Desabilitado |Não faça cumprir |string |Sim |Não | O efeito de política não é aplicado durante a criação ou atualização de recursos. |

Se **enforcementMode** não for especificado em uma definição de política ou iniciativa, o _padrão de_ valor será usado. [As tarefas de remediação](../how-to/remediate-resources.md) podem ser iniciadas para [implantarpolíticasIfNotExist,](./effects.md#deployifnotexists) mesmo quando **o enforcementMode** é definido como _DoNotEnforce_.

## <a name="excluded-scopes"></a>Escopos excluídos

O **escopo** da atribuição inclui todos os contêineres de recursos infantis e recursos infantis. Se um contêiner de recursos filho ou recurso filho não tiver a definição aplicada, cada um pode ser excluído da avaliação definindo **notScopes**. Esta propriedade é uma matriz para permitir a exclusão de um ou mais contêineres de recursos ou recursos da avaliação. **notScopes** podem ser adicionados ou atualizados após a criação da atribuição inicial.

## <a name="policy-definition-id"></a>ID de definição de política

Este campo deve ser o nome completo do caminho de uma definição de política ou de uma definição de iniciativa.
`policyDefinitionId`é uma seqüência e não uma matriz. Recomenda-se que, se várias políticas forem frequentemente atribuídas em conjunto, use uma [iniciativa](./definition-structure.md#initiatives) em vez disso.

## <a name="parameters"></a>Parâmetros

Este segmento da atribuição de políticas fornece os valores para os parâmetros definidos na [definição da política ou definição de iniciativa](./definition-structure.md#parameters).
Esse projeto permite reutilizar uma definição de política ou iniciativa com diferentes recursos, mas verificar se há diferentes valores ou resultados de negócios.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

Neste exemplo, os parâmetros previamente definidos na definição da política são `prefix` e `suffix`. Esta atribuição `prefix` de política em `suffix` particular define-se para **DeptA** e para **-LC**. A mesma definição de política é reutilizável com um conjunto diferente de parâmetros para um departamento diferente, reduzindo a duplicação e a complexidade das definições de políticas, proporcionando flexibilidade.

## <a name="next-steps"></a>Próximas etapas

- Conheça a estrutura de [definição de políticas](./definition-structure.md).
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Aprenda a [remediar recursos não compatíveis.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [organize seus recursos com grupos de gerenciamento do Azure.](../../management-groups/overview.md)