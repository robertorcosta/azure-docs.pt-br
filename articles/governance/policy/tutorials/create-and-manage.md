---
title: 'Tutorial: criar políticas para impor a conformidade'
description: Neste tutorial, você usa políticas para impor padrões, controlar custos, manter a segurança e impor princípios de design empresariais.
ms.date: 12/20/2019
ms.topic: tutorial
ms.openlocfilehash: a4e4190e5ff6a87098c349cde99572df2dba4331
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384553"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Tutorial: criar e gerenciar políticas para impor a conformidade

Compreender como criar e gerenciar políticas no Azure é importante para manter a conformidade com padrões corporativos e com contratos de nível de serviço. Neste tutorial, você aprenderá a usar o Azure Policy para realizar algumas das tarefas mais comuns relacionadas à criação, à atribuição e ao gerenciamento de políticas em sua organização, como:

> [!div class="checklist"]
> - Atribuir uma política para impor uma condição para os recursos que você criar no futuro
> - Criar e atribuir uma definição de iniciativa para acompanhar a conformidade de vários recursos
> - Resolver um recurso negado ou sem conformidade
> - Implementar uma nova política em toda a organização

Se você deseja atribuir uma política para identificar o estado atual de conformidade dos recursos existentes, os artigos de início rápido falam sobre como fazer isso.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="assign-a-policy"></a>Atribuir uma política

A primeira etapa para impor a conformidade com a Política do Azure é atribuir uma definição de política. Uma definição de política define sob quais condições a política será aplicada e qual efeito será tomado Neste exemplo, atribua uma definição de política interna chamada *Exigir SQL Server versão 12.0* para impor a condição de que todos os bancos de dados do SQL Server devem ser a v12.0 para estar em conformidade.

1. Vá para o portal do Azure para atribuir políticas. Pesquise por **Política** e selecione essa opção.

   ![Pesquise por Política na barra de pesquisa](../media/create-and-manage/search-policy.png)

1. Selecione **Atribuições** no lado esquerdo da página de Política do Azure. Uma atribuição é uma política que foi atribuída para entrar em vigor em um escopo específico.

   ![Selecionar Atribuições na página Visão geral da política](../media/create-and-manage/select-assignments.png)

1. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   ![Atribuir uma definição de política na página Atribuições](../media/create-and-manage/select-assign-policy.png)

1. Na página **Atribuir Política** e guia **Básico**, selecione o **Escopo** selecionando as reticências e uma assinatura ou um grupo de gerenciamento. Opcionalmente, selecione um grupo de recursos. Um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta.
   Em seguida, selecione a opção **Selecionar** na parte inferior da página **Escopo**.

   Este exemplo usa a assinatura da **Contoso**. Sua assinatura será diferente.

1. Recursos que podem ser excluídos com base no **Escopo**. **Exclusões** começam um nível abaixo do **Escopo**. **Exclusões** são opcionais, então deixe-as em branco por enquanto.

1. Selecione a reticências **Definição de política** para abrir a lista de definições. Você pode filtrar a definição da política **Tipo** para *Interna* para exibir todas as políticas e ler suas descrições.

1. Selecione **Adicionar ou substituir uma tag nos recursos**. Se você não conseguir localizá-la imediatamente, digite **add or replace** na caixa de pesquisa, pressione ENTER e selecione um ponto fora da caixa de pesquisa. Selecione a opção **Selecionar** na parte inferior da página **Definições Disponíveis** depois de ter encontrado e selecionado a definição de política.

   ![Usar o filtro de pesquisa para localizar uma política](../media/create-and-manage/select-available-definition.png)

1. O **Nome da atribuição** é automaticamente preenchido com o nome da política selecionada, mas você pode alterá-lo. Para este exemplo, deixe *Adicionar ou substituir uma tag em recursos*. Você também pode adicionar uma **Descrição**opcional. A descrição fornece detalhes sobre essa atribuição de política.

1. Deixe **Imposição de política** como _Habilitado_. Quando _Desabilitado_, essa configuração permite testar o resultado da política sem disparar o efeito. Para mais informações, confira [modo de imposição](../concepts/assignment-structure.md#enforcement-mode).

1. **Atribuído por** é preenchido automaticamente com base em quem está conectado. Esse campo é opcional, portanto, valores personalizados podem ser inseridos.

1. Selecione a guia **Parâmetros** na parte superior do assistente.

1. Para **Nome da Tag**, insira _Ambiente_ e, para **Valor da Tag**, insira _Dev_.

1. Selecione a guia **Remediação** na parte superior do assistente.

1. Deixe **Criar uma tarefa de correção** desmarcado. Essa caixa permite que você crie uma tarefa para alterar recursos existentes além de recursos novos ou atualizados. Para saber mais, confira [remediar recursos](../how-to/remediate-resources.md).

1. **Criar uma Identidade Gerenciada** é marcado automaticamente, pois essa definição de política usa o efeito [modificar](../concepts/effects.md#modify). **Permissões** é definido como _Colaborador_ automaticamente com base na definição de política. Para obter mais informações, confira [identidades gerenciadas](../../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de correção](../how-to/remediate-resources.md#how-remediation-security-works).

1. Selecione a guia **Examinar + criar** na parte superior do assistente.

1. Examine suas seleções e, em seguida, selecione **Criar** na parte inferior da página.

## <a name="implement-a-new-custom-policy"></a>Implementar uma nova política personalizada

Agora que você atribuiu uma definição de política interna, você pode fazer muito mais com o Azure Policy. Em seguida, crie uma nova política personalizada para economizar custos, validando que as VMs criadas em seu ambiente não podem estar na série G. Dessa forma, sempre que um usuário na sua organização tentar criar uma VM na série G, a solicitação é negada.

1. Selecione **Definições** em **Criação** no lado esquerdo da página Azure Policy.

   ![Página de definição em Grupo de criação](../media/create-and-manage/definition-under-authoring.png)

1. Selecione **+ Definição de política** na parte superior da página. Isso abre a página **Definição de política**.

1. Insira as seguintes informações:

   - O grupo de gerenciamento ou a assinatura em que a definição de política é salva. Selecione usando o botão de reticências no **local da definição**.

     > [!NOTE]
     > Se você planeja aplicar esta definição de política a várias assinaturas, o local deve ser um grupo de gerenciamento que contém as assinaturas para as quais você atribuirá a política. O mesmo é verdadeiro para uma definição de iniciativa.

   - O nome da definição de política – _*_Exigir SKUs de VM menores que a série G_
   - A descrição do que a definição de política se destina a fazer – _Esta definição de política impõe que todas as VMs criadas nesse escopo tenham SKUs menores que a série G para reduzir o custo._
   - Escolha entre as opções existentes (como _Computação_) ou crie uma nova categoria para esta definição de política.
   - Copie o seguinte código JSON e, em seguida, atualize-o de acordo com suas necessidades com:
      - Os parâmetros da política.
      - As regras/condições da política, nesse caso – tamanho do SKU de VM igual a série G
      - O efeito da política, neste caso – **Negar**.

   Esta deve ser a aparência do JSON. Cole o código revisado no Portal do Azure.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   A propriedade _field_ na regra de política deve ser um valor compatível. Uma lista completa de valores é encontrada em [campos de estrutura de definição de política](../concepts/definition-structure.md#fields). Um exemplo de um alias pode ser `"Microsoft.Compute/VirtualMachines/Size"`.

   Para exibir mais exemplos de política do Azure, confira [Exemplos do Azure Policy](../samples/index.md).

1. Selecione **Salvar**.

## <a name="create-a-policy-definition-with-rest-api"></a>Criar uma definição de política com a API REST

É possível criar uma política com a API REST para definições do Azure Policy. A API REST permite que você crie e exclua as definições de políticas e obtenha informações sobre as definições existentes. Para criar uma definição de política, use o exemplo a seguir:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Inclua um corpo de solicitação semelhante ao exemplo a seguir:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
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

## <a name="create-a-policy-definition-with-powershell"></a>Criar uma definição de política com o PowerShell

Antes de continuar com o exemplo do PowerShell, verifique se você instalou a versão mais recente do módulo Az do Azure PowerShell.

Você pode criar uma definição de política usando o cmdlet `New-AzPolicyDefinition`.

Para criar uma definição de política de um arquivo, passe o caminho para o arquivo. Para um arquivo externo, use o exemplo a seguir:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Para um arquivo local, use o exemplo a seguir:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Para criar uma definição de política com uma regra embutida, use o exemplo a seguir:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

A saída é armazenada em um objeto `$definition`, que é usado durante a atribuição da política. O exemplo a seguir cria uma definição de política que inclui parâmetros:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Exibir definições de política com o PowerShell

Para visualizar todas as definições de política em sua assinatura, utilize o seguinte comando:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Ele retorna todas as definições de política disponíveis, incluindo políticas internas. Cada política é retornada no seguinte formato:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Criar uma definição de política com a CLI do Azure

Você pode criar uma definição de política usando CLI do Azure com o comando `az policy definition`. Para criar uma definição de política com uma regra embutida, use o exemplo a seguir:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Exibir definições de política com a CLI do Azure

Para visualizar todas as definições de política em sua assinatura, utilize o seguinte comando:

```azurecli-interactive
az policy definition list
```

Ele retorna todas as definições de política disponíveis, incluindo políticas internas. Cada política é retornada no seguinte formato:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Criar e atribuir uma definição de iniciativa

Com uma definição de iniciativa, você pode agrupar várias definições de política para alcançar uma meta geral. Uma iniciativa avalia os recursos dentro do escopo da atribuição para conformidade com as políticas incluídas. Para obter mais informações sobre definições de iniciativas, consulte [Visão Geral da Política do Azure](../overview.md).

### <a name="create-an-initiative-definition"></a>Criar uma definição de iniciativa

1. Selecione **Definições** em **Criação** no lado esquerdo da página Azure Policy.

   ![Selecionar definição na página Definições](../media/create-and-manage/definition-under-authoring.png)

1. Selecione **+Definição de Iniciativa** na parte superior da página, esta opção leva você para a página **Definição de Iniciativa**.

   ![Analisar página de definição da iniciativa](../media/create-and-manage/initiative-definition.png)

1. Use as reticências do **Local da definição** para selecionar um grupo de gerenciamento ou uma assinatura para armazenar a definição. Se a página anterior fosse o escopo para um único grupo de gerenciamento ou assinatura, o **Local da definição** seria populado automaticamente. Depois de selecionado, **Definições Disponíveis** são preenchidas.

1. Insira o  **Nome** e a **Descrição** da iniciativa.

   Este exemplo valida os recursos estão em conformidade com as definições de política sobre como ficar seguro. Atribua à iniciativa o nome de **Manter Segurança** e defina a descrição como: **Esta iniciativa foi criada para lidar com todas as definições de política associadas à proteção de recursos**.

1. Para a **Categoria**, escolha entre as opções existentes ou crie uma nova categoria.

1. Percorra a lista de **Definições Disponíveis** (metade direita da página **Definição de iniciativa**) e selecione as definições de política que você deseja adicionar a essa iniciativa. Para a iniciativa **Ficar Seguro**, adicione as seguintes definições de política interna selecionando **+** próximo às informações de definição de política ou selecionando uma linha de definição de política e, em seguida, opção **+ Adicionar** na página de detalhes:

   - Locais permitidos
   - monitora o Endpoint Protection ausente na Central de Segurança do Azure
   - As regras do grupo de segurança de rede para máquinas virtuais para a Internet devem ser protegidas
   - O Backup do Azure deve ser habilitado para máquinas virtuais
   - A criptografia de disco deve ser aplicada em máquinas virtuais

   Depois de selecionar a definição de política na lista, cada uma é adicionada abaixo de **Categoria**.

   ![Analisar parâmetros de definição da iniciativa](../media/create-and-manage/initiative-definition-2.png)

1. Se uma definição de política sendo adicionada à iniciativa tiver parâmetros, eles serão mostrados sob o nome da política na área sob **Categoria**. O _valor_ pode ser definido como 'Definir valor' (embutido em código para todas as atribuições dessa iniciativa) ou 'Usar a iniciativa de parâmetro' (definir durante cada atribuição de iniciativa). Se a opção 'Definir valor' for selecionada, a lista suspensa à direita de _Valores_ permitirá a inserção ou a seleção dos valores. Se 'Usar Parâmetro de Iniciativa' estiver selecionado, uma nova seção de **Parâmetros de iniciativa** será exibida, permitindo que você defina o parâmetro que será definido durante a atribuição de iniciativa. Os valores permitidos nesse parâmetro de iniciativa podem restringir ainda mais o que pode ser definido durante a atribuição da iniciativa.

   ![Alterar parâmetros de definição da iniciativa com base nos valores permitidos](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > No caso de alguns parâmetros `strongType`, a lista de valores não pode ser determinada automaticamente. Nesses casos, um sinal de reticências será exibido à direita da linha de parâmetro. Selecionar essa opção abre a página 'Escopo do parâmetro (&lt;nome do parâmetro&gt;)'. Nessa página, selecione a assinatura a ser usada para fornecer as opções de valor. Esse escopo de parâmetro é usado somente durante a criação da definição de iniciativa e não tem impacto sobre a avaliação de política ou o escopo da iniciativa quando atribuído.

   Defina o parâmetro 'Localizações permitidas' como 'Leste dos EUA 2' e deixe os outros como o padrão 'AuditifNotExists'.

1. Selecione **Salvar**.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Criar uma definição de iniciativa de política com a CLI do Azure

Você pode criar uma definição de iniciativa de política usando CLI do Azure com o comando `az policy set-definition`. Para criar uma definição de iniciativa de política com uma definição de política existente, use o exemplo a seguir:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Criar uma definição de iniciativa de política com o Azure PowerShell

Você pode criar uma definição de iniciativa de política usando Azure PowerShell com o cmdlet `New-AzPolicySetDefinition`. Para criar uma definição de iniciativa de política com uma definição de política existente, use o seguinte arquivo de definição de iniciativa de política como `VMPolicySet.json`:

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>Atribuir uma definição de iniciativa

1. Selecione **Definições** em **Criação** no lado esquerdo da página Azure Policy.

1. Localize a definição de iniciativa **Fique Seguro** que você criou anteriormente e selecione-a. Selecione **Atribuir** na parte superior da página para abrir a página **Manter Segurança: atribuir iniciativa**.

   ![Atribuir uma definição na página Definição da iniciativa](../media/create-and-manage/assign-definition.png)

   Você também pode clicar com o botão direito do mouse na linha selecionada ou selecionar as reticências no final da linha de um menu contextual. Em seguida, selecione **Atribuir**.

   ![Opções alternativas para uma iniciativa](../media/create-and-manage/select-right-click.png)

1. Preencha a página **Fique seguro: Atribuir iniciativa**, inserindo as informações de exemplo a seguir. Você pode usar suas próprias informações.

   - Escopo: O grupo de gerenciamento ou a assinatura na qual você salvou a iniciativa se torna o padrão.
     Você pode alterar o escopo para atribuir a iniciativa para um grupo de recursos ou assinatura dentro do local de salvamento.
   - Exclusões: configure todos os recursos dentro do escopo para evitar que a atribuição iniciativa seja aplicada a eles.
   - Definição de iniciativa e o nome de atribuição: Fique seguro (preenchido como nome da iniciativa que está sendo atribuída).
   - Descrição: esta atribuição de iniciativa é voltada para impor este grupo de definições de política à assinatura.
   - Imposição de política: deixe como o padrão _habilitado_.
   - Atribuído por: preenchido automaticamente com base em quem está conectado. Esse campo é opcional, portanto, valores personalizados podem ser inseridos.

1. Selecione a guia **Parâmetros** na parte superior do assistente. Se você configurou um parâmetro de iniciativa nas etapas anteriores, defina um valor aqui.

1. Selecione a guia **Remediação** na parte superior do assistente. Deixe a opção **Criar uma identidade gerenciada** desmarcada. Esta caixa _precisa_ ser marcada quando a política ou iniciativa que está sendo atribuída inclui uma política com o efeito [deployIfNotExists](../concepts/effects.md#deployifnotexists) ou [modify](../concepts/effects.md#modify). Já que a política usada para este tutorial não tem esse efeito, deixe em branco. Para obter mais informações, confira [identidades gerenciadas](../../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de correção](../how-to/remediate-resources.md#how-remediation-security-works).

1. Selecione a guia **Examinar + criar** na parte superior do assistente.

1. Examine suas seleções e, em seguida, selecione **Criar** na parte inferior da página.

## <a name="check-initial-compliance"></a>Verificar a conformidade inicial

1. Selecione **Conformidade** no lado esquerdo da página do Azure Policy.

1. Localize a iniciativa **Ficar Seguro**. É provável que ainda esteja no _Estado de conformidade_ de **Não iniciado**.
   Selecione a iniciativa para obter todos os detalhes sobre o progresso da atribuição.

   ![Página de conformidade da iniciativa – avaliações não iniciadas](../media/create-and-manage/compliance-status-not-started.png)

1. Quando a atribuição de iniciativa tiver sido concluída, a página de conformidade será atualizada com o _Estado de conformidade_ de **Em conformidade**.

   ![Página de conformidade da iniciativa – recursos compatíveis](../media/create-and-manage/compliance-status-compliant.png)

1. Selecione qualquer política na página de conformidade da iniciativa abre a página de detalhes de conformidade para a política. Esta página fornece detalhes no nível de recursos para fins de conformidade.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Isentar um recurso negado ou sem conformidade usando a Exclusão

Depois de atribuir uma iniciativa de política para exigir uma localização específica, qualquer recurso criado em uma localização diferente será negado. Nesta seção, você verá como resolver uma solicitação negada de criar um recurso solicitando a exclusão de um grupo de recursos único. A exclusão impede a imposição da política (ou iniciativa) desse grupo de recursos. No exemplo a seguir, qualquer localização é permitida no grupo de recursos excluído. Uma exclusão pode ser aplicada a uma assinatura, a um grupo de recursos ou a recursos individuais.

Implantações impedidas por uma política ou iniciativa atribuídas podem ser exibidas no grupo de recursos de destino da implantação: selecione **implantações** no lado esquerdo da página e, em seguida, selecione o **nome de implantação** da implantação com falha. O recurso que foi negado é listado com um status de _Proibido_. Para determinar a política, a iniciativa e a atribuição que negou o recurso, selecione **falha. Clique aqui para obter detalhes->** na página Visão geral da implantação. Uma janela será aberta no lado direito da página com as informações do erro. Em **Detalhes do Erro** estão os GUIDs dos objetos da política selecionada.

![Implantação negada pela atribuição de política](../media/create-and-manage/rg-deployment-denied.png)

Na página Azure Policy: selecione **conformidade** no lado esquerdo da página e selecione a iniciativa obter a política **segura** . Nessa página, há um aumento na contagem de **Negação** para recursos bloqueados. Na guia **Eventos** estão detalhes sobre quem tentou criar ou implantar o recurso que foi negado pela definição de política.

![Visão geral de conformidade de uma política atribuída](../media/create-and-manage/compliance-overview.png)

Neste exemplo, Trent Baker, um dos especialistas de virtualização sênior da Contoso, estava fazendo o trabalho necessário. Precisamos conceder a Trent um espaço para uma exceção. Crie um grupo de recursos, **LocationsExcluded** e, em seguida, conceda a ele uma exceção a essa atribuição de política.

### <a name="update-assignment-with-exclusion"></a>Atribuição de atualização com exclusão

1. Selecione **Atribuições** em **Criação** no lado esquerdo da página Azure Policy.

1. Navegue em todas as atribuições de política e abra a atribuição de política _Ficar Seguro_.

1. Defina a **Exclusão** selecionando o botão de reticências e selecionando o grupo de recursos para excluir, que é _LocationsExcluded_ neste exemplo. Selecione **Adicionar ao Escopo Selecionado** e selecione **Salvar**.

   ![Adicionar um grupo de recursos excluídos à atribuição de política](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > Dependendo da definição política e seu efeito, a exclusão também pode ser concedida a recursos específicos dentro de um grupo de recursos dentro do escopo da atribuição. Como o efeito de **Negação** foi usado neste tutorial, não faria sentido definir a exclusão em um recurso específico que já existe.

1. Selecione **Examinar + salvar** e selecione **Salvar**.

Nesta seção, você resolveu a solicitação negada criando uma exclusão em um único grupo de recursos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver trabalhando com os recursos deste tutorial, use as etapas a seguir para excluir qualquer uma das atribuições ou definições de política criadas acima:

1. Selecione **Definições** (ou **Atribuições** se você estiver tentando excluir uma atribuição) em **Criação** no lado esquerdo da página Azure Policy.

1. Pesquisar pela nova iniciativa ou definição de política (ou atribuição) que você quer remover.

1. Clique com o botão direito na linha e selecione as reticências no final da definição ou da atribuição e selecione **Excluir Definição** (ou **Excluir Atribuição**).

## <a name="review"></a>Revisão

Neste tutorial, você realizou as seguintes tarefas com sucesso:

> [!div class="checklist"]
> - Atribuiu uma política para impor uma condição para os recursos que você criar no futuro
> - Criou e atribuiu uma definição de iniciativa para acompanhar a conformidade de vários recursos
> - Resolveu um recurso negado ou sem conformidade
> - Implementou uma nova política em toda a organização

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Para saber mais sobre as estruturas das definições de políticas, consulte este artigo:

> [!div class="nextstepaction"]
> [Estrutura de definição do Azure Policy](../concepts/definition-structure.md)