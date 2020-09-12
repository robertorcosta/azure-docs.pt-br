---
title: Marcar recursos, grupos de recursos e assinaturas para a organização lógica
description: Mostra como aplicar marcas para organizar os recursos do Azure para cobrança e gerenciamento.
ms.topic: conceptual
ms.date: 07/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1eaf9b735e65811b242fa7198b3545c9c68a4d46
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89425986"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Use marcas para organizar os recursos e a hierarquia de gerenciamento do Azure

Você aplica marcas aos recursos, grupos de recursos e assinaturas do Azure para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

Para obter recomendações sobre como implementar uma estratégia de marcação, consulte [nomenclatura de recursos e guia de decisão de marcação](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

> [!IMPORTANT]
> Os nomes de marca não diferenciam maiúsculas de minúsculas para operações. Uma marca com um nome de marca, independentemente de maiúsculas e minúsculas, é atualizada ou recuperada. No entanto, o provedor de recursos pode manter a capitalização que você fornece para o nome da marca. Você verá essa capitalização nos relatórios de custo.
> 
> Os valores de marca diferenciam maiúsculas de minúsculas.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Acesso necessário

Para aplicar marcas a um recurso, você deve ter acesso de gravação ao tipo de recurso **Microsoft. Resources/Tags** . A função de [colaborador de marca](../../role-based-access-control/built-in-roles.md#tag-contributor) permite aplicar marcas a uma entidade sem ter acesso à própria entidade. Atualmente, a função de colaborador de marca não pode aplicar marcas a recursos ou grupos de recursos por meio do Portal. Ele pode aplicar marcas às assinaturas por meio do Portal. Ele dá suporte a todas as operações de marca por meio do PowerShell e da API REST.  

A função [colaborador](../../role-based-access-control/built-in-roles.md#contributor) também concede o acesso necessário para aplicar marcas a qualquer entidade. Para aplicar marcas a apenas um tipo de recurso, use a função de colaborador para esse recurso. Por exemplo, para aplicar marcas a máquinas virtuais, use o [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Aplicar marcas

O Azure PowerShell oferece dois comandos para aplicar marcas- [New-AzTag](/powershell/module/az.resources/new-aztag) e [Update-AzTag](/powershell/module/az.resources/update-aztag). Você deve ter o módulo AZ. Resources 1.12.0 ou posterior. Você pode verificar sua versão com `Get-Module Az.Resources` . Você pode instalar esse módulo ou [instalar o Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 ou posterior.

O **New-AzTag** substitui todas as marcas no recurso, no grupo de recursos ou na assinatura. Ao chamar o comando, passe a ID de recurso da entidade que você deseja marcar.

O exemplo a seguir aplica um conjunto de marcas a uma conta de armazenamento:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Quando o comando for concluído, observe que o recurso tem duas marcas.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Se você executar o comando novamente, mas desta vez com marcas diferentes, observe que as marcas anteriores são removidas.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Para adicionar marcas a um recurso que já tem marcas, use **Update-AzTag**. Defina o parâmetro **-Operation** para **mesclar**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Observe que as duas novas marcas foram adicionadas às duas marcas existentes.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Cada nome de marca pode ter apenas um valor. Se você fornecer um novo valor para uma marca, o valor antigo será substituído mesmo se você usar a operação de mesclagem. O exemplo a seguir altera a marca de status de normal para verde.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Quando você define o parâmetro **-Operation** como **replace**, as marcas existentes são substituídas pelo novo conjunto de marcas.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Somente as novas marcas permanecem no recurso.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Os mesmos comandos também funcionam com grupos de recursos ou assinaturas. Você passa o identificador do grupo de recursos ou da assinatura que deseja marcar.

Para adicionar um novo conjunto de marcas a um grupo de recursos, use:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Para atualizar as marcas de um grupo de recursos, use:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Para adicionar um novo conjunto de marcas a uma assinatura, use:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Para atualizar as marcas de uma assinatura, use:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

Você pode ter mais de um recurso com o mesmo nome em um grupo de recursos. Nesse caso, você pode definir cada recurso com os seguintes comandos:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Listar marcas

Para obter as marcas de um recurso, grupo de recursos ou assinatura, use o comando [Get-AzTag](/powershell/module/az.resources/get-aztag) e passe a ID do recurso para a entidade.

Para ver as marcas de um recurso, use:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Para ver as marcas de um grupo de recursos, use:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Para ver as marcas de uma assinatura, use:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Listar por marca

Para obter recursos que têm um nome e valor de marca específicos, use:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Para obter recursos que têm um nome de marca específico com qualquer valor de marca, use:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Para obter os grupos de recursos que têm um nome e valor de marca específicos, use:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Remover marcas

Para remover marcas específicas, use **Update-AzTag** e Set **-Operation** para **excluir**. Passe as marcas que você deseja excluir.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

As marcas especificadas são removidas.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Para remover todas as marcas, use o comando [Remove-AzTag](/powershell/module/az.resources/remove-aztag) .

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>CLI do Azure

### <a name="apply-tags"></a>Aplicar marcas

Ao adicionar marcas a um grupo de recursos ou recurso, você pode substituir as marcas existentes ou acrescentar novas marcas a marcas existentes.

Para substituir as marcas em um recurso, use:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para acrescentar uma marca às marcas existentes em um recurso, use:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para substituir as marcas existentes em um grupo de recursos, use:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Para acrescentar uma marca às marcas existentes em um grupo de recursos, use:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Atualmente, CLI do Azure não tem um comando para aplicar marcas a assinaturas. No entanto, você pode usar a CLI para implantar um modelo do ARM que aplica as marcas a uma assinatura. Consulte [aplicar marcas a grupos de recursos ou assinaturas](#apply-tags-to-resource-groups-or-subscriptions).

### <a name="list-tags"></a>Listar marcas

Para ver as marcas existentes de um recurso, use:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Para conferir as marcas existentes para um grupo de recursos, use:

```azurecli-interactive
az group show -n examplegroup --query tags
```

Esse script retorna o seguinte formato:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

### <a name="list-by-tag"></a>Listar por marca

Para obter todos os recursos que tem marca e valor específicos, use `az resource list`:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Para obter grupos de recursos que têm uma marca específica, use `az group list`:

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>Espaços de manuseio

Se os nomes ou valores de marcação incluírem espaços, você deverá executar algumas etapas adicionais. O exemplo a seguir aplica todas as marcas de um grupo de recursos a seus recursos quando as marcas podem conter espaços.

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>Modelos

Você pode marcar recursos, grupos de recursos e assinaturas durante a implantação com um modelo do Resource Manager.

### <a name="apply-values"></a>Aplicar valores

O exemplo a seguir implanta uma conta de armazenamento com três marcas. Duas das marcas ( `Dept` e `Environment` ) são definidas como valores literais. Uma marca ( `LastDeployed` ) é definida como um parâmetro que usa como padrão a data atual.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-an-object"></a>Aplicar um objeto

Você pode definir um parâmetro de objeto que armazena várias marcas e aplicar esse objeto para o elemento de marca. Essa abordagem fornece mais flexibilidade do que o exemplo anterior porque o objeto pode ter propriedades diferentes. Cada propriedade no objeto se torna uma marca separada para o recurso. O exemplo a seguir tem um parâmetro chamado `tagValues` que é aplicado ao elemento de marca.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string"></a>Aplicar uma cadeia de caracteres JSON

Para armazenar diversos valores em uma única marca, aplica uma cadeia de caracteres JSON que representa os valores. A cadeia de caracteres JSON inteira é armazenada como uma marca que não pode exceder 256 caracteres. O exemplo a seguir tem uma única marca denominada `CostCenter` que contém vários valores de uma cadeia de caracteres JSON:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Aplicar marcas do grupo de recursos

Para aplicar marcas de um grupo de recursos a um recurso, use a função [resourcegroup ()](../templates/template-functions-resource.md#resourcegroup) . Ao obter o valor da marca, use a `tags[tag-name]` sintaxe em vez da `tags.tag-name` sintaxe, porque alguns caracteres não são analisados corretamente na notação de ponto.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Aplicar marcas a grupos de recursos ou assinaturas

Você pode adicionar marcas a um grupo de recursos ou assinatura implantando o tipo de recurso **Microsoft. Resources/Tags** . As marcas são aplicadas ao grupo de recursos de destino ou à assinatura para a implantação. Cada vez que você implanta o modelo, você substitui todas as marcas que foram aplicadas anteriormente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Para aplicar as marcas a um grupo de recursos, use o PowerShell ou CLI do Azure. Implante no grupo de recursos que você deseja marcar.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Para aplicar as marcas a uma assinatura, use o PowerShell ou CLI do Azure. Implante na assinatura que você deseja marcar.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Para obter mais informações sobre implantações de assinatura, consulte [criar grupos de recursos e recursos no nível da assinatura](../templates/deploy-to-subscription.md).

O modelo a seguir adiciona as marcas de um objeto a um grupo de recursos ou assinatura.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>API REST

Para trabalhar com marcas por meio da API REST do Azure, use:

* [Marcas – criar ou atualizar no escopo](/rest/api/resources/tags/createorupdateatscope) (operação put)
* [Marcas – atualizar no escopo](/rest/api/resources/tags/updateatscope) (operação de patch)
* [Marcas-obter no escopo](/rest/api/resources/tags/getatscope) (operação get)
* [Marcas – excluir no escopo](/rest/api/resources/tags/deleteatscope) (operação de exclusão)

## <a name="inherit-tags"></a>Herdar marcas

As marcas aplicadas ao grupo de recursos ou à assinatura não são herdadas pelos recursos. Para aplicar marcas de uma assinatura ou grupo de recursos aos recursos, consulte [políticas do Azure – marcas](tag-policies.md).

## <a name="tags-and-billing"></a>Marcas e cobrança

Você pode usar marcas para agrupar os dados de cobrança. Por exemplo, se estiver executando várias VMs para organizações diferentes, use as marcas para agrupar o uso por centro de custo. Você também pode usar marcas para categorizar os custos pelo ambiente de runtime, como por exemplo, o uso de cobrança para VMs em execução no ambiente de produção.

Você pode recuperar informações sobre marcas por meio de [APIs de uso de recursos do Azure e de cartão de taxa](../../cost-management-billing/manage/usage-rate-card-overview.md) ou o arquivo CSV (valores separados por vírgula) de uso. Você baixa o arquivo de uso do portal do Azure. Para saber mais, confira [Baixar ou exibir sua fatura de cobrança e dados de uso diário do Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md). Ao baixar o arquivo de uso do Centro de Contas do Azure, selecione **Versão 2**. Para os serviços que dão suporte a marcas com cobrança, as marcas aparecerão na coluna **Marcas**.

Para operações de API REST, confira [Referência da API REST de cobrança do Azure](/rest/api/billing/).

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam a marcas:

* Nem todos os tipos de recursos suportam tags. Para determinar se você pode aplicar uma tag a um tipo de recurso, consulte [Suporte a tags para recursos do Azure](tag-support.md).
* Cada recurso, grupo de recursos e assinatura podem ter um máximo de 50 pares de nome/valor de marca. Se você precisar aplicar mais marcas do que o número máximo permitido, use uma cadeia de caracteres JSON para o valor da marca. A cadeia de caracteres JSON pode conter diversos valores que são aplicados a um único nome de marca. Um grupo de recursos ou uma assinatura pode conter muitos recursos, cada um com pares de nome/valor de marca 50.
* O nome da marca é limitado a 512 caracteres e o valor da marca é limitado a 256 caracteres. Para contas de armazenamento, o nome da marca é limitado a 128 caracteres e o valor da marca é limitado a 256 caracteres.
* As marcas não podem ser aplicadas a recursos clássicos como Serviços de Nuvem.
* Os nomes das marcas não podem conter esses caracteres: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Atualmente, as zonas DNS do Azure e os serviços do Traffic Manager também não permitem o uso de espaços na marca.
   >
   > A porta frontal do Azure não dá suporte ao uso de `#` no nome da marca.
   >
   > A automação do Azure e a CDN do Azure dão suporte apenas a 15 marcas em recursos.

## <a name="next-steps"></a>Próximas etapas

* Nem todos os tipos de recursos suportam tags. Para determinar se você pode aplicar uma tag a um tipo de recurso, consulte [Suporte a tags para recursos do Azure](tag-support.md).
* Para obter recomendações sobre como implementar uma estratégia de marcação, consulte [nomenclatura de recursos e guia de decisão de marcação](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).
