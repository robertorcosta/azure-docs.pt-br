---
title: Diagnosticar e solucionar problemas ao usar o SDK . NET para Azure Cosmos DB
description: Use recursos como registro em log do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas de Azure Cosmos DB ao usar o SDK do .NET.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/16/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0eb5d9cd86be05e5ad69bc9543231987e3c1dd2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85799258"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticar e solucionar problemas ao usar o SDK . NET para Azure Cosmos DB

> [!div class="op_single_selector"]
> * [SDK do Java v4](troubleshoot-java-sdk-v4-sql.md)
> * [SDK do Java Assíncrono v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Este artigo aborda problemas comuns, soluções alternativas, etapas de diagnóstico e ferramentas quando você usa o [SDK do .net](sql-api-sdk-dotnet.md) com Azure Cosmos DB contas da API do SQL.
O SDK do .NET fornece a representação lógica do lado do cliente para acessar a API do SQL do Azure Cosmos DB. Este artigo descreve as ferramentas e as abordagens para ajudá-lo se você tiver algum problema.

## <a name="checklist-for-troubleshooting-issues"></a>Lista de verificação para solucionar problemas
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


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemas comuns e soluções alternativas

### <a name="general-suggestions"></a>Sugestões gerais
* Execute seu aplicativo na mesma região do Azure que sua conta de Azure Cosmos DB, sempre que possível. 
* Você pode encontrar problemas de conectividade/disponibilidade devido à falta de recursos no computador cliente. É recomendável monitorar a utilização da CPU em nós que executam o cliente Azure Cosmos DB e escalar verticalmente se eles estiverem sendo executados com alta carga.

### <a name="check-the-portal-metrics"></a>Verificar as métricas do portal
Verificar as [métricas do portal](monitor-accounts.md) ajudará a determinar se é um problema do lado do cliente ou se há um problema com o serviço. Por exemplo, se as métricas contiverem uma alta taxa de solicitações limitadas por taxa (código de status HTTP 429), o que significa que a solicitação está sendo limitada e, em seguida, verifique a seção [taxa de solicitação muito grande] . 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Tempos limite de solicitações
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

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Alta latência de rede
Alta latência de rede pode ser identificada usando a [cadeia de caracteres de diagnóstico](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) no SDK v2 ou [diagnóstico](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) no SDK v3.

Se nenhum [tempo limite](#request-timeouts) estiver presente e o diagnóstico mostrar solicitações únicas em que a alta latência é evidente na diferença entre `ResponseTime` e `RequestStartTime` , assim como (>300 milissegundos neste exemplo):

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

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Esgotamento da porta SNAT (PAT) do Azure

Se seu aplicativo for implantado em [máquinas virtuais do Azure sem um endereço IP público](../load-balancer/load-balancer-outbound-connections.md), por padrão, [as portas SNAT do Azure](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) estabelecerão conexões com qualquer ponto de extremidade fora de sua VM. O número de conexões permitidas da VM para o ponto de extremidade do Azure Cosmos DB é limitado pela [configuração do Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Essa situação pode levar à limitação de conexão, fechamento de conexão ou aos [tempos limite de solicitação](#request-timeouts)mencionados acima.

 As portas SNAT do Azure são usadas somente quando sua VM tem um endereço IP privado está se conectando a um endereço IP público. Há duas soluções alternativas para evitar a limitação de SNAT do Azure (desde que você já esteja usando uma única instância de cliente em todo o aplicativo):

* Adicione seu ponto de extremidade de serviço do Azure Cosmos DB para a sub-rede da sua rede virtual de Máquinas Virtuais do Azure. Para saber mais, consulte [pontos de extremidade de serviço de Rede Virtual do Microsoft Azure](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quando o ponto de extremidade for habilitado, as solicitações não são mais enviadas de um IP público para o Azure Cosmos DB. Em vez disso, a rede virtual e a identidade de sub-rede são enviadas. Essa alteração poderá resultar em quedas de firewall se apenas IPs públicos forem permitidos. Se você usar um firewall, quando você habilitar o ponto de extremidade de serviço, adicione uma sub-rede para o firewall usando as [ACLs de Rede Virtual](../virtual-network/virtual-networks-acl.md).
* Atribua um [IP público à sua VM do Azure](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="http-proxy"></a>Proxy HTTP
Se você usar um proxy HTTP, certifique-se que pode suportar o número de conexões configuradas no SDK `ConnectionPolicy`.
Caso contrário, você enfrentará problemas de conexão.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Taxa de solicitação grande demais
' Taxa de solicitação muito grande ' ou código de erro 429 indica que suas solicitações estão sendo limitadas, pois a taxa de transferência consumida (RU/s) excedeu a [taxa de transferência provisionada](set-throughput.md). O SDK tentará automaticamente as solicitações com base na [política de repetição](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)especificada. Se você receber essa falha com frequência, considere aumentar a taxa de transferência na coleção. Verifique as [métricas do portal](use-metrics.md) para ver se você está recebendo erros 429. Examine sua [chave de partição](partitioning-overview.md#choose-partitionkey) para garantir que ela resulte em uma distribuição uniforme do armazenamento e do volume de solicitação. 

### <a name="slow-query-performance"></a>Desempenho de consulta lento
As [métricas de consulta](sql-api-query-metrics.md) ajudarão a determinar onde a consulta está sendo gasta na maior parte do tempo. A partir das métricas de consulta, você pode ver quanto ele está sendo gasto no back-end em vez do cliente.
* Se a consulta de back-end retornar rapidamente e passar um tempo grande no cliente, verifique a carga no computador. É provável que não haja recursos suficientes e o SDK esteja aguardando que os recursos estejam disponíveis para lidar com a resposta.
* Se a consulta de back-end estiver lenta, tente [otimizar a consulta](optimize-cost-queries.md) e examinar a [política de indexação](index-overview.md) atual 

### <a name="http-401-the-mac-signature-found-in-the-http-request-is-not-the-same-as-the-computed-signature"></a>HTTP 401: a assinatura MAC encontrada na solicitação HTTP não é igual à assinatura computada
Se você recebeu a seguinte mensagem de erro 401: "A assinatura MAC encontrada na solicitação HTTP não é igual à assinatura computada". pode ser causado pelos cenários a seguir.

1. A chave foi revezada e não segue as [melhores práticas](secure-access-to-data.md#key-rotation). Normalmente, esse é o motivo. O revezamento de chave de conta do Cosmos DB pode levar desde alguns segundos a alguns dias, dependendo do tamanho da conta do Cosmos DB.
   1. A assinatura MAC 401 é vista logo após um revezamento de chave e eventualmente é interrompida sem alterações. 
1. A chave está configurada incorretamente no aplicativo, então ela não corresponde à conta.
   1. O problema de assinatura MAC 401 será constante e acontecerá em todas as chamadas
1. O aplicativo está usando as [chaves somente leitura](secure-access-to-data.md#master-keys) para operações de gravação.
   1. O problema de assinatura MAC 401 ocorrerá somente quando o aplicativo estiver solicitando gravações. Não haverá problemas em solicitações de leitura.
1. Há uma condição de corrida com a criação de contêiner. Uma instância do aplicativo está tentando acessar o contêiner antes que a criação do contêiner seja concluída. Esse é o cenário mais comum nesse caso, se o aplicativo estiver em execução e o contêiner for excluído e recriado com o mesmo nome durante essa execução. O SDK tentará usar o novo contêiner, mas, como a criação do contêiner ainda está em andamento, ele não terá as chaves.
   1. O problema de assinatura MAC 401 aparece logo após a criação de um contêiner e só ocorre até a conclusão da criação do contêiner.
 
 ### <a name="http-error-400-the-size-of-the-request-headers-is-too-long"></a>Erro de HTTP 400. O tamanho dos cabeçalhos de solicitação é muito longo.
 O tamanho do cabeçalho cresceu para grande e excede o tamanho máximo permitido. É sempre recomendável usar o SDK mais recente. Certifique-se de usar pelo menos a versão [3. x](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/changelog.md) ou [2. x](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md), que adiciona o rastreamento de tamanho de cabeçalho à mensagem de exceção.

Com
 1. O token de sessão ficou muito grande. O token de sessão aumenta conforme o número de partições aumenta no contêiner.
 2. O token de continuação cresceu para grande. Consultas diferentes terão tamanhos de token de continuação diferentes.
 3. Ela é causada por uma combinação do token de sessão e do token de continuação.

Solução:
   1. Siga as [dicas de desempenho](performance-tips.md) e converta o aplicativo no modo de conexão direta + TCP. O Direct + TCP não tem a restrição de tamanho de cabeçalho, como HTTP, que evita esse problema.
   2. Se o token de sessão for a causa, uma mitigação temporária será reiniciar o aplicativo. Reiniciar a instância do aplicativo redefinirá o token de sessão. Se as exceções forem interrompidas após a reinicialização, ela confirmará que o token de sessão é a causa. Eventualmente vai aumentar para o tamanho que causará a exceção.
   3. Se o aplicativo não puder ser convertido em Direct + TCP e o token de sessão for a causa, a mitigação poderá ser feita alterando o [nível de consistência](consistency-levels.md)do cliente. O token de sessão é usado somente para consistência de sessão, que é o padrão para Cosmos DB. Qualquer outro nível de consistência não usará o token de sessão. 
   4. Se o aplicativo não puder ser convertido em Direct + TCP e o token de continuação for a causa, tente definir a opção ResponseContinuationTokenLimitInKb. A opção pode ser encontrada no Feedoptions para v2 ou no QueryRequestOptions em v3.

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Taxa de solicitação grande demais]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
