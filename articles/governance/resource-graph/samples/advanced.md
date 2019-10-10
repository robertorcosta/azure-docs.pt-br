---
title: Exemplos de consulta avançada
description: Use o Azure Resource Graph para executar algumas consultas avançadas, incluindo a capacidade do conjunto de dimensionamento de máquinas virtuais, listagem de todas as marcas usadas e as máquinas virtuais correspondentes com expressões regulares.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 668dfc908418792c1258d112fbdfb640e85abbe2
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980290"
---
# <a name="advanced-resource-graph-queries"></a>Consultas do Microsoft Azure Active Directory Graph

A primeira etapa para consultas de reconhecimento com o Microsoft Azure Resource Graph é uma compreensão básica da [Linguagem de Consulta](../concepts/query-language.md). Se você já não estiver familiarizado com o [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md), é recomendável revisar os conceitos básicos para entender como compor solicitações para os recursos que está procurando.

Vamos percorrer as seguintes consultas avançadas:

> [!div class="checklist"]
> - [Obter capacidade e tamanho do conjunto de dimensionamento de máquina virtual](#vmss-capacity)
> - [Listar todos os nomes de marca](#list-all-tags)
> - [Máquinas virtuais correspondidas por regex](#vm-regex)
> - [Incluir os nomes de locatário e assinatura com DisplayNames](#displaynames)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="language-support"></a>Suporte ao idioma

A CLI do Azure (por meio de uma extensão) e o Azure PowerShell (por meio de um módulo) suportam o Gráfico de Recursos do Azure. Antes de executar qualquer uma das consultas a seguir, verifique se seu ambiente está preparado. Veja [CLI do Azure](../first-query-azurecli.md#add-the-resource-graph-extension) e [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) para obter as etapas instalar e validar o ambiente shell de escolha.

## <a name="vmss-capacity"></a>Obter capacidade e tamanho do conjunto de dimensionamento de máquina virtual

Essa consulta procura por recursos VMSS e obtém os diversos detalhes, incluindo o tamanho da máquina virtual e a capacidade do conjunto de dimensionamento. A consulta usa a função `toint()` para converter a capacidade em um número para que ela possa ser classificada. Por fim, as colunas são renomeadas para as propriedades nomeadas personalizadas.

```kusto
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>Listar todos os nomes de marca

Essa consulta começa com a marca e cria um objeto JSON listando todos os nomes de marca exclusivos e seus tipos correspondentes.

```kusto
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Máquinas virtuais correspondidas por regex

Essa consulta procura por máquinas virtuais que correspondem a um [expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference) (conhecida como _regex_). O **corresponde ao regex \@** nos permite definir o regex para correspondência, que é `^Contoso(.*)[0-9]+$`.
Essa definição de regex é explicada como:

- `^` - a correspondência deve começar no início da cadeia de caracteres.
- `Contoso` - a cadeia de caracteres com diferenciação de maiúsculas e minúsculas.
- `(.*)` - uma correspondência de subexpressão:
  - `.` - corresponde a qualquer caractere único (exceto uma nova linha).
  - `*` - corresponde ao elemento anterior nenhuma ou mais vezes.
- `[0-9]` - correspondência de grupo de caracteres para os números de 0 a 9.
- `+` - corresponde ao elemento anterior uma ou mais vezes.
- `$` - a correspondência do elemento anterior deve ocorrer no final da cadeia de caracteres.

Após a correspondência por nome, a consulta projeta o nome e ordena por nome em ordem crescente.

```kusto
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="displaynames"></a>Incluir os nomes de locatário e assinatura com DisplayNames

Essa consulta usa o novo parâmetro **Include** com a opção _DisplayNames_ para adicionar **subscriptionDisplayName** e **tenantDisplayName** aos resultados. Esse parâmetro está disponível apenas para a CLI do Azure e o Azure PowerShell.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

> [!NOTE]
> Se a consulta não usar **project** para especificar as propriedades retornadas, **subscriptionDisplayName** e **tenantDisplayName** serão incluídos automaticamente nos resultados.
> Se a consulta usar **project**, cada um dos campos _DisplayName_ deverá ser incluído explicitamente no **project** ou eles não serão retornados nos resultados, mesmo quando o parâmetro **Include** for usado.

## <a name="next-steps"></a>Próximas etapas

- Ver exemplos de [Consultas iniciais](starter.md)
- Saiba mais sobre a [linguagem de consulta](../concepts/query-language.md)
- Saiba [explorar recursos](../concepts/explore-resources.md)