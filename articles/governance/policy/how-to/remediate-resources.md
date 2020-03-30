---
title: Corrigir recursos sem conformidade
description: Este guia orienta você sobre a remediação de recursos que não estão em conformidade com as políticas da Política Azure.
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 71af5c81e0dce4d5c0a0461534f634db36bd66a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471380"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Corrigir recursos que não estão em conformidade com o Azure Policy

Os recursos que não estão em conformidade com uma política **deployIfNotExist** ou **modify** podem ser colocados em um estado compatível através da **Remediação**. A remediação é realizada instruindo a Política Do Azure a executar o efeito **deployIfNotExists** ou as **operações** de marcação da política atribuída em seus recursos existentes, seja para um grupo de gerenciamento, uma assinatura, um grupo de recursos ou um recurso individual. Este artigo mostra os passos necessários para entender e realizar a remediação com a Política do Azure.

## <a name="how-remediation-security-works"></a>Como funciona a correção de segurança

Quando o Azure Policy executa o modelo na definição de diretiva **deployIfNotExist,** ele o faz usando uma [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md).
A Azure Policy cria uma identidade gerenciada para cada atribuição, mas deve ter detalhes sobre quais funções conceder a identidade gerenciada. Se a identidade gerenciada não tiver funções, esse erro será exibido durante a atribuição da política ou uma iniciativa. Ao usar o portal, a Diretiva Azure concede automaticamente à identidade gerenciada as funções listadas quando a atribuição é iniciada. A _localização_ da identidade gerenciada não afeta sua operação com a Política Do Azure.

![Identidade gerenciada – função ausente](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Se um recurso modificado pelo **deployIfNotExist** ou **modificar** estiver fora do escopo da atribuição de diretiva ou o modelo acessar propriedades em recursos fora do escopo da atribuição de diretiva, a identidade gerenciada da atribuição deve ser [concedida manualmente ou](#manually-configure-the-managed-identity) a implantação de remediação falhará.

## <a name="configure-policy-definition"></a>Configurar a definição de política

O primeiro passo é definir as funções que **implantamIfNotExist** e **modificar** necessidades na definição de diretiva para implantar com sucesso o conteúdo do modelo incluído. Na propriedade **detalhes**, adicione uma propriedade **roleDefinitionIds**. Essa propriedade é uma matriz de cadeias de caracteres que correspondem a funções no ambiente. Para um exemplo completo, consulte o [exemplo deployIfNotExist](../concepts/effects.md#deployifnotexists-example) ou os [exemplos de modificação](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

A propriedade **roleDefinitionIds** usa o identificador completo de recursos e não assume o papel **curtoNome** da função. Para obter a ID para a função 'Colaborador' em seu ambiente, use o seguinte código:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Configurar manualmente a identidade gerenciada

Ao criar uma atribuição usando o portal, a Azure Policy gera a identidade gerenciada e concede-lhe as funções definidas em **roleDefinitionIds**. Nas seguintes condições, as etapas usadas para criar a identidade gerenciada e atribuir permissões a ela precisam ser feitas manualmente:

- Ao usar o SDK (como o Azure PowerShell)
- Quando um recurso fora do escopo de atribuição é modificado pelo modelo
- Quando um recurso fora do escopo de atribuição é lido pelo modelo

> [!NOTE]
> O Azure PowerShell e o .NET são os únicos SDKs que atualmente dão suporte a essa funcionalidade.

### <a name="create-managed-identity-with-powershell"></a>Criar identidade gerenciada com o PowerShell

Para criar uma identidade gerenciada durante a atribuição da política, o **Local** deve ser definido e **AssignIdentity** deve ser usada. O exemplo a seguir obtém a definição da política interna **Implantar Transparent Data Encryption do Banco de Dados SQL**, define o grupo de recursos de destino e, em seguida, cria a atribuição.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

A variável `$assignment` agora contém a ID da entidade de segurança referente à identidade gerenciada, juntamente com os valores padrão retornados durante a criação de uma atribuição de política. Ela pode ser acessada por meio de `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Conceder funções definidas com o PowerShell

A nova identidade gerenciada deve concluir a replicação por meio do Azure Active Directory antes que possam ser concedidas a ela as funções necessárias. Após a conclusão da replicação, o exemplo a seguir itera a definição de política em `$policyDef` para **roleDefinitionIds** e usa [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para conceder as funções à nova identidade gerenciada.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Conceder funções definidas por meio do portal

Há duas maneiras de conceder uma identidade gerenciada da atribuição às funções definidas usando o portal: usando **Controle de acesso (IAM)** ou editando a atribuição de política ou iniciativa e clicando em **Salvar**.

Para adicionar uma função à identidade gerenciada da atribuição, siga estas etapas:

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

1. Selecione **Atribuições** no lado esquerdo da página de Política do Azure.

1. Localize a atribuição que tem uma identidade gerenciada e clique no nome.

1. Localize a propriedade **ID de Atribuição** na página de edição. A ID de atribuição será algo como:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   O nome da identidade gerenciada é a última parte da ID do recurso de atribuição, que é `2802056bfc094dfb95d4d7a5` neste exemplo. Copie essa parte da ID do recurso da atribuição.

1. Navegue até o recurso ou os recursos do contêiner pai (grupo de recursos, assinatura, grupo de gerenciamento) que precisa na definição de função adicionada manualmente.

1. Clique no link **Controle de acesso (IAM)** na página de recursos e clique em **+ Adicionar atribuição de função** na parte superior da página do controle de acesso.

1. Selecione a função apropriada que corresponde a **roleDefinitionIds** da definição de política.
   Deixe **Atribuir acesso** definido como o padrão de 'Usuário, grupo ou aplicativo do Azure AD'. Na caixa **Selecionar**, cole ou digite a parte da ID do recurso de atribuição localizada anteriormente. Depois que a pesquisa for concluída, clique no objeto com o mesmo nome para selecionar a ID e clique em **Salvar**.

## <a name="create-a-remediation-task"></a>Criar uma tarefa de correção

### <a name="create-a-remediation-task-through-portal"></a>Crie uma tarefa de remediação através do portal

Durante a avaliação, a atribuição de diretiva com **implanteSNãoExiste** ou **modifica** efeitos determina se há recursos não compatíveis. Quando forem encontrados recursos sem conformidade, os detalhes serão fornecidos na página **Correção**. Junto com a lista de políticas que têm recursos sem conformidade está a opção para disparar uma **tarefa de correção**. Essa opção é o que cria uma implantação a partir do modelo **deployIfNotExist** ou das operações **de modificação.**

Para criar uma **tarefas de correção**, siga estas etapas:

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

   ![Pesquisar Política em Todos os Serviços](../media/remediate-resources/search-policy.png)

1. Selecione **Correção** no lado esquerdo da página do Azure Policy.

   ![Selecione Remediação na página Política](../media/remediate-resources/select-remediation.png)

1. Todos os **implantesIfNotExist** e **modifiquem** as atribuições de diretiva com recursos não compatíveis estão incluídos nas **Políticas para remediar a** tabela de dados e a tabela de dados. Clique em uma política com recursos sem conformidade. A página **Nova tarefa de correção** é aberta.

   > [!NOTE]
   > Uma maneira alternativa de abrir a página **tarefa de correção** é localizar e clicar na política na página **Conformidade** e, em seguida, clicar no botão **Criar tarefa de correção**.

1. Na página **Nova tarefa de correção**, filtre os recursos a serem corrigidos usando as reticências de **Escopo** para escolher os recursos filho nos quais a política está atribuída (incluindo até os objetos do recurso individuais). Além disso, use a lista suspensa **Locais** para filtrar ainda mais os recursos. Somente os recursos listados na tabela serão corrigidos.

   ![Remediar - selecione quais recursos remediar](../media/remediate-resources/select-resources.png)

1. Inicie a tarefa de correção depois que os recursos forem filtrados clicando em **Corrigir**. A página de conformidade de políticas é aberta na guia **Tarefas de Remediação** para mostrar o progresso das tarefas. As implantações criadas pela tarefa de remediação começam imediatamente.

   ![Remediar - progresso de tarefas de remediação](../media/remediate-resources/task-progress.png)

1. Clique na **tarefa de correção** na página de conformidade de política para obter detalhes sobre o progresso. A filtragem usada para a tarefa é mostrada junto com uma lista dos recursos que estão sendo corrigidos.

1. Na página **Tarefa de correção**, clique com o botão direito do mouse em um recurso para exibir o recurso ou a implantação da tarefa de correção. No final da linha, clique em **Eventos relacionados** para ver detalhes como uma mensagem de erro.

   ![Remedir – menu de contexto da tarefa de recursos](../media/remediate-resources/resource-task-context-menu.png)

Os recursos implantados por meio de uma **tarefa de correção** são adicionados à guia **Recursos Implantados** na página de conformidade com a política.

### <a name="create-a-remediation-task-through-azure-cli"></a>Crie uma tarefa de remediação através do Azure CLI

Para criar uma **tarefa de remediação** `az policy remediation` com o Azure CLI, use os comandos. Substitua `{subscriptionId}` por seu `{myAssignmentId}` ID de assinatura e por sua **implantaçãoIfNotExist** ou **modifique** o ID de atribuição de diretiva.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Para outros comandos e exemplos de remediação, consulte os comandos [de remediação de políticas az.](/cli/azure/policy/remediation)

### <a name="create-a-remediation-task-through-azure-powershell"></a>Crie uma tarefa de remediação através do Azure PowerShell

Para criar uma **tarefa de remediação** com `Start-AzPolicyRemediation` o Azure PowerShell, use os comandos. Substitua `{subscriptionId}` por seu `{myAssignmentId}` ID de assinatura e por sua **implantaçãoIfNotExist** ou **modifique** o ID de atribuição de diretiva.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Para obter outros cmdlets e exemplos de remediação, consulte o módulo [Az.PolicyInsights.](/powershell/module/az.policyinsights/#policy_insights)

## <a name="next-steps"></a>Próximas etapas

- Revisar exemplos em [amostras de política do Azure](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Saiba como [obter dados de conformidade](get-compliance-data.md).
- Reveja o que é um grupo de gestão com [organize seus recursos com grupos de gerenciamento do Azure.](../../management-groups/overview.md)
