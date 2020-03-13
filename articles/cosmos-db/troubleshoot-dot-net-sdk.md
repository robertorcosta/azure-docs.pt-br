---
title: Diagnosticar e solucionar problemas ao usar o SDK do .NET Azure Cosmos DB
description: Use recursos como registro em log do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas de Azure Cosmos DB ao usar o SDK do .NET.
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137947"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticar e solucionar problemas ao usar o SDK do .NET Azure Cosmos DB
Este artigo aborda problemas comuns, soluções alternativas, etapas de diagnóstico e ferramentas quando você usa o [SDK do .net](sql-api-sdk-dotnet.md) com Azure Cosmos DB contas da API do SQL.
O SDK do .NET fornece a representação lógica do lado do cliente para acessar a API do SQL do Azure Cosmos DB. Este artigo descreve as ferramentas e as abordagens para ajudá-lo se você tiver algum problema.

## <a name="checklist-for-troubleshooting-issues"></a>Lista de verificação para solucionar problemas:
Considere a seguinte lista de verificação antes de mover seu aplicativo para produção. O uso da lista de verificação impedirá vários problemas comuns que você possa ver. Você também pode diagnosticar rapidamente quando ocorrer um problema:

*    Use o [SDK](sql-api-sdk-dotnet-standard.md)mais recente. Os SDKs de visualização não devem ser usados para produção. Isso evitará o pressionamento de problemas conhecidos que já foram corrigidos.
*    Examine [dicas de desempenho](performance-tips.md) e siga as práticas sugeridas. Isso ajudará a evitar o dimensionamento, latência e outros problemas de desempenho.
*    Habilite o log do SDK para ajudá-lo a solucionar um problema. Habilitar o registro em log pode afetar o desempenho para que seja melhor habilitá-lo somente ao solucionar problemas. Você pode habilitar os seguintes logs:
    *    [Métricas de log](monitor-accounts.md) usando o portal do Azure. As métricas do portal mostram a telemetria do Azure Cosmos DB, o que é útil para determinar se o problema corresponde a Azure Cosmos DB ou se ele é do lado do cliente.
    *    Registre a [cadeia de caracteres de diagnóstico](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) no SDK v2 ou [diagnóstico](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) no SDK v3 das respostas de operação de ponto.
    *    Registrar as [métricas de consulta SQL](sql-api-query-metrics.md) de todas as respostas de consulta 
    *    Siga a configuração para o [log do SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Dê uma olhada na seção [Problemas comuns e soluções alternativas](#common-issues-workarounds) neste artigo.

Verifique a [seção de problemas do GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) monitorado ativamente. Verifique se você encontrar algum problema semelhante com uma solução alternativa já arquivada. Se você não encontrar uma solução, então, execute um problema do GitHub. Você pode abrir um tique de suporte para problemas urgentes.


## <a name="common-issues-workarounds"></a>Problemas comuns e soluções alternativas

### <a name="general-suggestions"></a>Sugestões gerais
* Execute seu aplicativo na mesma região do Azure que sua conta de Azure Cosmos DB, sempre que possível. 
* Você pode encontrar problemas de conectividade/disponibilidade devido à falta de recursos no computador cliente. É recomendável monitorar a utilização da CPU em nós que executam o cliente Azure Cosmos DB e escalar verticalmente se eles estiverem sendo executados com alta carga.

### <a name="check-the-portal-metrics"></a>Verificar as métricas do portal
Verificar as [métricas do portal](monitor-accounts.md) ajudará a determinar se é um problema do lado do cliente ou se há um problema com o serviço. Por exemplo, se as métricas contiverem uma alta taxa de solicitações limitadas por taxa (código de status HTTP 429), o que significa que a solicitação está sendo limitada e, em seguida, verifique a seção [taxa de solicitação muito grande] . 

### <a name="request-timeouts"></a>Tempos limite de solicitações
RequestTimeout geralmente ocorre ao usar Direct/TCP, mas pode acontecer no modo de gateway. Esses erros são as causas conhecidas comuns e as sugestões sobre como corrigir o problema.

* A utilização da CPU é alta, o que causará latência e/ou tempos limite de solicitação. O cliente pode escalar verticalmente o computador host para fornecer mais recursos ou a carga pode ser distribuída entre mais computadores.
* A disponibilidade de soquete/porta pode estar baixa. Ao executar no Azure, os clientes que usam o SDK do .NET podem atingir o esgotamento de porta de SNAT do Azure (PAT). Para reduzir a chance de atingir esse problema, use a versão 2. x ou 3. x mais recente do SDK do .NET. Este é um exemplo de por que é recomendável sempre executar a versão mais recente do SDK.
* A criação de várias instâncias de DocumentClient pode levar à contenção de conexão e a problemas de tempo limite. Siga as [dicas de desempenho](performance-tips.md)e use uma única instância de DocumentClient em todo o processo.
* Os usuários às vezes veem latência elevada ou tempos limite de solicitação porque suas coleções são provisionadas insuficientemente, as solicitações de restrição de back-end e o cliente tenta novamente internamente. Verifique as [métricas do portal](monitor-accounts.md).
* Azure Cosmos DB distribui a taxa de transferência provisionada de maneira uniforme entre as partições físicas. Verifique as métricas do portal para ver se a carga de trabalho está encontrando uma [chave de partição](partition-data.md)ativa. Isso fará com que a taxa de transferência consumida de agregação (RU/s) pareça estar sob o RUs provisionado, mas uma taxa de transferência consumida de partição única (RU/s) excederá a taxa de transferência provisionada. 
* Além disso, o SDK 2,0 adiciona a semântica de canal a conexões diretas/TCP. Uma conexão TCP é usada para várias solicitações ao mesmo tempo. Isso pode levar a dois problemas em casos específicos:
    * Um alto grau de simultaneidade pode levar à contenção no canal.
    * Solicitações ou respostas grandes podem levar ao bloqueio de cabeçalho de linha no canal e exacerbar a contenção, mesmo com um grau relativamente baixo de simultaneidade.
    * Se o caso cair em qualquer uma dessas duas categorias (ou se a alta utilização da CPU for suspeita), essas são possíveis mitigações:
        * Tente dimensionar o aplicativo para cima/para fora.
        * Além disso, os logs do SDK podem ser capturados por meio do [ouvinte de rastreamento](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) para obter mais detalhes.

### <a name="high-network-latency"></a>Alta latência de rede
Alta latência de rede pode ser identificada usando a [cadeia de caracteres de diagnóstico](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) no SDK v2 ou [diagnóstico](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) no SDK v3.

Se nenhum [tempo limite](#request-timeouts) estiver presente e o diagnóstico mostrar solicitações únicas em que a alta latência é evidente na diferença entre `ResponseTime` e `RequestStartTime`, como (> 300 milissegundos neste exemplo):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Essa latência pode ter várias causas:

* Seu aplicativo não está em execução na mesma região que sua conta de Azure Cosmos DB.
* A configuração do [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) ou do [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) está incorreta e está tentando se conectar a uma região diferente na qual seu aplicativo está sendo executado no momento.
* Pode haver um afunilamento na interface de rede devido ao alto tráfego. Se o aplicativo estiver em execução em máquinas virtuais do Azure, há possíveis soluções alternativas:
    * Considere usar uma [máquina virtual com rede acelerada habilitada](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Habilite [a rede acelerada em uma máquina virtual existente](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Considere o uso de uma [máquina virtual de extremidade superior](../virtual-machines/windows/sizes.md).

### <a name="snat"></a>Esgotamento da porta SNAT (PAT) do Azure

Se seu aplicativo for implantado em [máquinas virtuais do Azure sem um endereço IP público](../load-balancer/load-balancer-outbound-connections.md#defaultsnat), por padrão, [as portas SNAT do Azure](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) estabelecerão conexões com qualquer ponto de extremidade fora de sua VM. O número de conexões permitidas da VM para o ponto de extremidade do Azure Cosmos DB é limitado pela [configuração do Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Essa situação pode levar à limitação de conexão, fechamento de conexão ou aos [tempos limite de solicitação](#request-timeouts)mencionados acima.

 As portas SNAT do Azure são usadas somente quando sua VM tem um endereço IP privado está se conectando a um endereço IP público. Há duas soluções alternativas para evitar a limitação de SNAT do Azure (desde que você já esteja usando uma única instância de cliente em todo o aplicativo):

* Adicione seu ponto de extremidade de serviço do Azure Cosmos DB para a sub-rede da sua rede virtual de Máquinas Virtuais do Azure. Para saber mais, consulte [pontos de extremidade de serviço de Rede Virtual do Microsoft Azure](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quando o ponto de extremidade for habilitado, as solicitações não são mais enviadas de um IP público para o Azure Cosmos DB. Em vez disso, a rede virtual e a identidade de sub-rede são enviadas. Essa alteração poderá resultar em quedas de firewall se apenas IPs públicos forem permitidos. Se você usar um firewall, quando você habilitar o ponto de extremidade de serviço, adicione uma sub-rede para o firewall usando as [ACLs de Rede Virtual](../virtual-network/virtual-networks-acl.md).
* Atribua um [IP público à sua VM do Azure](../load-balancer/load-balancer-outbound-connections.md#assignilpip).

### <a name="http-proxy"></a>Proxy HTTP
Se você usar um proxy HTTP, certifique-se que pode suportar o número de conexões configuradas no SDK `ConnectionPolicy`.
Caso contrário, você enfrentará problemas de conexão.

### <a name="request-rate-too-large"></a>Taxa de solicitação muito grande
' Taxa de solicitação muito grande ' ou código de erro 429 indica que suas solicitações estão sendo limitadas, pois a taxa de transferência consumida (RU/s) excedeu a [taxa de transferência provisionada](set-throughput.md). O SDK tentará automaticamente as solicitações com base na [política de repetição](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)especificada. Se você receber essa falha com frequência, considere aumentar a taxa de transferência na coleção. Verifique as [métricas do portal](use-metrics.md) para ver se você está recebendo erros 429. Examine sua [chave de partição](partitioning-overview.md#choose-partitionkey) para garantir que ela resulte em uma distribuição uniforme do armazenamento e do volume de solicitação. 

### <a name="slow-query-performance"></a>Desempenho de consulta lento
As [métricas de consulta](sql-api-query-metrics.md) ajudarão a determinar onde a consulta está sendo gasta na maior parte do tempo. A partir das métricas de consulta, você pode ver quanto ele está sendo gasto no back-end em vez do cliente.
* Se a consulta de back-end retornar rapidamente e passar um tempo grande no cliente, verifique a carga no computador. É provável que não haja recursos suficientes e o SDK esteja aguardando que os recursos estejam disponíveis para lidar com a resposta.
* Se a consulta de back-end estiver lenta, tente [otimizar a consulta](optimize-cost-queries.md) e examinar a [política de indexação](index-overview.md) atual 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Taxa de solicitação muito grande]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


