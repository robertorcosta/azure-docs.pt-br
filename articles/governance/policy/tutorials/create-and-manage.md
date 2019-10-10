---
title: Criar e gerenciar políticas para impor a conformidade
description: Use a Política do Azure para impor padrões, atender requisitos de auditoria e regulamentares, controlar custos, manter a consistência de segurança e desempenho e impor princípios de design em toda a empresa.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/04/2019
ms.topic: tutorial
ms.service: azure-policy
ms.openlocfilehash: 82c56a01d7f14f1eeae26dfa784f3545552bf6b9
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981380"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Tutorial: Criar e gerenciar políticas para impor a conformidade

Compreender como criar e gerenciar políticas no Azure é importante para manter a conformidade com padrões corporativos e com contratos de nível de serviço. Neste tutorial, você aprenderá a usar o Azure Policy para realizar algumas das tarefas mais comuns relacionadas à criação, à atribuição e ao gerenciamento de políticas em sua organização, como:

> [!div class="checklist"]
> - Atribuir uma política para impor uma condição para os recursos que você criar no futuro
> - Criar e atribuir uma definição de iniciativa para acompanhar a conformidade de vários recursos
> - Resolver um recurso negado ou sem conformidade
> - Implementar uma nova política em toda a organização

Se você deseja atribuir uma política para identificar o estado atual de conformidade dos recursos existentes, os artigos de início rápido falam sobre como fazer isso. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="assign-a-policy"></a>Atribuir uma política

A primeira etapa para impor a conformidade com a Política do Azure é atribuir uma definição de política. Uma definição de política define sob quais condições a política será aplicada e qual efeito será tomado Neste exemplo, atribua uma definição de política interna chamada *Exigir SQL Server versão 12.0* para impor a condição de que todos os bancos de dados do SQL Server devem ser a v12.0 para estar em conformidade.

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

   ![Pesquisar Política em Todos os Serviços](../media/create-and-manage/search-policy.png)

1. Selecione **Atribuições** no lado esquerdo da página de Política do Azure. Uma atribuição é uma política que foi atribuída para entrar em vigor em um escopo específico.

   ![Selecionar Atribuições na página Visão geral da política](../media/create-and-manage/select-assignments.png)

1. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   ![Atribuir uma definição de política na página Atribuições](../media/create-and-manage/select-assign-policy.png)

1. Na página **Atribuir Política**, selecione o **Escopo** clicando no botão de reticências e selecionando uma assinatura ou então um grupo de gerenciamento. Opcionalmente, selecione um grupo de recursos. Um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Em seguida, clique em **Selecionar** na parte inferior da página **Escopo**.

   Este exemplo usa a assinatura da **Contoso**. Sua assinatura será diferente.

1. Recursos que podem ser excluídos com base no **Escopo**. **Exclusões** começam um nível abaixo do **Escopo**. **Exclusões** são opcionais, então deixe-as em branco por enquanto.

1. Selecione a reticências **Definição de política** para abrir a lista de definições. Você pode filtrar a definição da política **Tipo** para *Interna* para exibir todas as políticas e ler suas descrições.

1. Selecione **Solicitar SQL Server versão 12.0**. Se você não conseguir localizá-la imediatamente, digite **require sql server** na caixa de pesquisa, pressione ENTER e clique fora da caixa de pesquisa. Clique em **Selecionar** na parte inferior da página **Definições** depois de ter encontrado e selecionado a definição de política.

   ![Usar o filtro de pesquisa para localizar uma política](../media/create-and-manage/select-available-definition.png)

1. O **Nome da atribuição** é automaticamente preenchido com o nome da política selecionada, mas você pode alterá-lo. Para este exemplo, use *Exigir SQL Server versão 12.0*. Você também pode adicionar uma **Descrição**opcional. A descrição fornece detalhes sobre essa atribuição de política.
   **Atribuído por** é preenchido automaticamente com base em quem está conectado. Esse campo é opcional, portanto, valores personalizados podem ser inseridos.

1. Deixe a opção **Criar uma identidade gerenciada** desmarcada. Esta caixa _precisa_ ser marcada quando a política ou iniciativa que está sendo atribuída inclui uma política com o efeito [deployIfNotExists](../concepts/effects.md#deployifnotexists). Já que a política usada para este tutorial não tem esse efeito, deixe em branco. Para obter mais informações, confira [identidades gerenciadas](../../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de correção](../how-to/remediate-resources.md#how-remediation-security-works).

1. Clique em **Atribuir**.

## <a name="implement-a-new-custom-policy"></a>Implementar uma nova política personalizada

Agora que você atribuiu uma definição de política interna, você pode fazer muito mais com o Azure Policy. Em seguida, crie uma nova política personalizada para economizar custos, validando que as VMs criadas em seu ambiente não podem estar na série G. Dessa forma, sempre que um usuário na sua organização tentar criar uma VM na série G, a solicitação é negada.

1. Selecione **Definições** em **Criação** no lado esquerdo da página Azure Policy.

   ![Página de definição em Grupo de criação](../media/create-and-manage/definition-under-authoring.png)

1. Selecione **+ Definição de política** na parte superior da página. Isso abre a página **Definição de política**.

1. Insira as seguintes informações:

   - O grupo de gerenciamento ou a assinatura em que a definição de política é salva. Selecione usando o botão de reticências no **local da definição**.

     > [!NOTE]
     > Se você planeja aplicar esta definição de política a várias assinaturas, o local deve ser um grupo de gerenciamento que contém as assinaturas para as quais você atribuirá a política. O mesmo é verdadeiro para uma definição de iniciativa.

   - O nome da definição de política – *Exigir SKUs de VM menores que a série G*
   - A descrição do que a definição de política se destina a fazer – *Esta definição de política impõe que todas as VMs criadas nesse escopo tenham SKUs menores que a série G para reduzir o custo.*
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

   A propriedade *field* na regra de política deve ser um dos seguintes valores: Nome, Tipo, Localização, Tags ou um alias. Um exemplo de um alias pode ser `"Microsoft.Compute/VirtualMachines/Size"`.

   Para exibir mais exemplos de política do Azure, confira [Exemplos do Azure Policy](../samples/index.md).

1. Clique em **Salvar**.

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

Você pode criar uma definição de política usando a CLI do Azure com o comando de definição de política. Para criar uma definição de política com uma regra embutida, use o exemplo a seguir:

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

1. Use as reticências do **Local da definição** para selecionar um grupo de gerenciamento ou uma assinatura para armazenar a definição. Se a página anterior fosse o escopo para um único grupo de gerenciamento ou assinatura, o **Local da definição** seria populado automaticamente.

1. Insira o  **Nome** e a **Descrição** da iniciativa.

   Este exemplo valida os recursos estão em conformidade com as definições de política sobre como ficar seguro. Dê à iniciativa o nome **Ficar seguro** e defina a descrição como: **Esta iniciativa foi criada para lidar com todas as definições de política associadas à proteção de recursos**.

1. Para a **Categoria**, escolha entre as opções existentes ou crie uma nova categoria.

1. Percorra a lista de **Definições Disponíveis** (metade direita da página **Definição de iniciativa**) e selecione as definições de política que você deseja adicionar a essa iniciativa. Para a iniciativa **Obter seguro** iniciativa, adicione as seguintes definições de política interna clicando em **+** próximo às informações de definição de política ou clicando em uma linha de definição de política e, em seguida, opção **+ Adicionar** na página de detalhes:

   - Requer o SQL Server versão 12.0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   Após selecionar a definição de política na lista, ela é adicionada em **Políticas e Parâmetros**.

   ![Analisar parâmetros de definição da iniciativa](../media/create-and-manage/initiative-definition-2.png)

1. Se uma definição de política sendo adicionada à iniciativa tiver parâmetros, eles serão mostrados sob o nome da política na área **Políticas e Parâmetros**. O _valor_ pode ser definido como 'Definir valor' (embutido em código para todas as atribuições dessa iniciativa) ou 'Usar a iniciativa de parâmetro' (definir durante cada atribuição de iniciativa). Se a opção 'Definir valor' for selecionada, a lista suspensa à direita de _Valores_ permitirá a inserção ou a seleção dos valores. Se 'Usar Parâmetro de Iniciativa' estiver selecionado, uma nova seção de **Parâmetros de iniciativa** será exibida, permitindo que você defina o parâmetro que será definido durante a atribuição de iniciativa. Os valores permitidos nesse parâmetro de iniciativa podem restringir ainda mais o que pode ser definido durante a atribuição da iniciativa.

   ![Alterar parâmetros de definição da iniciativa com base nos valores permitidos](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > No caso de alguns parâmetros `strongType`, a lista de valores não pode ser determinada automaticamente. Nesses casos, um sinal de reticências será exibido à direita da linha de parâmetro. Clicar nela abre a página “Escopo do parâmetro (&lt;nome do parâmetro&gt;)”. Nessa página, selecione a assinatura a ser usada para fornecer as opções de valor. Esse escopo de parâmetro é usado somente durante a criação da definição de iniciativa e não tem impacto sobre a avaliação de política ou o escopo da iniciativa quando atribuído.

1. Clique em **Save** (Salvar).

### <a name="assign-an-initiative-definition"></a>Atribuir uma definição de iniciativa

1. Selecione **Definições** em **Criação** no lado esquerdo da página Azure Policy.

1. Localize a definição de iniciativa **Manter Segurança** que você criou anteriormente e clique nela. Selecione **Atribuir** na parte superior da página para abrir a página **Ficar seguro: atribuir iniciativa**.

   ![Atribuir uma definição na página Definição da iniciativa](../media/create-and-manage/assign-definition.png)

   Você também pode clicar com o botão direito do mouse na linha selecionada ou clicar nas reticências no final da linha de um menu contextual. Em seguida, selecione **Atribuir**.

   ![Opções alternativas para uma iniciativa](../media/create-and-manage/select-right-click.png)

1. Preencha a página **Ficar seguro: atribuir iniciativa** inserindo as informações de exemplo a seguir. Você pode usar suas próprias informações.

   - Escopo: o grupo de gerenciamento ou a assinatura na qual você salvou a iniciativa se torna o padrão.
     Você pode alterar o escopo para atribuir a iniciativa para um grupo de recursos ou assinatura dentro do local de salvamento.
   - Exclusões: configure todos os recursos dentro do escopo para evitar que a atribuição iniciativa seja aplicada a eles.
   - Definição de iniciativa e o nome da Atribuição: Ficar seguro (previamente preenchido como nome da iniciativa que está sendo atribuída).
   - Descrição: essa atribuição de iniciativa é voltada para impor este grupo de definições de política à assinatura.
   - Atribuído por: preenchido automaticamente com base em quem está conectado. Esse campo é opcional, portanto, valores personalizados podem ser inseridos.

1. Deixe a opção **Criar uma identidade gerenciada** desmarcada. Esta caixa _precisa_ ser marcada quando a política ou iniciativa que está sendo atribuída inclui uma política com o efeito [deployIfNotExists](../concepts/effects.md#deployifnotexists). Já que a política usada para este tutorial não tem esse efeito, deixe em branco. Para obter mais informações, confira [identidades gerenciadas](../../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de correção](../how-to/remediate-resources.md#how-remediation-security-works).

1. Clique em **Atribuir**.

## <a name="check-initial-compliance"></a>Verificar a conformidade inicial

1. Selecione **Conformidade** no lado esquerdo da página do Azure Policy.

1. Localize a iniciativa **Obter Origem**. É provável que ainda esteja no _Estado de conformidade_ de **Não iniciado**.
   Clique na iniciativa para obter todos os detalhes sobre o progresso da atribuição.

   ![Página de conformidade da iniciativa – avaliações não iniciadas](../media/create-and-manage/compliance-status-not-started.png)

1. Quando a atribuição de iniciativa tiver sido concluída, a página de conformidade será atualizada com o _Estado de conformidade_ de **Em conformidade**.

   ![Página de conformidade da iniciativa – recursos compatíveis](../media/create-and-manage/compliance-status-compliant.png)

1. Clicar em qualquer política na página de conformidade da iniciativa abre a página de detalhes de conformidade da política. Esta página fornece detalhes no nível de recursos para fins de conformidade.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Isentar um recurso negado ou sem conformidade usando a Exclusão

Seguindo o exemplo acima, após atribuir a definição de política para exigir o SQL Server versão 12.0, um SQL Server criado com uma versão diferente da 12.0 seria recusado. Nesta seção, você verá como resolver uma solicitação negada de criar um SQL Server solicitando a exclusão de um grupo de recursos único. A exclusão impede a imposição da política (ou iniciativa) desse recurso.
No exemplo a seguir, qualquer versão do SQL Server pode ser em um único grupo de recursos. Uma exclusão pode ser aplicada a uma assinatura, um grupo de recursos, ou você pode restringir a exclusão a recursos individuais.

Uma implantação impedida por uma política atribuída ou iniciativa pode ser exibida em dois locais:

- No grupo de recursos direcionado pela implantação: Selecione **Implantações** no lado esquerdo da página e, em seguida, clique no **Nome da Implantação** da implantação com falha. O recurso que foi negado é listado com um status de _Proibido_. Para determinar a política ou iniciativa e atribuição que negou o recurso, clique em **Falhou. Clique aqui para obter detalhes ->** na página Visão geral da implantação. Uma janela será aberta no lado direito da página com as informações do erro. Em **Detalhes do Erro** estão os GUIDs dos objetos da política selecionada.

  ![Implantação negada pela atribuição de política](../media/create-and-manage/rg-deployment-denied.png)

- Na página do Azure Policy: selecione **Conformidade** no lado esquerdo da página e clique em **Exigir SQL Server política versão 12.0**. Na página que é aberta, você verá um aumento na conta **Negar**. Na guia **Eventos**, você também observa a tentativa de implantação que foi negada pela política.

  ![Visão geral de conformidade de uma política atribuída](../media/create-and-manage/compliance-overview.png)

Neste exemplo, Trent Baker, um dos especialistas de virtualização sênior da Contoso, estava fazendo o trabalho necessário. É necessário conceder a Trent uma exceção, mas nós não desejamos servidores que não sejam da versão 12.0 SQL em qualquer grupo de recursos. Criamos um novo grupo de recursos, **SQLServers_Excluded** e agora concederão uma exceção a essa atribuição de política.

### <a name="update-assignment-with-exclusion"></a>Atribuição de atualização com exclusão

1. Selecione **Atribuições** em **Criação** no lado esquerdo da página Azure Policy.

1. Navegue por todas as atribuições de política e abra a atribuição *Exigir SQL Server versão 12.0*.

1. Definir o **Exclusão** clicando no botão de reticências e selecionando o grupo de recursos para excluir, *SQLServers_Excluded* neste exemplo.

   ![Adicionar um grupo de recursos excluídos à atribuição de política](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > Dependendo da política e seu efeito, a exclusão também pode ser concedida a recursos específicos dentro de um grupo de recursos dentro do escopo da atribuição. Como o efeito **Negar** foi usado neste tutorial, não faria sentido definir a exclusão em um recurso específico que já existe.

1. Clique em **Selecionar** e em **Salvar**.

Nesta seção, você resolveu a solicitação negada criando uma exclusão em um único grupo de recursos.

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver trabalhando com os recursos deste tutorial, use as etapas a seguir para excluir qualquer uma das atribuições ou definições criadas acima:

1. Selecione **Definições** (ou **Atribuições** se você estiver tentando excluir uma atribuição) em **Criação** no lado esquerdo da página Azure Policy.

1. Pesquisar pela nova iniciativa ou definição de política (ou atribuição) que você quer remover.

1. Clique com o botão direito na linha e selecione as reticências no final da definição ou da atribuição e selecione **Excluir Definição** (ou **Excluir Atribuição**).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você realizou as seguintes tarefas com sucesso:

> [!div class="checklist"]
> - Atribuiu uma política para impor uma condição para os recursos que você criar no futuro
> - Criou e atribuiu uma definição de iniciativa para acompanhar a conformidade de vários recursos
> - Resolveu um recurso negado ou sem conformidade
> - Implementou uma nova política em toda a organização

Para saber mais sobre as estruturas das definições de políticas, consulte este artigo:

> [!div class="nextstepaction"]
> [Estrutura de definição do Azure Policy](../concepts/definition-structure.md)