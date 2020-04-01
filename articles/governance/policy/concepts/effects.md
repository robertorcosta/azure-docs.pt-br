---
title: Entender como funcionam os efeitos
description: As definições de Diretiva do Azure têm vários efeitos que determinam como a conformidade é gerenciada e relatada.
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 0330cb5c732921efda3627dec92e486657097d82
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422448"
---
# <a name="understand-azure-policy-effects"></a>Compreender os efeitos do Azure Policy

Cada definição de política no Azure Policy tem um único efeito. Esse efeito determina o que acontece quando a regra de política é avaliada para correspondência. Os efeitos se comportam de modo diferente caso sejam para um novo recurso, um recurso atualizado ou um recurso existente.

Esses efeitos são atualmente suportados em uma definição de política:

- [Acrescentar](#append)
- [Auditoria](#audit)
- [AuditIfNotExiste](#auditifnotexists)
- [Negar](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Desativado](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (visualização)
- [EnforceRegoPolicy](#enforceregopolicy) (visualização)
- [Modificar](#modify)

## <a name="order-of-evaluation"></a>Ordem de avaliação

Solicitações para criar ou atualizar um recurso por meio do Azure Resource Manager são avaliadas primeiro pelo Azure Policy. A Diretiva Do Azure cria uma lista de todas as atribuições que se aplicam ao recurso e, em seguida, avalia o recurso em relação a cada definição. A Azure Policy processa vários dos efeitos antes de entregar a solicitação ao provedor de recursos apropriado. Isso evita o processamento desnecessário por um Provedor de Recursos quando um recurso não atende aos controles de governança projetados da Azure Policy.

- **Desabilitado** é marcado primeiro para determinar se a regra de política deve ser avaliada.
- **O apêndice** e **o Modify** são então avaliados. Uma vez que qualquer um poderia alterar a solicitação, uma alteração feita pode impedir uma auditoria ou negar o efeito de acionar.
- **Negar** é avaliada. Ao avaliar o efeito negar antes da auditoria, evita-se o log duplo de um recurso indesejado.
- **Audit** é avaliado antes de a solicitação ir para o provedor de recursos.

Depois que o provedor de recursos retorna um código de êxito, **AuditIfNotExists** e **DeployIfNotExists** fazem a avaliação para determinar se é necessário fazer registro em log para aumentar a conformidade.

Atualmente, não há nenhuma ordem de avaliação para os efeitos **EnforceOPAConstraint** ou **EnforceReGoPolicy.**

## <a name="disabled"></a>Desabilitado

Esse efeito é útil para testar situações ou quando a definição de política tiver parametrizado o efeito. Essa flexibilidade possibilita desabilitar uma única atribuição em vez de desabilitar todas as atribuições da política.

Uma alternativa ao efeito Disabled é o **EnforcementMode,** que é definido na atribuição de políticas.
Quando **o enforcementMode** é _desativado,_ os recursos ainda são avaliados. O registro, como registros de atividades, e o efeito de política não ocorrem. Para obter mais informações, consulte [a atribuição de políticas - modo de aplicação](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Acrescentar

O efeito acrescentar é usado para adicionar outros campos ao recurso solicitado durante a criação ou atualização. Um exemplo comum é especificar IPs permitidos para um recurso de armazenamento.

> [!IMPORTANT]
> O apêndice destina-se a ser usado com propriedades não-tag. Embora o Apêndice possa adicionar tags a um recurso durante uma solicitação de criação ou atualização, recomenda-se usar o efeito [Modificar](#modify) para tags.

### <a name="append-evaluation"></a>Avaliação de acréscimo

Append é avaliado antes que a solicitação seja processada por um provedor de recursos durante a criação ou a atualização de um recurso. O efeito append adiciona o campos ao recurso quando a condição **if** da regra de política for atendida. Se o efeito append substituir um valor na solicitação original por um valor diferente, ele atuará como um efeito Negar e rejeitará a solicitação. Para anexar um novo valor a uma matriz existente, use a versão **[\*]** do alias.

Quando uma definição de política usando o efeito append é executada como parte de um ciclo de avaliação, ela não faz alterações em recursos já existentes. Em vez disso, ela marca qualquer recurso que atende a condição **se** como não conforme.

### <a name="append-properties"></a>Propriedades de acréscimo

Um efeito acrescentar tem apenas uma matriz **detalhes**, que é necessária. Como **detalhes** é uma matriz, pode levar um único par **valor do campo** ou múltiplos. Confira a [estrutura de definição](definition-structure.md#fields) para saber a lista de campos aceitáveis.

### <a name="append-examples"></a>Exemplos de acréscimo

Exemplo 1: Um único par **de campo/valor** usando um [alias](definition-structure.md#aliases) **não-\*[ ]** com um **valor** de matriz para definir regras IP em uma conta de armazenamento. Quando o alias não-**\*[ ]** é uma matriz, o efeito anexa o **valor** como toda a matriz. Se já existir a matriz, ocorre um evento de negação como resultado do conflito.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Exemplo 2: Um único par **de campo/valor** usando um [alias](definition-structure.md#aliases) **\*com** um **valor** de matriz para definir regras IP em uma conta de armazenamento. Usando o **\*alias []** o efeito anexa o **valor** a uma matriz potencialmente pré-existente. Se a matriz ainda não existir, ela será criada.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="modify"></a>Modificar

Modify é usado para adicionar, atualizar ou remover tags em um recurso durante a criação ou atualização. Um exemplo comum é atualizar tags em recursos como costCenter. Uma diretiva Modificar `mode` sempre deve ter sido definida _como Indexada,_ a menos que o recurso de destino seja um grupo de recursos. Os recursos não compatíveis existentes podem ser remediados com uma [tarefa de remediação](../how-to/remediate-resources.md). Uma única regra modificar pode ter qualquer número de operações.

> [!IMPORTANT]
> Atualmente, modificar é apenas para uso com tags. Se você estiver gerenciando tags, recomenda-se usar Modificar em vez de Anexar como Modificar fornece tipos de operação adicionais e a capacidade de remediar os recursos existentes. No entanto, o Apêndice é recomendado se você não for capaz de criar uma identidade gerenciada.

### <a name="modify-evaluation"></a>Modificar avaliação

Modificar avaliações antes que a solicitação seja processada por um Provedor de Recursos durante a criação ou atualização de um recurso. Modificar adiciona ou atualiza tags em um recurso quando a condição **se** da regra de diretiva for atendida.

Quando uma definição de diretiva usando o efeito Modificar é executada como parte de um ciclo de avaliação, ela não faz alterações nos recursos que já existem. Em vez disso, ela marca qualquer recurso que atende a condição **se** como não conforme.

### <a name="modify-properties"></a>Modificar propriedades

A propriedade **details** do efeito Modificar tem todas as subpropriedades que definem as permissões necessárias para a remediação e as **operações** usadas para adicionar, atualizar ou remover valores de tag.

- **roleDefinitionIds** [obrigatório]
  - Essa propriedade deve incluir uma matriz de cadeias de caracteres que correspondem à ID de controle de acesso baseado em função que pode ser acessada pela assinatura. Para obter mais informações, confira [correção – configurar a definição de política](../how-to/remediate-resources.md#configure-policy-definition).
  - O papel definido deve incluir todas as operações concedidas à função [contribuinte.](../../../role-based-access-control/built-in-roles.md#contributor)
- **operações** [necessárias]
  - Uma matriz de todas as operações de tag a serem concluídas em recursos correspondentes.
  - Propriedades:
    - **operação** [necessária]
      - Define que ação tomar em um recurso correspondente. As opções são: _addOrReplace,_ _Add,_ _Remove_. _Adicionar_ comporta-se semelhante ao efeito [Apêndice.](#append)
    - **campo** [necessário]
      - A tag para adicionar, substituir ou remover. Os nomes das marcas devem aderir à mesma convenção de nomeação para outros [campos](./definition-structure.md#fields).
    - **valor** (opcional)
      - O valor para definir a tag.
      - Esta propriedade é necessária se **a operação** for _adicionarOrReplace_ ou _Add_.

### <a name="modify-operations"></a>Modificar operações

O array de propriedade de **operações** permite alterar várias tags de diferentes maneiras a partir de uma única definição de política. Cada operação é composta por propriedades de **operação,** **campo**e **valor.** A operação determina o que a tarefa de remediação faz com as tags, o campo determina qual tag é alterada e o valor define a nova configuração para essa tag. O exemplo abaixo faz as seguintes alterações de tag:

- Define `environment` a tag como "Teste", mesmo que ela já exista com um valor diferente.
- Remove a `TempResource`tag .
- Define `Dept` a marca para o parâmetro de política _DeptName_ configurado na atribuição de diretiva.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

A propriedade **de operação** tem as seguintes opções:

|Operação |Descrição |
|-|-|
|adicionarOrSubstituir |Adiciona a tag e o valor definidos ao recurso, mesmo que a tag já exista com um valor diferente. |
|Adicionar |Adiciona a tag e o valor definidos ao recurso. |
|Remover |Remove a tag definida do recurso. |

### <a name="modify-examples"></a>Modificar exemplos

Exemplo 1: `environment` Adicione a tag `environment` e substitua as tags existentes por "Teste":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Exemplo 2: `env` Remova a `environment` tag e adicione `environment` a tag ou substitua as tags existentes por um valor parametrizado:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## <a name="deny"></a>Negar

O efeito deny é usado para impedir uma solicitação de recurso que não corresponde aos padrões definidos em uma definição de política e leva à falha da solicitação.

### <a name="deny-evaluation"></a>Avaliação de deny

Ao criar ou atualizar um recurso, o efeito deny impede a solicitação antes de ser enviada ao provedor de recursos. A solicitação é retornada como um `403 (Forbidden)`. No portal, o Forbidden pode ser exibido como um status sobre a implantação que foi impedida pela atribuição de política.

Durante a avaliação dos recursos existentes, os recursos que correspondem a uma definição de política de negação são marcados como fora de conformidade.

### <a name="deny-properties"></a>Propriedades de deny

O efeito deny não têm propriedades adicionais para uso na condição **then** da definição de política.

### <a name="deny-example"></a>Exemplo de deny

Exemplo: usando o efeito negar.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Audit

Audit é usado para criar um evento de aviso no log de atividades ao avaliar um recurso fora de conformidade, mas ela não para a solicitação.

### <a name="audit-evaluation"></a>Avaliação de auditoria

A auditoria é o último efeito verificado pela Azure Policy durante a criação ou atualização de um recurso. Em seguida, a Diretiva Azure envia o recurso para o Provedor de Recursos. Audit funciona da mesma forma para uma solicitação de recurso e um ciclo de avaliação. A Diretiva Do `Microsoft.Authorization/policies/audit/action` Azure adiciona uma operação ao registro de atividades e marca o recurso como não compatível.

### <a name="audit-properties"></a>Propriedades de auditoria

O efeito audit não têm propriedades adicionais para uso na condição **then** da definição de política.

### <a name="audit-example"></a>Exemplo de auditoria

Exemplo: usando o efeito auditoria.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

O efeito AuditIfNotExists habilita a auditoria em recursos que correspondem à condição **if**, mas não têm os componentes especificados nos **detalhes** da condição **then**.

### <a name="auditifnotexists-evaluation"></a>Avaliação de AuditIfNotExists

O efeito AuditIfNotExists é executado depois de um provedor de recursos ter tratado uma solicitação de criação ou atualização de recurso e ter retornado um código de status de êxito. A auditoria ocorre quando não existem recursos relacionados ou se os recursos definidos por **ExistenceCondition** não são avaliados como verdadeiros. A Azure `Microsoft.Authorization/policies/audit/action` Policy adiciona uma operação ao registro de atividades da mesma forma que o efeito de auditoria. Quando disparado, o recurso que atendeu à condição **se** é o recurso marcado como não compatível.

### <a name="auditifnotexists-properties"></a>Propriedades de AuditIfNotExists

A propriedade **detalhes** dos efeitos AuditIfNotExists tem todas as subpropriedades que definem os recursos relacionados a serem correspondidos.

- **Tipo** [obrigatório]
  - Especifica o tipo do recurso relacionado a ser correspondido.
  - Se **details.type** for um tipo de recurso abaixo do recurso **if** condition, a política consulta recursos desse **tipo** no âmbito do recurso avaliado. Caso contrário, as consultas de diretiva dentro do mesmo grupo de recursos do recurso avaliado.
- **Nome** (opcional)
  - Especifica o nome exato do recurso a ser correspondido e faz com que a política busque um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando a condição valoriza para **if.field.type** e **então.details.type** match, então **Nome** torna-se _necessário_ e deve ser `[field('name')]`. No entanto, um efeito [de auditoria](#audit) deve ser considerado em vez disso.
- **ResourceGroupName** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se **type** for um recurso que estaria sob o recurso de condição **if**.
  - O padrão é o grupo de recursos do recurso de condição **se**.
- **ExistenceScope** (opcional)
  - Os valores permitidos são _Assinatura_ e _ResourceGroup_.
  - Define o escopo de onde buscar o recurso relacionado com o qual corresponder.
  - Não se aplica se **type** for um recurso que estaria sob o recurso de condição **if**.
  - Para _ResourceGroup_, limitaria para o grupo de recursos do recurso da condição **se** ou o grupo de recursos especificado em **ResourceGroupName**.
  - Para _Assinatura_, consulta a assinatura inteira para o recurso relacionado.
  - O padrão é _ResourceGroup_.
- **ExistenceCondition** (opcional)
  - Se não for especificado, todos os recursos relacionados de **type** cumprem o efeito e não acionam a auditoria.
  - Usa a mesma linguagem como a regra de política para a condição **se**, mas é avaliada em relação a cada recurso relacionado individualmente.
  - Se algum recurso relacionado correspondente for avaliado como verdadeiro, o efeito será atendido e não acionará a auditoria.
  - Pode usar [field()] para verificar a equivalência com valores na condição **se**.
  - Por exemplo, pode ser usado para validar que o recurso pai (na condição **se**) está no mesmo local que recursos como o recurso relacionado correspondente.

### <a name="auditifnotexists-example"></a>Exemplo de AuditIfNotExists

Exemplo: avalia máquinas virtuais para determinar se a extensão antimalware existe e faz auditorias quando ausente.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Semelhante ao AuditIfNotExists, uma definição de diretiva DeployIfNot executa uma implantação de modelo quando a condição é atendida.

> [!NOTE]
> [Modelos aninhados](../../../azure-resource-manager/templates/linked-templates.md#nested-template) são compatíveis com **deployIfNotExists**, mas [modelos vinculados](../../../azure-resource-manager/templates/linked-templates.md#linked-template) não são compatíveis atualmente.

### <a name="deployifnotexists-evaluation"></a>Avaliação de DeployIfNotExists

O DeployIfNotexiste é executado cerca de 15 minutos depois que um provedor de recursos lidou com uma solicitação de criação ou atualização de recursos e retornou um código de status de sucesso. Uma implantação de modelo ocorre quando não existem recursos relacionados ou se os recursos definidos por **ExistenceCondition** não são avaliados como verdadeiros.
A duração da implantação depende da complexidade dos recursos incluídos no modelo.

Durante um ciclo de avaliação, as definições de política com um efeito DeployIfNotExists que correspondam a recursos são marcadas como não conformes, mas nenhuma ação é realizada no recurso. Os recursos não compatíveis existentes podem ser remediados com uma [tarefa de remediação](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>Propriedades de DeployIfNotExists

A propriedade **detalhes** do efeito DeployIfNotExists tem todas as subpropriedades que definem os recursos relacionados a corresponder e a implantação do modelo a ser executada.

- **Tipo** [obrigatório]
  - Especifica o tipo do recurso relacionado a ser correspondido.
  - Começa tentando buscar um recurso sob o recurso de condição **se**, depois consulta dentro do mesmo grupo de recursos como o recurso de condição **se**.
- **Nome** (opcional)
  - Especifica o nome exato do recurso a ser correspondido e faz com que a política busque um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando a condição valoriza para **if.field.type** e **então.details.type** match, então **Nome** torna-se _necessário_ e deve ser `[field('name')]`.
- **ResourceGroupName** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se **type** for um recurso que estaria sob o recurso de condição **if**.
  - O padrão é o grupo de recursos do recurso de condição **se**.
  - Se uma implantação de modelo é executada, ele é implantado no grupo de recursos desse valor.
- **ExistenceScope** (opcional)
  - Os valores permitidos são _Assinatura_ e _ResourceGroup_.
  - Define o escopo de onde buscar o recurso relacionado com o qual corresponder.
  - Não se aplica se **type** for um recurso que estaria sob o recurso de condição **if**.
  - Para _ResourceGroup_, limitaria para o grupo de recursos do recurso da condição **se** ou o grupo de recursos especificado em **ResourceGroupName**.
  - Para _Assinatura_, consulta a assinatura inteira para o recurso relacionado.
  - O padrão é _ResourceGroup_.
- **ExistenceCondition** (opcional)
  - Se não for especificado, todos os recursos relacionados de **type** cumprem o efeito e não acionam a implantação.
  - Usa a mesma linguagem como a regra de política para a condição **se**, mas é avaliada em relação a cada recurso relacionado individualmente.
  - Se algum recurso relacionado correspondente for avaliado como verdadeiro, o efeito será atendido e não acionará a implantação.
  - Pode usar [field()] para verificar a equivalência com valores na condição **se**.
  - Por exemplo, pode ser usado para validar que o recurso pai (na condição **se**) está no mesmo local que recursos como o recurso relacionado correspondente.
- **roleDefinitionIds** [obrigatório]
  - Essa propriedade deve incluir uma matriz de cadeias de caracteres que correspondem à ID de controle de acesso baseado em função que pode ser acessada pela assinatura. Para obter mais informações, confira [correção – configurar a definição de política](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (opcional)
  - Os valores permitidos são _Assinatura_ e _ResourceGroup_.
  - Define o tipo de implantação a ser disparada. _Assinatura_ indica uma [implantação no nível de assinatura](../../../azure-resource-manager/templates/deploy-to-subscription.md), _ResourceGroup_ indica uma implantação em um grupo de recursos.
  - Uma propriedade _localização_ deverá ser especificada na _Implantação_ quando usar implantações no nível de assinatura.
  - O padrão é _ResourceGroup_.
- **Implantação** [obrigatória]
  - Essa propriedade deve incluir a implantação de modelo completo que seria passada para o API PUT de `Microsoft.Resources/deployments`. Para obter mais informações, consulte a [API REST de implantações](/rest/api/resources/deployments).

  > [!NOTE]
  > Todas as funções dentro da propriedade **Implantação** são avaliadas como componentes do modelo, não da política. A exceção é a propriedade **parâmetros**, que passa os valores da política para o modelo. O **valor** nesta seção em um nome de parâmetro de modelo é usado para executar essa passagem de valor (consulte _fullDbName_ no exemplo do efeito DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Exemplo de DeployIfNotExists

Exemplo: avalia os bancos de dados do SQL Server para determinar se transparentDataEncryption está habilitado. Se não estiver, uma implantação será executada para habilitá-lo.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="enforceopaconstraint"></a>Restrição de AplicaçãoOPA

Este efeito é usado *mode* com `Microsoft.Kubernetes.Data`um modo de definição de política de . Ele é usado para passar as regras de controle de admissão gatekeeper v3 definidas com [o OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) to Open Policy [Agent](https://www.openpolicyagent.org/) (OPA) para clusters Kubernetes auto-gerenciados no Azure.

> [!NOTE]
> [A Azure Policy for AKS Engine](aks-engine.md) está na Visualização Pública e só suporta definições de diretiva incorporadas.

### <a name="enforceopaconstraint-evaluation"></a>Avaliação de restrição de EnforceOPA

O controlador de admissão do Agente de Política Aberta avalia qualquer nova solicitação no cluster em tempo real.
A cada 5 minutos, uma varredura completa do cluster é concluída e os resultados reportados à Política Do Azure.

### <a name="enforceopaconstraint-properties"></a>Propriedades EnforceOPAConstraint

A propriedade de **detalhes** do efeito EnforceOPAConstraint tem as subpropriedades que descrevem a regra de controle de admissão Gatekeeper v3.

- **modelo de restrição** [necessário]
  - O modelo de restrição CustomResourceDefinition (CRD) que define novas restrições. O modelo define a lógica rego, o esquema de restrição e os parâmetros de restrição que são passados através de **valores** da Política Azure.
- **restrição** [necessária]
  - A implementação do CRD do modelo De restrição. Utiliza parâmetros passados através de **valores** como `{{ .Values.<valuename> }}`. No exemplo abaixo, isso `{{ .Values.cpuLimit }}` `{{ .Values.memoryLimit }}`seria e .
- **valores** [opcional]
  - Define quaisquer parâmetros e valores para passar para a Restrição. Cada valor deve existir no modelo de restrição CRD.

### <a name="enforceopaconstraint-example"></a>Exemplo de restrição de EnforceOPA

Exemplo: Regra de controle de admissão Gatekeeper v3 para definir os limites de recursos de CPU e memória do contêiner no AKS Engine.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>Política de ExecuçãoRego

Este efeito é usado *mode* com `Microsoft.ContainerService.Data`um modo de definição de política de . Ele é usado para passar gatekeeper v2 regras de controle de admissão definidas com [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) para [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) no [Azure Kubernetes Service](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [A diretiva do Azure para AKS](rego-for-aks.md) está na Visualização Limitada e só suporta definições de diretiva incorporadas

### <a name="enforceregopolicy-evaluation"></a>Avaliação da EnforceRegoPolicy

O controlador de admissão do Agente de Política Aberta avalia qualquer nova solicitação no cluster em tempo real.
A cada 5 minutos, uma varredura completa do cluster é concluída e os resultados reportados à Política Do Azure.

### <a name="enforceregopolicy-properties"></a>Propriedades EnforceRePolicy

A propriedade de **detalhes** do efeito EnforceRegoPolicy tem as subpropriedades que descrevem a regra de controle de admissão Gatekeeper v2.

- **policyId** [obrigatório]
  - Um nome único passou como parâmetro para a regra de controle de admissão de Rego.
- **política** [necessária]
  - Especifica o URI da regra de controle de admissão Rego.
- **policyParâmetros** [opcional]
  - Define quaisquer parâmetros e valores para passar para a política de rego.

### <a name="enforceregopolicy-example"></a>Exemplo de EnforceRegoPolicy

Exemplo: Regra de controle de admissão gatekeeper v2 para permitir apenas as imagens de contêiner especificadas no AKS.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="layering-policies"></a>Políticas de camadas

Um recurso pode ser afetado por várias atribuições. Essas atribuições podem estar no mesmo escopo ou em escopos diferentes. Também é provável que cada uma dessas atribuições tenha um efeito diferente definido. A condição e o efeito de cada política são avaliados independentemente. Por exemplo: 

- Política 1
  - Restringe o local do recurso para 'westus'
  - Atribuída à assinatura A
  - Efeito deny
- Política 2
  - Restringe o local do recurso para 'eastus'
  - Atribuída ao grupo de recursos B na assinatura A
  - Efeito audit
  
Essa configuração resultaria no seguinte:

- Os recursos já presentes no grupo de recursos B em “eastus” estão em conformidade em relação à política 2 e fora de conformidade em relação à política 1
- Os recursos presentes no grupo de recursos B e fora de 'eastus' estão fora de conformidade em relação à política 2 e fora de conformidade em relação à política 1 se não estão em 'westus'
- Novos recursos na assinatura A não presentes em “westus” são negados pela política 1
- Novos recursos na assinatura A e no grupo de recursos B em 'westus' são criados e estão fora de conformidade em relação à política 2

Se ambas as políticas 1 e 2 tiveram o efeito negar, a situação será alterada para:

- Os recursos já presentes no grupo de recursos B fora de “eastus” serão marcados como não conformes com a política 2
- Os recursos já presentes no grupo de recursos B fora de “westus” serão marcados como não conformes com a política 1
- Novos recursos na assinatura A não presentes em “westus” são negados pela política 1
- Os novos recursos no grupo de recursos B da assinatura A são negados

Cada atribuição é avaliada individualmente. Assim, não existe chance de um recurso passar por uma brecha nas diferenças de escopo. O resultado de políticas em camadas ou sobreposição de políticas é considerado **cumulativo mais restritivo**. Por exemplo, se as políticas 1 e 2 tivessem efeito deny, um recurso seria bloqueado pelas políticas conflitantes e sobrepostas. Caso ainda precise que o recurso seja criado no escopo de destino, revise as exclusões em cada atribuição para validar que as políticas corretas estão afetando os escopos corretos.

## <a name="next-steps"></a>Próximas etapas

- Revisar exemplos em [amostras de política do Azure](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](definition-structure.md).
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Aprenda a [remediar recursos não compatíveis.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [organize seus recursos com grupos de gerenciamento do Azure.](../../management-groups/overview.md)
