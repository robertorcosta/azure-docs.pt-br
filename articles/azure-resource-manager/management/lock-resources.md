---
title: Bloqueio de recursos para prevenir alterações
description: Impeça que os usuários atualizem ou excluam recursos críticos do Azure ao aplicar um bloqueio a todos os usuários e funções.
ms.topic: conceptual
ms.date: 06/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e76287c4524831a84a22fb23ddf8a5fdee8bc12b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87827275"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Bloquear recursos para evitar alterações inesperadas

Como administrador, talvez você precise bloquear uma assinatura, um recurso ou grupo de recursos para impedir que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos. É possível definir o nível de bloqueio como **CanNotDelete** ou **ReadOnly**. No portal, os bloqueios são chamados **Excluir** e **Somente leitura** respectivamente.

* **CanNotDelete** significa que os usuários autorizados ainda poderão ler e modificar um recurso, mas não poderão excluir o recurso.
* **ReadOnly** significa que os usuários autorizados poderão ler um recurso, mas não poderão excluir ou atualizar o recurso. Aplicar esse bloqueio é semelhante ao restringir todos os usuários autorizados para as permissões concedidas pela função **Leitor**.

## <a name="how-locks-are-applied"></a>Como os bloqueios são aplicados

Quando você aplica um bloqueio a um escopo pai, todos os recursos filho herdam o mesmo bloqueio. Até mesmo os recursos que você adiciona posteriormente herdam o bloqueio do pai. O bloqueio mais restritivo na herança terá precedência.

Ao contrário do controle de acesso baseado em função, é possível usar bloqueios de gerenciamento para aplicar uma restrição a todos os usuários e a todas as funções. Para saber mais sobre como definir permissões para usuários e funções, consulte [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/role-assignments-portal.md).

Bloqueios do Resource Manager se aplicam apenas às operações que ocorrem no plano de gerenciamento, que consistem em operações enviadas para `https://management.azure.com`. Os bloqueios não restringem a maneira como os recursos executam suas próprias funções. Alterações de recursos são restritas, mas as operações de recursos não são restritas. Por exemplo, um bloqueio de ReadOnly em um banco de dados SQL impede que você de exclua ou modifique o banco de dados. Ele não impede você de criar, atualizar ou excluir dados no banco de dados. As transações de dados são permitidas porque essas operações não são enviadas para `https://management.azure.com`.

## <a name="considerations-before-applying-locks"></a>Considerações antes da aplicação de bloqueios

A aplicação de bloqueios pode gerar resultados inesperados porque algumas operações que não parecem modificar o recurso, na verdade, exigem ações impedidas pelo bloqueio. Alguns exemplos comuns de operações que são impedidas por bloqueios são:

* Um bloqueio do tipo somente leitura em uma **conta de armazenamento** impede que todos os usuários listem as chaves. A operação de lista de chaves é tratada por meio de uma solicitação POST, pois as chaves retornadas estão disponíveis para operações de gravação.

* Um bloqueio do tipo somente leitura em um recurso do **Serviço de Aplicativo** impedirá o Gerenciador de Servidores do Visual Studio de exibir os arquivos para o recurso, pois essa interação exige acesso de gravação.

* Um bloqueio do tipo somente leitura em um **grupo de recursos** que contém uma **máquina virtual** impede que qualquer usuário inicie ou reinicie a máquina virtual. Essas operações requerem uma solicitação POST.

* Um bloqueio de não-exclusão em um **grupo de recursos** impede que Azure Resource Manager [exclua automaticamente as implantações](../templates/deployment-history-deletions.md) no histórico. Se você alcançar as implantações 800 no histórico, suas implantações falharão.

* Um bloqueio do tipo não excluir no **grupo de recursos** criado pelo **Serviço de Backup do Azure** causa falha nos backups. O serviço dá suporte a um máximo de 18 pontos de restauração. Quando bloqueado, o serviço de backup não consegue limpar os pontos de restauração. Para obter mais informações, veja [Perguntas frequentes sobre Backup de VMs do Azure](../../backup/backup-azure-vm-backup-faq.md).

* Um bloqueio do tipo somente leitura em uma **assinatura** impede que **Assistente do Azure** funcione corretamente. O Assistente não consegue armazenar os resultados de suas consultas.

## <a name="who-can-create-or-delete-locks"></a>Quem pode criar ou excluir bloqueios

Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso às ações `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*`. Das funções internas, somente **Proprietário** e **Administrador do Acesso de Usuário** recebem essas ações.

## <a name="managed-applications-and-locks"></a>Aplicativos gerenciados e bloqueios

Alguns serviços do Azure, como o Azure Databricks, usam [aplicativos gerenciados](../managed-applications/overview.md) para implementar o serviço. Nesse caso, o serviço cria dois grupos de recursos. Um grupo de recursos contém uma visão geral do serviço e não está bloqueado. O outro grupo de recursos contém a infraestrutura para o serviço e está bloqueado.

Se tentar excluir o grupo de recursos de infraestrutura, você receberá um erro informando que o grupo de recursos está bloqueado. Se tentar excluir o bloqueio para o grupo de recursos de infraestrutura, você receberá um erro informando que o bloqueio não pode ser excluído porque ele é propriedade de um aplicativo do sistema.

Em vez disso, exclua o serviço, o que também excluirá o grupo de recursos de infraestrutura.

Para aplicativos gerenciados, selecione o serviço que você implantou.

![Selecione o serviço](./media/lock-resources/select-service.png)

Observe que o serviço inclui um link para um **Grupo de Recursos Gerenciados**. Esse grupo de recursos contém a infraestrutura e está bloqueado. Ele não pode ser excluído diretamente.

![Exibição do grupo gerenciado](./media/lock-resources/show-managed-group.png)

Para excluir tudo para o serviço, incluindo o grupo de recursos de infraestrutura bloqueado, selecione **Excluir** para o serviço.

![Excluir serviço](./media/lock-resources/delete-service.png)

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Modelo

Quando usar um modelo do Resource Manager para implantar um bloqueio, use valores diferentes para o nome e tipo, dependendo do escopo do bloqueio.

Quando aplicar um bloqueio a um **recurso**, use os seguintes formatos:

* nome - `{resourceName}/Microsoft.Authorization/{lockName}`
* tipo - `{resourceProviderNamespace}/{resourceType}/providers/locks`

Quando aplicar um bloqueio a um **grupo de recursos** ou **assinatura**, use os seguintes formatos:

* nome - `{lockName}`
* tipo - `Microsoft.Authorization/locks`

O exemplo a seguir mostra um modelo que cria um plano de serviço de aplicativo, um site da Web e um bloqueio no site da Web. O tipo de recurso do bloqueio é o tipo de recurso do recurso a ser bloqueado e **/providers/bloqueios**. O nome do bloqueio é criado por meio da concatenação do nome do recurso com **/Microsoft.Authorization/** e o nome do bloqueio.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2019-08-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "tier": "Free",
        "name": "f1",
        "capacity": 0
      },
      "properties": {
        "targetWorkerCount": 1
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2019-08-01",
      "name": "[variables('siteName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Web/sites/providers/locks",
      "apiVersion": "2016-09-01",
      "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
      ],
      "properties": {
        "level": "CanNotDelete",
        "notes": "Site should not be deleted."
      }
    }
  ]
}
```

Para obter um exemplo de como definir um bloqueio em um grupo de recursos, veja [Criação e bloqueio de um grupo de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell

Bloqueie recursos implantados com o Azure PowerShell usando o comando [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock).

Para bloquear um recurso, forneça o nome dele, seu tipo de recurso e o nome do grupo de recursos.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Para bloquear um grupo de recursos, forneça o nome dele.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Para saber mais sobre um bloqueio, use [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Para obter todos os bloqueios em sua assinatura, use:

```azurepowershell-interactive
Get-AzResourceLock
```

Para obter todos os bloqueios de um recurso, use:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Para obter todos os bloqueios de um grupo de recursos, use:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Para excluir um bloqueio, use:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>CLI do Azure

Bloqueie recursos implantados com a CLI do Azure usando o comando [az lock create](/cli/azure/lock#az-lock-create).

Para bloquear um recurso, forneça o nome dele, seu tipo de recurso e o nome do grupo de recursos.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Para bloquear um grupo de recursos, forneça o nome dele.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Para saber mais sobre um bloqueio, use [az lock list](/cli/azure/lock#az-lock-list). Para obter todos os bloqueios em sua assinatura, use:

```azurecli
az lock list
```

Para obter todos os bloqueios de um recurso, use:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Para obter todos os bloqueios de um grupo de recursos, use:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Para excluir um bloqueio, use:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>API REST

Você pode bloquear os recursos implantados com a [API REST para bloqueios de gerenciamento](/rest/api/resources/managementlocks). A API REST permite que você crie e exclua bloqueios e recupere informações sobre bloqueios existentes.

Para criar um bloqueio, execute:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

O escopo pode ser uma assinatura, grupo de recursos ou recurso. O nome do bloqueio é como você deseja chamar o bloqueio. Para a versão da API, use **2016-09-01**.

Na solicitação, inclua um objeto JSON que especifica as propriedades do bloqueio.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a organização lógica de recursos, veja [Uso de marcas para organizar os recursos](tag-resources.md).
* É possível aplicar restrições e convenções em sua assinatura com políticas personalizadas. Para saber mais, veja [O que é o Azure Policy?](../../governance/policy/overview.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](/azure/architecture/cloud-adoption-guide/subscription-governance).
