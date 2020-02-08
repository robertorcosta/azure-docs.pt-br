---
title: Marcar recursos para a organização lógica
description: Mostra como aplicar marcas para organizar os recursos do Azure para cobrança e gerenciamento.
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5751f2d1bc123c5918ae0fabc5b908b5f4fec71d
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087319"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Usar marcações para organizar seus recursos do Azure

Você aplica marcas aos recursos do Azure para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

Depois de aplicar marcas, você pode recuperar todos os recursos em sua assinatura com esse nome e valor de marca. As marcas permitem que você recupere recursos relacionados de diferentes grupos de recursos. Esta abordagem será útil quando você precisar organizar os recursos de gerenciamento ou de cobrança.

Sua taxonomia deve considerar uma estratégia de marcação de metadados de autoatendimento, além de uma estratégia de marcação automática para reduzir a carga sobre os usuários e aumentar a precisão.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam a marcas:

* Nem todos os tipos de recursos suportam tags. Para determinar se você pode aplicar uma tag a um tipo de recurso, consulte [Suporte a tags para recursos do Azure](tag-support.md).
* Cada recurso ou grupo de recursos pode ter um máximo de 50 pares de nome/valor de marca. Se você precisar aplicar mais marcas do que o número máximo permitido, use uma cadeia de caracteres JSON para o valor da marca. A cadeia de caracteres JSON pode conter diversos valores que são aplicados a um único nome de marca. Um grupo de recursos pode conter muitos recursos que têm um número de 50 pares de nome/valor de marca.
* O nome da marca é limitado a 512 caracteres e o valor da marca é limitado a 256 caracteres. Para contas de armazenamento, o nome da marca é limitado a 128 caracteres e o valor da marca é limitado a 256 caracteres.
* As VMs generalizadas não dão suporte a marcas.
* Marcas aplicadas ao grupo de recursos não são herdadas pelos recursos desse grupo de recursos.
* As marcas não podem ser aplicadas a recursos clássicos como Serviços de Nuvem.
* Os nomes das marcas não podem conter esses caracteres: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Atualmente, as zonas DNS do Azure e os serviços do Gerenciador de tráfego também não permitem o uso de espaços na marca. 

## <a name="required-access"></a>Acesso necessário

Para aplicar marcas a recursos, o usuário deve ter acesso de gravação a esse tipo de recurso. Para aplicar marcas a todos os tipos de recursos, use a função [Colaborador](../../role-based-access-control/built-in-roles.md#contributor). Para aplicar marcas a apenas um tipo de recurso, use a função de colaborador para esse recurso. Por exemplo, para aplicar marcas a máquinas virtuais, use o [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="policies"></a>Políticas

Você pode usar o [Azure Policy](../../governance/policy/overview.md) para impor a marcação de regras e convenções. Ao criar uma política, você deve evitar o cenário de recursos implantados em sua assinatura que não são compatíveis com as marcas esperadas para a sua organização. Em vez de aplicar as marcas ou procurar os recursos que não são compatíveis manualmente, você pode criar uma política que aplica automaticamente as marcas necessárias durante a implantação. Agora, as marcas também podem ser aplicadas a recursos existentes com o novo efeito [Modificar](../../governance/policy/concepts/effects.md#modify) e uma [tarefa de correção](../../governance/policy/how-to/remediate-resources.md). A seção a seguir mostra as políticas de exemplo para marcas.

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

Para conferir as marcas existentes para um *grupo de recursos*, use:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

Esse script retorna o seguinte formato:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Para ver as marcas existentes de um *recurso que tem um nome e um grupo de recursos especificados*, use:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Ou, se você tiver a ID de recurso para um recurso, poderá passar essa ID de recurso para obter as marcas.

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Para obter os *grupos de recursos que têm um nome e valor de marca específicos*, use:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "Dept"="Finance" }).ResourceGroupName
```

Para obter *recursos que têm um nome e valor de marca específicos*, use:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "Dept"="Finance"}).Name
```

Para obter *recursos que têm um nome de marca específico*, use:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Ao aplicar marcas a um recurso ou grupo de recursos, você pode substituir as marcas existentes nesse recurso ou grupo de recursos. Portanto, você deve usar uma abordagem diferente com base em se o recurso ou o grupo de recursos tem marcas existentes.

Para adicionar marcas a um *grupo de recursos sem marcas existentes*, use:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ "Dept"="IT"; "Environment"="Test" }
```

Para adicionar marcas a um *grupo de recursos que tem marcas existentes*, recupere as marcas existentes, adicione a nova marca e reaplique as marcas:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Para adicionar marcas a um *recurso sem marcas existentes*, use:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $resource.ResourceId -Force
```

Você pode ter mais de um recurso com o mesmo nome em um grupo de recursos. Nesse caso, você pode definir cada recurso com os seguintes comandos:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Force }
```

Para adicionar marcas a um *grupo de recursos que tem marcas existentes*, use:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$resource.Tags.Add("Status", "Approved")
Set-AzResource -Tag $resource.Tags -ResourceId $resource.ResourceId -Force
```

Para aplicar todas as marcas de um grupo de recursos a seus recursos e *não manter as marcas existentes nos recursos*, use o seguinte script:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
Get-AzResource -ResourceGroupName $group.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $group.Tags -Force }
```

Para aplicar todas as marcas de um grupo de recursos a seus recursos e *manter as marcas existentes nos recursos que não são duplicatas*, use o seguinte script:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Para remover todas as marcas, passe uma tabela de hash vazio:

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>CLI do Azure

Para conferir as marcas existentes para um *grupo de recursos*, use:

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

Ou, para conferir as marcas existentes para um *recurso que tem um nome, tipo e um grupo de recursos especificado*, use:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Ao executar um loop em uma coleção de recursos, talvez você queira exibir o recurso segundo sua ID. Um exemplo completo é exibido posteriormente neste artigo. Para conferir as marcas existentes para um *recurso que tem uma ID de recurso especificada*, use:

```azurecli-interactive
az resource show --id <resource-id> --query tags
```

Para obter grupos de recursos que têm uma marca específica, use `az group list`:

```azurecli-interactive
az group list --tag Dept=IT
```

Para obter todos os recursos que tem marca e valor específicos, use `az resource list`:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Ao adicionar marcas a um grupo de recursos ou recurso, você pode substituir as marcas existentes ou acrescentar novas marcas a marcas existentes.

Para substituir as marcas existentes em um grupo de recursos, use:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Para acrescentar uma marca às marcas existentes em um grupo de recursos, use:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Para substituir as marcas em um recurso, use:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para acrescentar uma marca às marcas existentes em um recurso, use:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Para aplicar todas as marcas de um grupo de recursos a seus recursos e *não manter as marcas existentes nos recursos*, use o seguinte script:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags $tags --id $id
done
```

Para aplicar todas as marcas de um grupo de recursos a seus recursos e *manter as marcas existentes nos recursos*, use o seguinte script:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')

resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  resourcejsontags=$(az resource show --id $id --query tags -o json)
  resourcetags=$(echo $resourcejsontags | tr -d '"{},' | sed 's/: /=/g')
  az resource tag --tags $tags$resourcetags --id $id
done
```

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

Para marcar um recurso durante a implantação, adicione o elemento `tags` ao recurso que você está implantando. Forneça o nome e o valor da marca.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Aplicar um valor literal ao nome da marca

O exemplo a seguir mostra uma conta de armazenamento com duas marcas (`Dept` e `Environment`) que são definidas como valores literais:

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
                "Dept": "Finance",
                "Environment": "Production"
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

Para definir uma marca para um valor DateTime, use a [função UtcNow](../templates/template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Aplicar um objeto ao elemento da marca

Você pode definir um parâmetro de objeto que armazena várias marcas e aplicar esse objeto para o elemento de marca. Cada propriedade no objeto se torna uma marca separada para o recurso. O exemplo a seguir tem um parâmetro chamado `tagValues` que é aplicado ao elemento de marca.

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

### <a name="apply-a-json-string-to-the-tag-name"></a>Aplicar uma cadeia de caracteres JSON ao nome da marca

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

Para aplicar marcas de um grupo de recursos a um recurso, use a função [resourcegroup](../templates/template-functions-resource.md#resourcegroup) . Ao obter o valor da marca, use a sintaxe `tags[tag-name]` em vez da sintaxe `tags.tag-name`, porque alguns caracteres não são analisados corretamente na notação de ponto.

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

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>API REST

O portal do Azure e o PowerShell usam a [API REST do Gerenciador de Recursos](/rest/api/resources/) em segundo plano. Se você precisar integrar a marcação a outro ambiente, você pode obter marcas usando **GET** na ID do recurso e atualizar o conjunto de marcas usando uma chamada de **PATCH**.

## <a name="tags-and-billing"></a>Marcas e cobrança

Você pode usar marcas para agrupar os dados de cobrança. Por exemplo, se estiver executando várias VMs para organizações diferentes, use marcas para agrupar o uso por centro de custo. Você também pode usar marcas para categorizar os custos pelo ambiente de runtime, como por exemplo, o uso de cobrança para VMs em execução no ambiente de produção.

Você pode recuperar as informações sobre as marcações por meio das [APIs RateCard e Uso de Recursos do Azure](../../billing/billing-usage-rate-card-overview.md) ou do arquivo de uso CSV (com valores separados por vírgula). Você baixar o arquivo de uso do [Centro de Contas do Azure](https://account.azure.com/Subscriptions) ou do Portal do Azure. Para saber mais, confira [Baixar ou exibir sua fatura de cobrança e dados de uso diário do Azure](../../billing/billing-download-azure-invoice-daily-usage-date.md). Ao baixar o arquivo de uso do Centro de Contas do Azure, selecione **Versão 2**. Para os serviços que dão suporte a marcas com cobrança, as marcas aparecerão na coluna **Marcas**.

Para operações de API REST, confira [Referência da API REST de cobrança do Azure](/rest/api/billing/).

## <a name="next-steps"></a>Próximas etapas

* Nem todos os tipos de recursos suportam tags. Para determinar se você pode aplicar uma tag a um tipo de recurso, consulte [Suporte a tags para recursos do Azure](tag-support.md).
* Para obter uma introdução ao uso do portal, confira [Usando o portal do Azure para gerenciar os recursos do Azure](manage-resource-groups-portal.md).  
