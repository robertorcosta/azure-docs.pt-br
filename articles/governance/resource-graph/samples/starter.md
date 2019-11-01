---
title: Exemplos de consulta inicial
description: Use o Azure Resource Graph para executar algumas consultas iniciais, incluindo contagem de recursos, ordenação de recursos ou por uma marca específica.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/21/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: c2a8c60502aeb75173371d40475b5d2875417791
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808627"
---
# <a name="starter-resource-graph-queries"></a>Consultas do Microsoft Azure Active Directory Graph

A primeira etapa para consultas de reconhecimento com o Microsoft Azure Resource Graph é uma compreensão básica da [Linguagem de Consulta](../concepts/query-language.md). Se você ainda não estiver familiarizado com o [KQL (Kusto Query Language)](/azure/kusto/query/index), é recomendável examinar o [tutorial do KQL](/azure/kusto/query/tutorial) para entender como compor solicitações para os recursos que você está procurando.

Vamos percorrer as seguintes consultas iniciais:

> [!div class="checklist"]
> - [Recursos do Count Azure](#count-resources)
> - [Contagem de recursos do cofre de chaves](#count-keyvaults)
> - [Listar recursos classificados por nome](#list-resources)
> - [Mostrar todas as máquinas virtuais, ordenadas por nome em ordem decrescente](#show-vms)
> - [Mostrar as primeiras cinco máquinas virtuais por nome e tipo do sistema operacional](#show-sorted)
> - [Contagem de máquinas virtuais por tipo de sistema operacional](#count-os)
> - [Mostrar recursos que contêm o armazenamento](#show-storage)
> - [Listar todos os endereços de IP](#list-publicip)
> - [Recursos de contagem que têm endereços IP configurados por assinatura](#count-resources-by-ip)
> - [Listar de recursos com um valor de marca específica](#list-tag)
> - [Listar todas as contas de armazenamento com o valor de marca específica](#list-specific-tag)
> - [Mostrar aliases para um recurso de máquina virtual](#show-aliases)
> - [Mostrar valores distintos para um alias específico](#distinct-alias-values)
> - [Mostrar grupos de segurança de rede não associados](#unassociated-nsgs)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="language-support"></a>Suporte ao idioma

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Gráfico de Recursos do Azure. Antes de executar qualquer uma das consultas a seguir, verifique se seu ambiente está preparado. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter as etapas instalar e validar o ambiente shell de escolha.

## <a name="a-namecount-resources-count-azure-resources"></a><a name="count-resources" />Contar recursos do Azure

Essa consulta retorna o número de recursos do Azure que existem nas assinaturas que você tem acesso. Também é uma boa consulta para validar se o shell de escolha tem os componentes apropriados do Azure Resource Graph instalados e em funcionamento.

```kusto
Resources
| summarize count()
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

## <a name="a-namecount-keyvaults-count-key-vault-resources"></a><a name="count-keyvaults" />Contagem de recursos do cofre de chaves

Essa consulta usa `count` em vez de `summarize` para contar o número de registros retornados. Somente os cofres de chaves são incluídos na contagem.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

## <a name="a-namelist-resources-list-resources-sorted-by-name"></a><a name="list-resources" />Listar recursos classificados por nome

Essa consulta retorna todo tipo de recurso, mas apenas as propriedades **name**, **type** e **location**. Ele usa `order by` para classificar as propriedades pela propriedade **name** em ordem crescente (`asc`).

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

## <a name="a-nameshow-vms-show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms" />Mostrar todas as máquinas virtuais ordenadas por nome em ordem decrescente

Para listar somente as máquinas virtuais (que são do tipo `Microsoft.Compute/virtualMachines`), podemos fazer a correspondência com a propriedade **type** nos resultados. Semelhante à consulta anterior, `desc` alterações a `order by` devem estar em ordem decrescente. O `=~` no tipo de correspondência informa ao Microsoft Azure Active Directory Graph para diferenciar maiusculas e minúsculas.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

## <a name="a-nameshow-sorted-show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted" />Mostrar as cinco primeiras máquinas virtuais por nome e tipo de sistema operacional

Essa consulta usará `top` para recuperar apenas os cinco registros correspondentes que são ordenados pelo nome. O tipo de recurso do Azure é `Microsoft.Compute/virtualMachines`. `project` informa quais propriedades do Gráfico de Recurso do Azure incluir.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

## <a name="a-namecount-os-count-virtual-machines-by-os-type"></a><a name="count-os" />Contar máquinas virtuais por tipo de sistema operacional

Aproveitando a consulta anterior, estamos ainda limitando pelos recursos do Azure do tipo `Microsoft.Compute/virtualMachines`, mas não limitando o número de registros retornados.
Em vez disso, usamos `summarize` e `count()` para definir como agrupar e agregar os valores de propriedade, que neste exemplo é `properties.storageProfile.osDisk.osType`. Para obter um exemplo da aparência dessa cadeia de caracteres no objeto completo, consulte [Explorar recursos - descoberta de máquina virtual](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

Outra maneira de escrever a mesma consulta é `extend` uma propriedade e dê a ele um nome temporário para uso dentro da consulta, nesse caso **sistema operacional**. **Sistema Operacional** em seguida, é usado pelo `summarize` e `count()` do exemplo anterior.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

> [!NOTE]
> Lembre-se que, embora `=~` permite o uso de correspondência, diferencia maiusculas de minúsculas de propriedades (como **properties.storageProfile.osDisk.osType**) na consulta exigem que o caso esteja correto. Se a propriedade for o caso incorreto, ele ainda pode retornar um valor, mas o agrupamento ou resumo seria incorreto.

## <a name="a-nameshow-storage-show-resources-that-contain-storage"></a><a name="show-storage" />Mostrar recursos que contêm armazenamento

Em vez de definir explicitamente o tipo a ser correspondido, este exemplo de consulta encontrará qualquer recurso do Azure que `contains` a palavra **armazenamento**.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

## <a name="a-namelist-publicip-list-all-public-ip-addresses"></a><a name="list-publicip" />Listar todos os endereços IP públicos

Semelhante à consulta anterior, encontre tudo o que seja um tipo que contenha a palavra **publicIPAddresses**.
Essa consulta expande esse padrão para incluir resultados em que **properties.ipAddress**
`isnotempty`, retornar somente **properties.ipAddress** e `limit` os resultados aos principais
100. Você talvez precise escapar as cotas dependendo do seu shell escolhido.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

## <a name="a-namecount-resources-by-ip-count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip" />Contar recursos que têm endereços IP configurados por assinatura

Usando a consulta de exemplo anterior e adicionando `summarize` e `count()`, obtemos uma lista por assinatura de recursos com endereços IP configurados.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

## <a name="a-namelist-tag-list-resources-with-a-specific-tag-value"></a><a name="list-tag" />Listar recursos com um valor de marca específico

É possível limitar os resultados por propriedades que não seja o tipo de recurso do Azure, como uma marca. Neste exemplo, estamos filtrando por recursos do Azure com o nome de marca de **Ambiente** que tenha um valor de **Interno**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

Para fornecer também quais marcas o recurso tem e seus valores, adicione a propriedade **tags** à palavra-chave `project`.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

## <a name="a-namelist-specific-tag-list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag" />Listar todas as contas de armazenamento com um valor de marca específico

Combine a funcionalidade de filtro do exemplo anterior e filtre o tipo de recurso do Azure pela propriedade **type**. Essa consulta também limita a nossa pesquisa por tipos específicos de recursos do Azure com um valor e nome de marca específicos.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

> [!NOTE]
> Este exemplo usa `==` para a correspondência em vez do `=~` condicional. `==` é uma correspondência que diferencia maiusculas de minúsculas.

## <a name="a-nameshow-aliases-show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases" />Mostrar aliases para um recurso de máquina virtual

[Aliases do Azure Policy](../../policy/concepts/definition-structure.md#aliases) são usados pelo Azure Policy para gerenciar a conformidade do recurso. O Azure Resource Graph pode retornar os _aliases_ de um tipo de recurso. Esses valores são úteis para comparar o valor atual dos aliases ao criar uma definição de política personalizada. A matriz de _aliases_ não é fornecida por padrão nos resultados de uma consulta. Use `project aliases` para adicioná-la explicitamente aos resultados.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

## <a name="a-namedistinct-alias-values-show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values" />Mostrar valores distintos para um alias específico

Ver o valor de aliases em um único recurso é útil, mas não mostra o valor real de usar o Azure Resource Graph para fazer consultas entre assinaturas. Este exemplo examina todos os valores de um alias específico e retorna os valores distintos.

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

## <a name="a-nameunassociated-nsgs-show-unassociated-network-security-groups"></a><a name="unassociated-nsgs" />Mostrar grupos de segurança de rede não associados

Essa consulta retorna NSGs (grupos de segurança de rede) que não estão associados a uma sub-rede ou adaptador de rede.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Ícone do Explorador do Resource Graph](../media/resource-graph-small.png) Experimente usar esta consulta no Explorador do Azure Resource Graph:

- Portal do Azure: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![ícone Abrir link em uma nova janela](../../media/new-window.png)

---

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [linguagem de consulta](../concepts/query-language.md)
- Saiba [explorar recursos](../concepts/explore-resources.md)
- Veja exemplos de [Consultas avançadas](advanced.md)
