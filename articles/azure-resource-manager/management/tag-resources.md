---
title: Marque recursos, grupos de recursos e assinaturas para organização lógica
description: Mostra como aplicar marcas para organizar os recursos do Azure para cobrança e gerenciamento.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: ffc97df0923e26c3abf0eed8e7810f3b1dc61ed2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132182"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Use tags para organizar seus recursos e hierarquia de gerenciamento do Azure

Você aplica tags aos seus recursos do Azure, grupos de recursos e assinaturas para organizá-las logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

Para obter recomendações sobre como implementar uma estratégia de marcação, consulte [o guia de decisão de nomeação e marcação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Acesso necessário

Para aplicar tags a um recurso, você deve ter acesso à gravação do tipo de recurso **Microsoft.Resources/tags.** A **função Contribuinte de marca** permite que você aplique tags a uma entidade sem ter acesso à própria entidade.

A função [contribuinte](../../role-based-access-control/built-in-roles.md#contributor) também concede o acesso necessário para aplicar tags a qualquer entidade. Para aplicar marcas a apenas um tipo de recurso, use a função de colaborador para esse recurso. Por exemplo, para aplicar marcas a máquinas virtuais, use o [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Aplicar tags

O Azure PowerShell oferece dois comandos para a aplicação de tags - [New-AzTag](/powershell/module/az.resources/new-aztag) e [Update-AzTag](/powershell/module/az.resources/update-aztag). Você deve ter o Azure PowerShell 3.6.1 ou posterior para usar esses comandos.

O **New-AzTag** substitui todas as tags no recurso, grupo de recursos ou assinatura. Ao chamar o comando, passe no ID de recurso da entidade que deseja marcar.

O exemplo a seguir aplica um conjunto de tags em uma conta de armazenamento:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Quando o comando for concluído, observe que o recurso tem duas tags.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Se você executar o comando novamente, mas desta vez com tags diferentes, observe que as tags anteriores serão removidas.

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

Para adicionar tags a um recurso que já possui tags, use **Update-AzTag**. Defina o parâmetro **-Operação** para **Mesclar**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Observe que as duas novas tags foram adicionadas às duas tags existentes.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Cada nome de tag pode ter apenas um valor. Se você fornecer um novo valor para uma tag, o valor antigo será substituído mesmo se você usar a operação de mesclagem. O exemplo a seguir altera a tag Status de Normal para Verde.

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

Quando você define o parâmetro **-Operação** para **substituir,** as tags existentes são substituídas pelo novo conjunto de tags.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Apenas as novas tags permanecem no recurso.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Os mesmos comandos também funcionam com grupos de recursos ou assinaturas. Você passa no identificador para o grupo de recursos ou assinatura que deseja marcar.

Para adicionar um novo conjunto de tags a um grupo de recursos, use:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Para atualizar as tags de um grupo de recursos, use:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Para adicionar um novo conjunto de tags a uma assinatura, use:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Para atualizar as tags para uma assinatura, use:

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

Para obter as tags de um recurso, grupo de recursos ou assinatura, use o comando [Get-AzTag](/powershell/module/az.resources/get-aztag) e passe no ID de recurso para a entidade.

Para ver as tags de um recurso, use:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Para ver as tags de um grupo de recursos, use:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Para ver as tags de uma assinatura, use:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Lista por tag

Para obter recursos que tenham um nome e valor específicos da tag, use:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Para obter recursos que tenham um nome de tag específico com qualquer valor de tag, use:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Para obter grupos de recursos que tenham um nome e valor específicos da tag, use:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Remover tags

Para remover tags específicas, use **Update-AzTag** e set **-Operation** to **Delete**. Passe nas tags que deseja excluir.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

As tags especificadas são removidas.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Para remover todas as tags, use o comando [Remove-AzTag.](/powershell/module/az.resources/remove-aztag)

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>CLI do Azure

### <a name="apply-tags"></a>Aplicar tags

Ao adicionar tags a um grupo de recursos ou recurso, você pode substituir as tags existentes ou anexar novas tags às tags existentes.

Para substituir as tags em um recurso, use:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para anexar uma tag às tags existentes em um recurso, use:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para substituir as tags existentes em um grupo de recursos, use:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Para anexar uma tag às tags existentes em um grupo de recursos, use:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Atualmente, o Azure CLI não suporta a aplicação de tags em assinaturas.

### <a name="list-tags"></a>Listar marcas

Para ver as tags existentes para um recurso, use:

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

### <a name="list-by-tag"></a>Lista por tag

Para obter todos os recursos que tem marca e valor específicos, use `az resource list`:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Para obter grupos de recursos que têm uma marca específica, use `az group list`:

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>Movimentação de espaços

Se os nomes ou valores da sua tag incluem espaços, você deve dar alguns passos extras. O exemplo a seguir aplica todas as tags de um grupo de recursos aos seus recursos quando as tags podem conter espaços.

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

Você pode marcar recursos, grupos de recursos e assinaturas durante a implantação com um modelo do Gerenciador de recursos.

### <a name="apply-values"></a>Aplicar valores

O exemplo a seguir implanta uma conta de armazenamento com três tags. Duas das tags`Dept` `Environment`( e ) são definidas como valores literais. Uma tag`LastDeployed`( ) é definida como um parâmetro que é padrão para a data atual.

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

### <a name="apply-an-object"></a>Aplique um objeto

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

### <a name="apply-a-json-string"></a>Aplique uma seqüência JSON

Para armazenar diversos valores em uma única marca, aplica uma cadeia de caracteres JSON que representa os valores. Toda a seqüência JSON é armazenada como uma tag que não pode exceder 256 caracteres. O exemplo a seguir tem uma única marca denominada `CostCenter` que contém vários valores de uma cadeia de caracteres JSON:  

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

### <a name="apply-tags-from-resource-group"></a>Aplicar tags do grupo de recursos

Para aplicar tags de um grupo de recursos a um recurso, use a função [resourceGroup.](../templates/template-functions-resource.md#resourcegroup) Ao obter o valor `tags[tag-name]` da tag, use `tags.tag-name` a sintaxe em vez da sintaxe, porque alguns caracteres não são analisados corretamente na notação de nota.

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

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Aplicar tags a grupos de recursos ou assinaturas

Você pode adicionar tags a um grupo de recursos ou assinatura implantando o tipo de recurso **Microsoft.Resources/tags.** As tags são aplicadas ao grupo de recursos de destino ou assinatura para a implantação. Cada vez que você implanta o modelo, você substitui quaisquer tags que foram aplicadas anteriormente.

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

Para aplicar as tags a um grupo de recursos, use powershell ou Cli do Azure. Implantar no grupo de recursos que você deseja marcar.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Para aplicar as tags a uma assinatura, use powershell ou Azure CLI. Implante na assinatura que você deseja marcar.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

O modelo a seguir adiciona as tags de um objeto a um grupo de recursos ou assinatura.

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

Para trabalhar com tags através da API Azure REST, use:

* [Tags - Criar ou Atualizar no Escopo](/rest/api/resources/tags/createorupdateatscope) (operação PUT)
* [Tags - Atualização no escopo](/rest/api/resources/tags/updateatscope) (operação PATCH)
* [Tags - Get At Scope](/rest/api/resources/tags/getatscope) (operação GET)
* [Tags - Excluir no escopo](/rest/api/resources/tags/deleteatscope) (operação DELETE)

## <a name="inherit-tags"></a>Etiquetas herdar

As tags aplicadas ao grupo de recursos ou assinatura não são herdadas pelos recursos. Para aplicar tags de um grupo de assinatura ou recurso aos recursos, consulte [Azure Policies - tags](tag-policies.md).

## <a name="tags-and-billing"></a>Marcas e cobrança

Você pode usar marcas para agrupar os dados de cobrança. Por exemplo, se estiver executando várias VMs para organizações diferentes, use marcas para agrupar o uso por centro de custo. Você também pode usar marcas para categorizar os custos pelo ambiente de runtime, como por exemplo, o uso de cobrança para VMs em execução no ambiente de produção.

Você pode recuperar as informações sobre as marcações por meio das [APIs RateCard e Uso de Recursos do Azure](../../billing/billing-usage-rate-card-overview.md) ou do arquivo de uso CSV (com valores separados por vírgula). Você baixar o arquivo de uso do [Centro de Contas do Azure](https://account.azure.com/Subscriptions) ou do Portal do Azure. Para saber mais, confira [Baixar ou exibir sua fatura de cobrança e dados de uso diário do Azure](../../billing/billing-download-azure-invoice-daily-usage-date.md). Ao baixar o arquivo de uso do Centro de Contas do Azure, selecione **Versão 2**. Para os serviços que dão suporte a marcas com cobrança, as marcas aparecerão na coluna **Marcas**.

Para operações de API REST, confira [Referência da API REST de cobrança do Azure](/rest/api/billing/).

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam a marcas:

* Nem todos os tipos de recursos suportam tags. Para determinar se você pode aplicar uma tag a um tipo de recurso, consulte [Suporte a tags para recursos do Azure](tag-support.md).
* Atualmente, os grupos de gerenciamento não suportam tags.
* Cada recurso, grupo de recursos e assinatura pode ter um máximo de 50 pares de nomes/valor de tag. Se você precisar aplicar mais tags do que o número máximo permitido, use uma seqüência JSON para o valor da tag. A cadeia de caracteres JSON pode conter diversos valores que são aplicados a um único nome de marca. Um grupo de recursos ou assinatura pode conter muitos recursos que cada um tem 50 pares de nomes/valor de tag.
* O nome da marca é limitado a 512 caracteres e o valor da marca é limitado a 256 caracteres. Para contas de armazenamento, o nome da marca é limitado a 128 caracteres e o valor da marca é limitado a 256 caracteres.
* VMs generalizadas não suportam tags.
* As marcas não podem ser aplicadas a recursos clássicos como Serviços de Nuvem.
* Os nomes das marcas não podem conter esses caracteres: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Atualmente, as zonas De DNS do Azure e os serviços de Traffic Manger também não permitem o uso de espaços na tag.

## <a name="next-steps"></a>Próximas etapas

* Nem todos os tipos de recursos suportam tags. Para determinar se você pode aplicar uma tag a um tipo de recurso, consulte [Suporte a tags para recursos do Azure](tag-support.md).
* Para obter recomendações sobre como implementar uma estratégia de marcação, consulte [o guia de decisão de nomeação e marcação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).
