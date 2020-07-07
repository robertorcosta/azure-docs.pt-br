---
title: 'Padrão: O operador count em uma definição de política'
description: Esse padrão do Azure Policy fornece um exemplo de como usar o operador count em uma definição de política.
ms.date: 06/29/2020
ms.topic: sample
ms.openlocfilehash: 807890b7fb08d790deff6e0be9e08ad91c4ec44d
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/30/2020
ms.locfileid: "85565751"
---
# <a name="azure-policy-pattern-the-count-operator"></a>Padrão do Azure Policy: o operador count

O operador [count](../concepts/definition-structure.md#count) avalia os membros de um alias de \[\*\].

## <a name="sample-policy-definition"></a>Definição de política de amostra

Essa definição de política [audita](../concepts/effects.md#audit) Grupos de Segurança de Rede configurados para permitir o tráfego RDP (Protocolo de Área de Trabalho Remota) de entrada.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json":::

### <a name="explanation"></a>Explicação

Os componentes principais do operador **count** são _campo_, _onde_ e a condição. Cada um é realçado no snippet de código abaixo.

- O _campo_ diz ao count qual [alias](../concepts/definition-structure.md#aliases) deve ser avaliado pelos membros. Aqui, examinamos a _matriz_ do alias **securityRules\[\*\]** do grupo de segurança de rede.
- _onde_ usa a linguagem da política para definir quais membros da _matriz_ atendem aos critérios. Neste exemplo, um operador lógico **allOf** agrupa três avaliações de condições diferentes das propriedades da _matriz do alias_: _direção_, _acesso_ e _destinationPortRange_.
- A condição de contagem neste exemplo é **maior**. A contagem é avaliada como verdadeira quando um ou mais membros da _matriz do alias_ corresponde à cláusula _onde_.

:::code language="json" source="~/policy-templates/patterns/pattern-count-operator.json" range="12-32" highlight="3,4,20":::

## <a name="next-steps"></a>Próximas etapas

- Revise outros [padrões e definições internas](./index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).