---
title: Detalhes da estrutura de definição de política
description: Descreve como as definições de política são usadas para estabelecer convenções para os recursos do Azure na sua organização.
ms.date: 10/05/2020
ms.topic: conceptual
ms.openlocfilehash: 2db91bd1968f816eb2a9320ee81019aeec5d2449
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873992"
---
# <a name="azure-policy-definition-structure"></a>Estrutura de definição da Política do Azure

O Azure Policy estabelece convenções para recursos. As definições de política descrevem as [condições](#conditions) de conformidade de recursos e o efeito a ser realizado se uma condição for atendida. Uma condição compara um [field](#fields) de propriedade de recurso com um valor obrigatório. Os campos de propriedade de recurso são acessados por meio de [aliases](#aliases). Um campo de propriedade de recurso é um campo de valor único ou uma [matriz](#understanding-the--alias) de vários valores. A avaliação de condição é diferente nas matrizes.
Saiba mais sobre as [condições](#conditions).

Definindo as convenções, você pode controlar os custos e muito mais fácil gerenciar seus recursos. Por exemplo, você pode especificar que somente determinados tipos de máquinas virtuais são permitidos. Ou, você pode exigir que os recursos tenham uma marca específica. As atribuições de política são herdadas por recursos filho. Se uma atribuição de política for aplicada a um grupo de recursos, ela será aplicável a todos os recursos nesse grupo de recursos.

O esquema _policyRule_ de definição de política é encontrado aqui: [https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json)

Você usa JSON para criar uma definição de política. A definição de política contém elementos para:

- nome de exibição
- descrição
- mode
- metadata
- parameters
- regra de política
  - avaliação de lógica
  - efeito

Por exemplo, o JSON a seguir mostra uma política que limita os locais em que os recursos são implantados:

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Azure Policys e padrões estão em exemplos de [Azure Policy](../samples/index.md).

## <a name="display-name-and-description"></a>Nome de exibição e descrição

Use **displayName** e **description** para identificar a definição de política e fornecer contexto de quando ela é usada. **displayName** tem um comprimento máximo de _128_ caracteres e **description** tem um comprimento máximo de _512_ caracteres.

> [!NOTE]
> Durante a criação ou a atualização de uma definição de política, **id**, **type** e **name** são definidos por propriedades externas ao JSON e não são necessários no arquivo JSON. O fetch da definição de política por meio do SDK retorna a as propriedades **id**, **type** e **name** como parte do JSON, mas cada uma delas é uma informação somente leitura relacionada à definição de política.

## <a name="type"></a>Type

Embora a propriedade **Type** não possa ser definida, há três valores que são retornados pelo SDK e visíveis no Portal:

- `Builtin`: Essas definições de política são fornecidas e mantidas pela Microsoft.
- `Custom`: Todas as definições de política criadas por clientes têm esse valor.
- `Static`: Indica uma definição de política de [conformidade regulatória](./regulatory-compliance.md) com a **Propriedade**da Microsoft. Os resultados de conformidade para essas definições de política são os resultados de auditorias de terceiros na infraestrutura da Microsoft. No portal do Azure, esse valor às vezes é exibido como **gerenciado pela Microsoft**. Para obter mais informações, consulte [responsabilidade compartilhada na nuvem](../../../security/fundamentals/shared-responsibility.md).

## <a name="mode"></a>Mode

O **modo** é configurado dependendo do fato de a política ser direcionada a uma propriedade do Azure Resource Manager ou do provedor de recursos.

### <a name="resource-manager-modes"></a>Modos do Resource Manager

O **modo** determina quais tipos de recursos são avaliados para uma definição de política. Os modos suportados são:

- `all`: avalia grupos de recursos, assinaturas e todos os tipos de recursos
- `indexed`: avaliar apenas os tipos de recursos que oferecem suporte a marcas e local

Por exemplo, o recurso `Microsoft.Network/routeTables` dá suporte às marcas e à localização e é avaliado em ambos os modos. No entanto, o recurso `Microsoft.Network/routeTables/routes` não pode ser marcado e não é avaliado no modo `Indexed`.

É recomendável definir o **modo** como `all` na maioria dos casos. Todas as definições de políticas criadas através do portal usam o modo `all`. Se você usar a CLI do Azure ou PowerShell, será necessário especificar o modo **parâmetro** manualmente. Se a definição de política não incluir um valor **modo**, ela usará como padrão `all` no Azure PowerShell e `null` na CLI do Azure. Um modo `null` é o mesmo que usar `indexed` para dar suporte à compatibilidade com versões anteriores.

`indexed` deve ser usado ao criar políticas que vão impor marcas ou locais. Embora não seja obrigatório, impedirá que recursos que não oferecem suporte a marcas nem locais apareçam como não compatíveis nos resultados de conformidade. Os **grupos de recursos** e as **assinaturas** são exceções a essa regra. As definições de política que impõem localização ou marcas em um grupo de recursos ou uma assinatura devem definir o **modo** como `all` e, especificamente, direcionar o tipo `Microsoft.Resources/subscriptions/resourceGroups` ou `Microsoft.Resources/subscriptions`. Para obter um exemplo, confira [Padrão: Marcas – Amostra nº 1](../samples/pattern-tags.md). Para obter uma lista de recursos que dão suporte a marcas, confira [Suporte de marcas para recursos do Azure](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes"></a>Modos de provedor de recursos

O seguinte modo de provedor de recursos tem suporte completo:

- `Microsoft.Kubernetes.Data` para gerenciar seus clusters Kubernetes no Azure ou fora dele. As definições que usam esse modo de provedor de recursos usam efeitos de _auditoria_, _negação_e _desabilitação_. O uso do efeito [EnforceOPAConstraint](./effects.md#enforceopaconstraint) foi _preterido_.

Atualmente, há suporte para os seguintes modos de provedor de recursos como uma **Visualização**:

- `Microsoft.ContainerService.Data` para gerenciar as regras do controlador de admissão no [Serviço de Kubernetes do Azure](../../../aks/intro-kubernetes.md). As definições que usam esse modo de provedor de recursos **devem** usar o efeito [EnforceRegoPolicy](./effects.md#enforceregopolicy) . Esse modo é _preterido_.
- `Microsoft.KeyVault.Data` para gerenciar cofres e certificados no [Azure Key Vault](../../../key-vault/general/overview.md).

> [!NOTE]
> Os modos de provedor de recursos só dão suporte a definições de políticas internas.

## <a name="metadata"></a>Metadados

A `metadata` propriedade opcional armazena informações sobre a definição de política. Os clientes podem definir quaisquer propriedades e valores úteis para sua organização no `metadata` . No entanto, há algumas propriedades _comuns_ usadas por Azure Policy e em interno.

### <a name="common-metadata-properties"></a>Propriedades de metadados comuns

- `version` (cadeia de caracteres): rastreia detalhes sobre a versão do conteúdo de uma definição de política.
- `category` (String): determina sob qual categoria em portal do Azure a definição de política é exibida.
- `preview` (booliano): sinalizador true ou false para se a definição de política for _Preview_.
- `deprecated` (booliano): sinalizador true ou false para se a definição de política tiver sido marcada como _preterida_.

> [!NOTE]
> O serviço Azure Policy usa as propriedades `version`, `preview` e `deprecated` para transmitir o nível de alteração para uma definição de política interna ou uma iniciativa e um estado. O formato de `version` é: `{Major}.{Minor}.{Patch}`. Estados específicos, como _preterido_ ou _versão prévia_, são acrescentados à propriedade `version` ou a outra propriedade como um **booliano**. Para obter mais informações sobre como Azure Policy versões internas, consulte [controle de versão interno](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Parâmetros

Parâmetros ajudam a simplificar o gerenciamento de política, reduzindo o número de definições de política. Pense em parâmetros como os campos em um formulário – `name`, `address`, `city`, `state`. Esses parâmetros sempre permanecem iguais, porém, seus valores mudam com base no preenchimento individual do formulário.
Os parâmetros funcionam da mesma maneira que ao criar políticas. Ao incluir parâmetros em uma definição de política, você pode reutilizar essa política para diferentes cenários usando valores diferentes.

> [!NOTE]
> Os parâmetros podem ser adicionados a uma definição existente e atribuída. O novo parâmetro deve incluir a propriedade **defaultValue**. Isso impede que atribuições existentes da política ou da iniciativa sejam tornadas inválidas indiretamente.

### <a name="parameter-properties"></a>Propriedades do parâmetro

Um parâmetro tem as seguintes propriedades que são usadas na definição de política:

- `name`: O nome do parâmetro. Usado pela função de implantação `parameters` dentro da regra de política. Para saber mais, confira [Usar o valor de parâmetro](#using-a-parameter-value).
- `type`: determina se o parâmetro é uma **cadeia de caracteres**, uma **matriz**, um **objeto**, um **booliano**, um **inteiro**, um **float** ou um **datetime**.
- `metadata`: define as subpropriedades usadas principalmente pelo portal do Azure para exibição de informações simples:
  - `description`: a explicação de uso do parâmetro. Pode ser usado para fornecer exemplos de valores aceitáveis.
  - `displayName`: O nome amigável exibido no portal para o parâmetro.
  - `strongType`: (opcional) usado ao atribuir a definição de política por meio do portal. Fornece uma lista de reconhecimento de contexto. Para obter mais informações, confira [strongType](#strongtype).
  - `assignPermissions`: (opcional) defina essa opção como _true_ para que o portal do Azure crie atribuições de função durante a atribuição de política. Essa propriedade é útil caso você queira atribuir permissões fora do escopo de atribuição. Há uma atribuição de função por definição de função na política (ou por definição de função em todas as políticas na iniciativa). O valor do parâmetro precisa ser um recurso ou um escopo válido.
- `defaultValue`: (opcional) define o valor do parâmetro em uma atribuição se não houver valor fornecido.
  Necessário ao atualizar uma definição de política existente que é atribuída.
- `allowedValues`: (opcional) fornece uma matriz de valores que o parâmetro aceita durante a atribuição.

Por exemplo, você pode definir uma definição de política para limitar os locais em que os recursos podem ser implantados. Um parâmetro para essa definição de política pode ser **allowedLocations**. Esse parâmetro deve ser usado por cada atribuição da definição de política para limitar os valores aceitos. O uso de **strongType** fornece uma experiência aprimorada ao concluir a atribuição por meio do portal:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Usando um valor de parâmetro

Na regra de política, você referencia os parâmetros com a seguinte sintaxe de função `parameters`:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Este exemplo faz referência ao parâmetro **allowedLocations** que foi demonstrado nas [propriedades do parâmetro](#parameter-properties).

### <a name="strongtype"></a>strongType

Na propriedade `metadata`, você pode usar **strongType** para fornecer uma lista de opções de seleção múltipla no portal do Azure. **strongType** pode ser um _tipo de recurso_ compatível ou um valor permitido. Para determinar se um _tipo de recurso_ é válido para **strongType**, use [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider). O formato de um _resource type_ **strongtype** de tipo de recurso é `<Resource Provider>/<Resource Type>` . Por exemplo, `Microsoft.Network/virtualNetworks/subnets`.

Há suporte para alguns _tipos de recursos_ não retornados pelo **Get-AzResourceProvider**. Esses tipos são:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Os valores permitidos para não _tipos de recursos_ para **strongType** são:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Local da definição

Durante a criação de uma iniciativa ou política, é necessário especificar o local da definição. O local da definição deve ser um grupo de gerenciamento ou uma assinatura. Esse local determina o escopo para o qual pode ser atribuída a iniciativa ou política. Os recursos devem ser membros diretos ou filhos da hierarquia do local da definição para direcionar para a atribuição.

Se o local da definição for:

- Os recursos somente de **assinatura** dentro dessa assinatura podem ser atribuídos à definição de política.
- Recursos somente de **grupo de gerenciamento** dentro de grupos de gerenciamento filho e assinaturas filho podem ser atribuídos à definição de política. Se você planeja aplicar a definição de política a várias assinaturas, o local deve ser um grupo de gerenciamento que contém cada assinatura.

Para obter mais informações, consulte [entender o escopo em Azure Policy](./scope.md#definition-location).

## <a name="policy-rule"></a>Regra de política

A regra de política consiste em **se** e **, em seguida,** blocos. No **se** bloco, você define uma ou mais condições que especificam quando a política é aplicada. Você pode aplicar os operadores lógicos para essas condições para definir exatamente o cenário para uma política.

No **, em seguida,** bloco, você define o efeito que acontecerá quando o **se** condições sejam atendidas.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | modify | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Operadores lógicos

Os operadores lógicos compatíveis são:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

O **não** sintaxe inverte o resultado da condição. A sintaxe de **allOf** (semelhante à operação **E** lógica) requer que todas as condições sejam verdadeiras. A sintaxe de **anyOf** (semelhante à operação **Ou** lógica) requer que uma ou mais condições sejam verdadeiras.

Você pode aninhar operadores lógicos. A exemplo a seguir mostra uma operação **not** operação é aninhada dentro de uma operação **allOf**.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Condições

Uma condição avalia se um **campo** ou um acessador de **valor** atende a determinados critérios. As condições com suporte são:

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

Para **less**, **lessOrEquals**, **greater** e **greaterOrEquals**, se o tipo de propriedade não corresponder ao tipo de condição, um erro será gerado. As comparações de cadeias de caracteres são feitas por meio de `InvariantCultureIgnoreCase`.

Ao usar as condições **like** e **notLike**, você fornece um curinga (`*`) no valor.
O valor não deve ter mais de um curinga `*`.

Ao usar as condições **match** e **notMatch**, forneça `#` para fazer a correspondência de um dígito, `?` para uma letra, `.` para fazer a correspondência de todos os caracteres e qualquer outro caractere para fazer a correspondência com o caractere real. Enquanto **Match** e não **Match** diferenciam maiúsculas de minúsculas, todas as outras condições que avaliam uma _cadeia de caracteres_ não diferenciam maiúsculas de minúsculas. Estão disponíveis alternativas que diferenciam maiúsculas de minúsculas em **matchInsensitively** e **notMatchInsensitively**.

Em um valor do campo matriz de **alias \[\*\]** , cada elemento da matriz é avaliado individualmente com um **and** lógico entre os elementos. Para obter mais informações, confira [Como avaliar o alias \[\*\]](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

### <a name="fields"></a>Campos

As condições são formadas usando campos. Um campo combina às propriedades no conteúdo da solicitação de recurso e descreve o estado do recurso.

Há suporte para os seguintes campos:

- `name`
- `fullName`
  - Retorna o nome completo do recurso. O nome completo de um recurso é o nome do recurso precedido dos nomes dos recursos pai (por exemplo, "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Use **global** para recursos independentes de local.
- `identity.type`
  - Retorna o tipo de [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md) habilitada no recurso.
- `tags`
- `tags['<tagName>']`
  - Essa sintaxe de colchete dá suporte a nomes de marca que tem pontuação, como hífen, ponto ou espaço.
  - Em que **\<tagName\>** é o nome da marca para validar a condição.
  - Exemplos: `tags['Acct.CostCenter']` em que **Acct.CostCenter** é o nome da marca.
- `tags['''<tagName>''']`
  - Essa sintaxe de colchete dá suporte a nomes de marca contendo apóstrofos, evitando os apóstrofos duplos.
  - Em que **' \<tagName\> '** é o nome da marca para validar a condição.
  - Exemplo: `tags['''My.Apostrophe.Tag''']`, em que **'My.Apostrophe.Tag'** é o nome da marca.
- aliases de propriedade - para obter uma lista, confira [Aliases](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`, e `tags[tag.with.dots]` ainda são maneiras aceitáveis de declarar um campo de marcas. No entanto, as expressões preferenciais são aquelas listadas acima.

#### <a name="use-tags-with-parameters"></a>Usar marcas com parâmetros

Um valor de parâmetro pode ser passado para um campo de marca. Passando um parâmetro para um campo de marca aumenta a flexibilidade da definição de política durante a atribuição de política.

No exemplo a seguir, `concat` é usado para criar uma pesquisa de campo de marcas para a marca que nomeou o valor do parâmetro **tagName**. Se essa marca não existir, o efeito **modify** será usado para adicionar a marca usando o valor da mesma marca nomeada definida no grupo de recursos pai dos recursos auditados por meio da função de pesquisa `resourcegroup()`.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>Valor

As condições também podem ser formadas usando o **valor**. O **valor** verifica as condições em relação aos [parâmetros](#parameters), [funções de modelo com suporte](#policy-functions) ou literais. O **valor** é emparelhado a uma [condição](#conditions) com suporte.

> [!WARNING]
> Se o resultado de uma _função de modelo_ for um erro, a avaliação da política falhará. Uma avaliação com falha é um **deny** implícito. Para mais informações, confira [Evitar falhas de modelo](#avoiding-template-failures). Use [enforcementMode](./assignment-structure.md#enforcement-mode) de **DoNotEnforce** para evitar o impacto de uma avaliação com falha em recursos novos ou atualizados durante o teste e a validação de uma nova definição de política.

#### <a name="value-examples"></a>Exemplos de valor

Este exemplo de regra de política usa **valor** para comparar o resultado da função `resourceGroup()` e a propriedade **nome** retornada para uma condição **like** de `*netrg`. A regra nega qualquer recurso que não for do **tipo** `Microsoft.Network/*` em qualquer grupo de recursos cujo nome termine com `*netrg`.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Este exemplo de regra de política usa o **valor** para verificar se o resultado de várias funções aninhadas é **igual** a `true`. A regra nega qualquer recurso que não tenha pelo menos três marcas.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Como evitar falhas de modelo

O uso de _funções de modelo_ no **valor** permite muitas funções aninhadas complexas. Se o resultado de uma _função de modelo_ for um erro, a avaliação da política falhará. Uma avaliação com falha é um **deny** implícito. Um exemplo de um **valor** que falha em alguns cenários:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

A regra de política de exemplo acima usa [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring) para comparar os três primeiros caracteres de **nome** com **abc**. Se o **nome** for menor que três caracteres, a função `substring()` resultará em um erro. Esse erro faz com que a política se torne um efeito **deny**.

Em vez disso, use a função [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) para verificar se os três primeiros caracteres do **nome** são iguais a **abc** sem permitir um **nome** menor que três caracteres cause um erro:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Com a regra de política revisada, `if()` verifica o tamanho do **nome** antes de tentar obter uma `substring()` em um valor com menos de três caracteres. Se o **nome** for muito curto, o valor "não começa com abc" será retornado e comparado com **abc**. Um recurso com um nome curto que não começa com **abc** ainda falha na regra de política, mas deixa de causar erros durante a avaliação.

### <a name="count"></a>Contagem

As condições que contam quantos membros de uma matriz no conteúdo do recurso atendem a uma expressão de condição podem ser formadas por meio de uma expressão **count**. Os cenários comuns são verificar se 'pelo menos um dos', 'exatamente um dos', 'todos os' ou 'nenhum dos' membros da matriz atendem à condição. **count** avalia cada membro da matriz de [alias \[\*\]](#understanding-the--alias) para uma expressão de condição e soma os resultados _true_, que é comparado ao operador de expressão. As expressões de **contagem** podem ser adicionadas até três vezes a uma única definição de **policyRule** .

A estrutura da expressão **count** é:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

As seguintes propriedades são usadas com **count**:

- **count.field** (obrigatório): contém o caminho para a matriz e precisa ser um alias de matriz. Se a matriz estiver ausente, a expressão será avaliada como _false_ sem considerar a expressão de condição.
- **count.where** (opcional): a expressão de condição usada para avaliar individualmente cada membro da matriz de [alias \[\*\]](#understanding-the--alias) de **count.field**. Se essa propriedade não for fornecida, todos os membros da matriz com o caminho de ' Field ' serão avaliados como _true_. Qualquer [condição](../concepts/definition-structure.md#conditions) pode ser usada nessa propriedade.
  Os [operadores lógicos](#logical-operators) podem ser usados nessa propriedade para criar requisitos complexos de avaliação.
- **\<condition\>** (obrigatório): o valor é comparado com o número de itens que atendem à **contagem.** expressão de condição WHERE. Uma [condição](../concepts/definition-structure.md#conditions) numérica deve ser usada.

#### <a name="count-examples"></a>Exemplos de contagem

Exemplo 1: verificar se uma matriz está vazia

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Exemplo 2: verificar se apenas um membro da matriz atende à expressão de condição

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Exemplo 3: verificar se, pelo menos, um membro da matriz atende à expressão de condição

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Exemplo 4: verificar se todos os membros da matriz de objetos atendem à expressão de condição

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Exemplo 5: Verifique se pelo menos um membro da matriz corresponde a várias propriedades na expressão de condição

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Efeito

O Azure Policy dá suporte aos seguintes tipos de efeitos:

- **Acrescentar**: adiciona o conjunto de campos definido à solicitação
- **Auditoria**: gera um evento de aviso no log de atividades, mas não falha na solicitação
- **AuditIfNotExists**: gera um evento de aviso no log de atividades se um recurso relacionado não existe
- **Negar**: gera um evento no log de atividades e falha na solicitação
- **DeployIfNotExists**: implanta um recurso relacionado caso ele ainda não exista
- **Desabilitado**: não avalia os recursos de conformidade para a regra de política
- **Modify**: adiciona, atualiza ou remove as marcas definidas de um recurso
- **EnforceOPAConstraint** (preterido): configura o controlador de admissão do agente de política aberto com o gatekeeper V3 para clusters kubernetes autogerenciados no Azure
- **EnforceRegoPolicy** (preterido): configura o controlador de admissão do agente de política aberto com o gatekeeper V2 no serviço kubernetes do Azure

Para obter detalhes completos sobre cada efeito, a ordem de avaliação, as propriedades, bem como exemplos, confira [Noções básicas dos efeitos do Azure Policy](effects.md).

### <a name="policy-functions"></a>Funções de política

Todas as [funções de modelo do Resource Manager](../../../azure-resource-manager/templates/template-functions.md) estão disponíveis para uso em uma regra de política, exceto as seguintes funções e funções definidas pelo usuário:

- copyIndex()
- deployment()
- lista*
- newGuid()
- pickZones()
- providers()
- reference()
- resourceId()
- variables()

> [!NOTE]
> Essas funções ainda estão disponíveis na parte `details.deployment.properties.template` da implantação de modelo em uma definição de política **deployIfNotExists**.

A função a seguir está disponível para uso em uma regra de política, mas difere do uso em um modelo de Azure Resource Manager (modelo ARM):

- `utcNow()` -Ao contrário de um modelo de ARM, essa propriedade pode ser usada fora do _DefaultValue_.
  - Retorna uma cadeia de caracteres que é definida como a data e a hora atuais no formato universal ISO 8601 DateTime `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

As seguintes funções estão disponíveis apenas em regras de política:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **DateTime**: [Required] String-String no formato de data/hora Universal ISO 8601 `yyyy-MM-ddTHH:mm:ss.fffffffZ` .
  - **numberOfDaysToAdd**: [obrigatório] número inteiro de dias para adicionar.
- `field(fieldName)`
  - **fieldName**: [obrigatório] cadeia de caracteres; nome do [field](#fields) a ser recuperado
  - Retorna o valor desse campo do recurso que está sendo avaliado pela condição if.
  - `field` é principalmente para uso com **AuditIfNotExists** e **DeployIfNotExists** para referenciar campos no recurso que estão sendo avaliados. Um exemplo desse uso pode ser visto no [exemplo DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Retorna a versão da API da solicitação que disparou a avaliação da política (exemplo: `2019-09-01`).
    Esse valor é a versão da API que foi usada na solicitação PUT/PATCH para avaliações na criação/atualização de recursos. A última versão da API é sempre usada durante a avaliação de conformidade nos recursos existentes.
- `policy()`
  - Retorna as informações a seguir sobre a política que está sendo avaliada. As propriedades podem ser acessadas do objeto retornado (exemplo: `[policy().assignmentId]` ).
  
  ```json
  {
    "assignmentId": "/subscriptions/ad404ddd-36a5-4ea8-b3e3-681e77487a63/providers/Microsoft.Authorization/policyAssignments/myAssignment",
    "definitionId": "/providers/Microsoft.Authorization/policyDefinitions/34c877ad-507e-4c82-993e-3452a6e0ad3c",
    "setDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/42a694ed-f65e-42b2-aa9e-8052e9740a92",
    "definitionReferenceId": "StorageAccountNetworkACLs"
  }
  ```
  
  
#### <a name="policy-function-example"></a>Exemplo de função de política

Este exemplo de regra de política usa a função de recurso `resourceGroup` para obter a propriedade **name**, combinada com a matriz `concat` e o objeto de função para criar uma condição `like` que impõe o nome do recurso para iniciar com o nome do grupo de recursos.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Aliases

Você pode usar aliases de propriedade para acessar propriedades específicas para um tipo de recurso. Os aliases permitem restringir quais valores ou condições são permitidas para uma propriedade em um recurso. Cada alias é mapeado para caminhos em diferentes versões de API para um tipo de recurso específico. Durante a avaliação de política, o mecanismo de políticas obtém o caminho de propriedade para essa versão de API.

A lista de aliases sempre está aumentando. Para descobrir quais aliases atualmente são compatíveis com o Azure Policy, use um dos seguintes métodos:

- Extensão do Azure Policy para Visual Studio Code (recomendado)

  Use a [extensão do Azure Policy para Visual Studio Code](../how-to/extension-for-vscode.md) para ver e descobrir aliases das propriedades de recurso.

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Captura de tela da extensão de Azure Policy para Visual Studio Code passando uma propriedade para exibir os nomes de alias." border="false":::

- Gráfico de Recursos do Azure

  Use o operador `project` para exibir o **alias** de um recurso.

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

  > [!NOTE]
  > Para localizar aliases que possam ser usados com o efeito [Modificar](./effects.md#modify) , use o seguinte comando no Azure PowerShell **4.6.0** ou superior:
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

- CLI do Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- API REST / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Noções básicas sobre o alias [*]

Vários aliases disponíveis têm uma versão que é exibida como um nome 'normal' e outra com **\[\*\]** anexado. Por exemplo:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

o alias 'normal' representa o campo como um só valor. Esse campo destina-se a cenários de comparação de correspondência exata quando o conjunto inteiro de valores precisa ser exatamente o mesmo definido, nem mais nem menos.

O alias **\[\*\]** possibilita a comparação com o valor de cada elemento na matriz e nas propriedades específicas de cada elemento. Essa abordagem possibilita a comparação das propriedades do elemento em relação aos cenários 'se nenhum dos', 'se algum dos' ou 'se todos os'. Para cenários mais complexos, use a expressão de condição [count](#count). Com **ipRules\[\*\]** , um exemplo será validar se cada _action_ é _Deny_; porém, não se preocupe com a quantidade de regras existentes ou em saber qual é o _value_ de IP.
Esta regra de exemplo verifica se há correspondências de **ipRules\[\*\].value** e **10.0.4.1** e aplica o **effectType** somente se não encontra, pelo menos, uma correspondência:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Para obter mais informações, confira [Como avaliar o alias [\*]](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="next-steps"></a>Próximas etapas

- Consulte a [estrutura de definição de iniciativa](./initiative-definition-structure.md)
- Examine os exemplos em [Amostras do Azure Policy](../samples/index.md).
- Revisar [Compreendendo os efeitos da política](effects.md).
- Entenda como [criar políticas de forma programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
