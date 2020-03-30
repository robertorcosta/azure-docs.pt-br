---
title: Scripts PowerShell usando o módulo Az.Search
titleSuffix: Azure Cognitive Search
description: Crie e configure um serviço de pesquisa cognitiva do Azure com o PowerShell. Você pode dimensionar um serviço para cima ou para baixo, gerenciar administradores e consultar api-keys e consultar informações do sistema.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209289"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Gerencie seu serviço de pesquisa cognitiva do Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [Powershell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Você pode executar cmdlets e scripts do PowerShell no Windows, Linux ou no [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) para criar e configurar o Azure Cognitive Search. O **módulo Az.Search** estende [o Azure PowerShell](https://docs.microsoft.com/powershell/) com total paridade para as [APIs de Gerenciamento de Pesquisa REST](https://docs.microsoft.com/rest/api/searchmanagement) e a capacidade de executar as seguintes tarefas:

> [!div class="checklist"]
> * [Listar serviços de pesquisa em uma assinatura](#list-search-services)
> * [Informações do serviço de devolução](#get-search-service-information)
> * [Criar ou excluir um serviço](#create-or-delete-a-service)
> * [Regenerar chaves aPI de admin](#regenerate-admin-keys)
> * [Criar ou excluir as teclas de consulta](#create-or-delete-query-keys)
> * [Dimensione para cima ou para baixo com réplicas e partições](#scale-replicas-and-partitions)

Ocasionalmente, são feitas perguntas sobre tarefas *que não* estão na lista acima. Atualmente, você não pode usar o módulo **Az.Search** ou a API REST de gerenciamento para alterar um nome, região ou nível de servidor. Os recursos dedicados são alocados quando um serviço é criado. Como tal, alterar o hardware subjacente (tipo de localização ou nó) requer um novo serviço. Da mesma forma, não há ferramentas ou APIs para transferir conteúdo, como um índice, de um serviço para outro.

Dentro de um serviço, a criação e o gerenciamento de conteúdo é através da [API rest do Serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/) ou do [.NET SDK](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search). Embora não haja comandos PowerShell dedicados para conteúdo, você pode escrever o script PowerShell que chama DE REST ou .NET APIs para criar e carregar índices.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Verificar versões e módulos de carga

Os exemplos deste artigo são interativos e exigem permissões elevadas. O Azure PowerShell (o módulo **Az)** deve ser instalado. Para obter mais informações, consulte [Instalar o Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Verificação da versão powershell (5.1 ou posterior)

O PowerShell local deve ser 5.1 ou posterior, em qualquer sistema operacional suportado.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Carregar Azure PowerShell

Se você não tiver certeza se **o Az** está instalado, execute o seguinte comando como uma etapa de verificação. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Alguns sistemas não carregam automaticamente módulos. Se você tiver um erro no comando anterior, tente carregar o módulo e, se isso falhar, volte às instruções de instalação para ver se você perdeu um passo.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Conecte-se ao Azure com um token de login do navegador

Você pode usar as credenciais de login do portal para se conectar a uma assinatura no PowerShell. Alternativamente, você pode [autenticar não interativamente com um diretor de serviço](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Se você tiver várias assinaturas do Azure, defina sua assinatura do Azure. Para ver uma lista de suas assinaturas atuais, execute este comando.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Para especificar a assinatura, execute o comando a seguir. No exemplo a seguir, o nome da assinatura é `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Listar serviços em uma assinatura

Os seguintes comandos são do [**Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), devolvendo informações sobre recursos e serviços existentes já provisionados em sua assinatura. Se você não sabe quantos serviços de pesquisa já estão criados, esses comandos retornam essas informações, poupando-lhe uma viagem para o portal.

O primeiro comando retorna todos os serviços de busca.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Na lista de serviços, retorne informações sobre um recurso específico.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Os resultados devem ser semelhantes à saída a seguir.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importar Az.Search

Os comandos do [**Az.Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) não estão disponíveis até que você carregue o módulo.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Liste todos os comandos Az.Search

Como etapa de verificação, retorne uma lista de comandos fornecidos no módulo.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Os resultados devem ser semelhantes à saída a seguir.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Obtenha informações do serviço de pesquisa

Depois **que o Az.Search** for importado e você conhecer o grupo de recursos que contém seu serviço de pesquisa, execute [get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) para retornar a definição do serviço, incluindo nome, região, nível e contagem de réplicas e partições.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Os resultados devem ser semelhantes à saída a seguir.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Criar ou excluir um serviço

[**O New-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) é usado para [criar um novo serviço de pesquisa.](search-create-service-portal.md)

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Os resultados devem ser semelhantes à saída a seguir.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Regenerar chaves de administrador

[**New-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) é usado para rolar [as teclas aRP](search-security-api-keys.md). Duas teclas de admin são criadas com cada serviço para acesso autenticado. As chaves são necessárias em cada pedido. Ambas as teclas de admin são funcionalmente equivalentes, concedendo acesso total à gravação a um serviço de pesquisa com a capacidade de recuperar qualquer informação ou criar e excluir qualquer objeto. Existem duas teclas para que você possa usar uma enquanto substitui a outra. 

Você só pode regenerar um de cada `primary` vez, especificado como ou a `secondary` chave. Para um serviço ininterrupto, lembre-se de atualizar todo o código do cliente para usar uma chave secundária enquanto rola a chave principal. Evite trocar as chaves enquanto as operações estiverem em vôo.

Como você pode esperar, se você regenerar chaves sem atualizar o código do cliente, as solicitações usando a chave antiga falharão. Regenerar todas as novas chaves não o bloqueia permanentemente do seu serviço, e você ainda pode acessar o serviço através do portal. Depois de regenerar chaves primárias e secundárias, você pode atualizar o código do cliente para usar as novas chaves e as operações serão retomadas de acordo.

Os valores para as chaves de API são gerados pelo serviço. Você não pode fornecer uma chave personalizada para o Azure Cognitive Search usar. Da mesma forma, não há um nome definido pelo usuário para admin API-keys. As referências à chave são `primary` strings fixas, ou `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Os resultados devem ser semelhantes à saída a seguir. Ambas as chaves são devolvidas mesmo que você só mude uma de cada vez.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Criar ou excluir chaves de consulta

[**O New-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) é usado para criar [chaves de API](search-security-api-keys.md) de consulta para acesso somente à leitura de aplicativos clientes a um índice de pesquisa cognitiva do Azure. As teclas de consulta são usadas para autenticar um índice específico com o objetivo de recuperar resultados de pesquisa. As chaves de consulta não concedem acesso somente à leitura a outros itens do serviço, como um índice, fonte de dados ou indexador.

Você não pode fornecer uma chave para o Azure Cognitive Search usar. As chaves de API são geradas pelo serviço.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Dimensionar réplicas e partições

[**O Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) é usado para [aumentar ou diminuir réplicas e partições](search-capacity-planning.md) para reajustar recursos faturados dentro do seu serviço. O aumento de réplicas ou partições adiciona à sua conta, que tem taxas fixas e variáveis. Se você tiver uma necessidade temporária de poder de processamento adicional, você pode aumentar réplicas e partições para lidar com a carga de trabalho. A área de monitoramento na página do portal Visão Geral tem telhas na latência da consulta, consultas por segundo e estrangulamento, indicando se a capacidade atual é adequada.

Pode levar um tempo para adicionar ou remover o resourcing. Os ajustes na capacidade ocorrem em segundo plano, permitindo que as cargas de trabalho existentes continuem. A capacidade adicional é usada para solicitações recebidas assim que estiver pronta, sem necessidade de configuração adicional. 

Remover a capacidade pode ser perturbador. É recomendável interromper todos os trabalhos de indexação e indexador antes de reduzir a capacidade para evitar pedidos de queda. Se isso não for viável, você pode considerar reduzir a capacidade incrementalmente, uma réplica e partição por vez, até que seus novos níveis de destino sejam atingidos.

Uma vez que você enviar o comando, não há nenhuma maneira de terminá-lo no meio do caminho. Você terá que esperar até que o comando seja concluído antes de revisar as contagens.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Os resultados devem ser semelhantes à saída a seguir.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="next-steps"></a>Próximas etapas

Construa um [índice,](search-what-is-an-index.md) [consulta um índice](search-query-overview.md) usando o portal, ASIS REST ou o SDK .NET.

* [Criar um índice da Pesquisa Cognitiva do Azure no portal do Azure](search-create-index-portal.md)
* [Configure um indexador para carregar dados de outros serviços](search-indexer-overview.md)
* [Consultar um índice de pesquisa cognitiva do Azure usando o search explorer no portal Azure](search-explorer.md)
* [Como usar a Pesquisa Cognitiva do Azure em .NET](search-howto-dotnet-sdk.md)