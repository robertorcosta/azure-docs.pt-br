---
title: Entender a ordem da sequência de implantação
description: Saiba mais sobre a ordem padrão em que os artefatos de projeto são implantados durante uma atribuição de projeto e como personalizar a ordem de implantação.
ms.date: 08/22/2019
ms.topic: conceptual
ms.openlocfilehash: 51026862c989f15acf6d3e21702cfcfc8b2b27b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74128826"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Entenda a sequência de implantação nos Blueprints do Azure

O Azure Blueprints usa uma **ordem de seqüenciamento** para determinar a ordem de criação de recursos ao processar a atribuição de uma definição de projeto. Este artigo o guiará pelos seguintes conceitos:

- A ordem de sequenciamento padrão usada
- Como personalizar o pedido
- Como o pedido personalizado é processado

Existem variáveis nos exemplos JSON que você precisa substituir por seus próprios valores:

- `{YourMG}`: substitua pelo nome do seu grupo de gerenciamento

## <a name="default-sequencing-order"></a>Ordem de sequenciamento padrão

Se a definição do projeto não contiver nenhuma diretiva para a ordem de implantação de artefatos ou a diretiva for nula, então a seguinte ordem será usada:

- Designação de função de **nível de assinatura** artefatos classificados por nome de artefato
- Designação de política do **nível de assinatura** artefatos classificados pelo nome do artefato
- Nível de assinatura **Artefatos do modelo** do Azure Resource Manager classificados por nome de artefato
- **Artefatos do grupo de recursos** (incluindo artefatos derivados) classificados por nome de espaço reservado

Em cada artefato do **grupo de recursos**, a seguinte ordem de sequência é usada para artefatos a serem criados dentro desse grupo de recursos:

- Filho de grupo de recursos **atribuição de função** artefatos classificados por nome de artefato
- Filho de grupo de recursos **atribuição de política** artefatos classificados por nome de artefato
- Filho do grupo de recursos **Artefatos do modelo do Azure Resource Manager** classificados por nome de artefato

> [!NOTE]
> O uso de [artefatos cria](../reference/blueprint-functions.md#artifacts) uma dependência implícita do artefato a que está sendo referido.

## <a name="customizing-the-sequencing-order"></a>Personalizando o pedido de sequenciamento

Ao compor grandes definições de projeto, pode ser necessário que os recursos sejam criados em uma ordem específica. O padrão de uso mais comum deste cenário é quando uma definição de projeto inclui vários modelos do Azure Resource Manager. Blueprints lida com esse padrão permitindo que a ordem de sequenciamento seja definida.

A ordenação é realizada definindo uma propriedade `dependsOn` no JSON. A definição do projeto, para grupos de recursos e objetos de artefato, suportam essa propriedade. `dependsOn` é uma matriz de cadeia de caracteres de nomes de artefatos que o artefato específico precisa ser criado antes de ser criado.

> [!NOTE]
> Ao criar objetos de projeto, cada recurso de artefato obtém seu nome a partir do nome do arquivo, se estiver usando [powershell](/powershell/module/az.blueprint/new-azblueprintartifact)ou o ponto final da URL, se estiver usando [a API REST](/rest/api/blueprints/artifacts/createorupdate).
> _resourceAs_ referências em artefatos devem corresponder às definidas na definição do projeto.

### <a name="example---ordered-resource-group"></a>Exemplo - grupo de recursos encomendado

Esta definição de projeto de exemplo tem um grupo de recursos `dependsOn`que definiu uma ordem de seqüenciamento personalizada declarando um valor para , juntamente com um grupo de recursos padrão. Nesse caso, o artefato denominado **assignPolicyTags** será processado antes do grupo de recursos **ordered-rg**.
**standard-rg** será processado de acordo com a ordem de sequenciamento padrão.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Exemplo - artefato com ordem personalizada

Este exemplo é um artefato de política que depende de um modelo do Azure Resource Manager. Por padrão, um artefato de política seria criado antes do modelo do Azure Resource Manager. Essa ordenação permite que o artefato de política aguarde o modelo do Azure Resource Manager ser criado.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Exemplo - artefato de modelo de nível de assinatura, dependendo de um grupo de recursos

Este exemplo é para um modelo de Gerenciador de recursos implantado no nível de assinatura para depender de um grupo de recursos. No pedido padrão, os artefatos de nível de assinatura seriam criados antes de quaisquer grupos de recursos e artefatos infantis nesses grupos de recursos. O grupo de recursos é definido na definição do projeto como esta:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

O artefato do modelo de nível de assinatura, dependendo do grupo de recursos **wait-for-me,** é definido assim:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>Processando a sequência customizada

Durante o processo de criação, uma classificação topológica é usada para criar o gráfico de dependência dos artefatos de blueprints. A verificação assegura que cada nível de dependência entre grupos de recursos e artefatos seja suportado.

Se uma dependência de artefato for declarada que não alteraria a ordem padrão, nenhuma alteração será feita. Um exemplo é um grupo de recursos que depende de uma política no nível da assinatura. Outro exemplo é uma atribuição de política filho 'standard-rg' do grupo de recursos que depende da atribuição de função filho 'standard-rg' do grupo de recursos. Em ambos os casos, o `dependsOn` não teria alterado a ordem de sequenciamento padrão e nenhuma alteração seria feita.

## <a name="next-steps"></a>Próximas etapas

- Conheça o [ciclo de vida](lifecycle.md)do projeto .
- Saiba como usar [parâmetros estáticos e dinâmicos](parameters.md).
- Saiba como usar o [bloqueio de recurso de blueprint](resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).