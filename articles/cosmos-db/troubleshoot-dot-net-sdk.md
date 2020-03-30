---
title: Diagnosticar e solucionar problemas ao usar o SDK . NET para Azure Cosmos DB
description: Use recursos como registro do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas do Azure Cosmos DB ao usar o .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137947"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnosticar e solucionar problemas ao usar o SDK . NET para Azure Cosmos DB
Este artigo abrange problemas comuns, solução sem solução, etapas de diagnóstico e ferramentas quando você usa o [SDK .NET](sql-api-sdk-dotnet.md) com contas API Azure Cosmos DB SQL.
O .NET SDK fornece representação lógica do lado do cliente para acessar a API Azure Cosmos DB SQL. Este artigo descreve as ferramentas e as abordagens para ajudá-lo se você tiver algum problema.

## <a name="checklist-for-troubleshooting-issues"></a>Lista de verificação para problemas de solução de problemas:
Considere a lista de verificação a seguir antes de mover sua aplicação para a produção. O uso da lista de verificação evitará vários problemas comuns que você possa ver. Você também pode diagnosticar rapidamente quando ocorre um problema:

*    Use o [SDK](sql-api-sdk-dotnet-standard.md)mais recente . Os SDKs de visualização não devem ser usados para produção. Isso evitará bater problemas conhecidos que já estão corrigidos.
*    Examine [dicas de desempenho](performance-tips.md) e siga as práticas sugeridas. Isso ajudará a evitar o dimensionamento, a latência e outros problemas de desempenho.
*    Habilite o registro de SDK para ajudá-lo a solucionar um problema. A ativação do registro pode afetar o desempenho, por isso é melhor habilitá-lo somente na solução de problemas. Você pode habilitar os seguintes logs:
    *    [Registre métricas](monitor-accounts.md) usando o portal Azure. As métricas do portal mostram a telemetria Azure Cosmos DB, o que é útil para determinar se o problema corresponde ao Azure Cosmos DB ou se é do lado do cliente.
    *    Registre a [seqüência de diagnósticos](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) no V2 SDK ou [diagnósticos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) no V3 SDK a partir das respostas de operação de ponto.
    *    Registre as métricas de [consulta sql](sql-api-query-metrics.md) de todas as respostas de consulta 
    *    Siga a configuração para [registro de SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Dê uma olhada na seção [Problemas comuns e soluções alternativas](#common-issues-workarounds) neste artigo.

Verifique a [seção de problemas do GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) que é monitorada ativamente. Verifique se você encontrar algum problema semelhante com uma solução alternativa já arquivada. Se você não encontrou uma solução, então faça um problema no GitHub. Você pode abrir um carrapato de apoio para questões urgentes.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemas comuns e soluções alternativas

### <a name="general-suggestions"></a>Sugestões gerais
* Execute seu aplicativo na mesma região do Azure que sua conta Azure Cosmos DB, sempre que possível. 
* Você pode encontrar problemas de conectividade/disponibilidade devido à falta de recursos na máquina cliente. Recomendamos monitorar a utilização da CPU em nós executando o cliente Azure Cosmos DB e dimensionando/acessando se eles estiverem sendo executados com alta carga.

### <a name="check-the-portal-metrics"></a>Confira as métricas do portal
Verificar as [métricas](monitor-accounts.md) do portal ajudará a determinar se é um problema do lado do cliente ou se há um problema com o serviço. Por exemplo, se as métricas contiverem uma alta taxa de solicitações limitadas de taxa (código de status HTTP 429), o que significa que a solicitação está sendo estrangulada, verifique a [taxa de solicitação muito grande.] 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Solicita tempoouts
RequestTimeout geralmente acontece quando se usa Direct/TCP, mas pode acontecer no modo Gateway. Esses erros são as causas conhecidas comuns e sugestões de como corrigir o problema.

* A utilização da CPU é alta, o que causará latência e/ou tempo outs de solicitação. O cliente pode escalar a máquina host para dar-lhe mais recursos, ou a carga pode ser distribuída em mais máquinas.
* A disponibilidade do soquete/porta pode ser baixa. Ao ser executado no Azure, os clientes que usam o .NET SDK podem atingir a exaustão da porta Azure SNAT (PAT). Para reduzir a chance de acertar este problema, use a versão mais recente 2.x ou 3.x do .NET SDK. Este é um exemplo de por que é recomendado para sempre executar a versão mais recente do SDK.
* A criação de várias instâncias do DocumentClient pode levar a problemas de contenção e tempo de tempo. Siga as [dicas de desempenho](performance-tips.md)e use uma única instância DocumentClient em todo um processo.
* Às vezes, os usuários veem latência elevada ou solicitam intervalos porque suas cobranças são provisionadas insuficientemente, as solicitações de aceleradores back-end e as tentativas internas do cliente. Verifique as métricas do [portal.](monitor-accounts.md)
* O Azure Cosmos DB distribui o throughput provisionado global uniformemente através de partições físicas. Verifique as métricas do portal para ver se a carga de trabalho está encontrando uma [chave de partição](partition-data.md)quente . Isso fará com que o throughput agregado consumido (RU/s) pareça estar as RUs provisionadas, mas uma única partição consumida throughput (RU/s) excederá o throughput provisionado. 
* Além disso, o SDK 2.0 adiciona semântica de canal a conexões diretas/TCP. Uma conexão TCP é usada para várias solicitações ao mesmo tempo. Isso pode levar a duas questões em casos específicos:
    * Um alto grau de concorrência pode levar à discórdia no canal.
    * Grandes solicitações ou respostas podem levar ao bloqueio de linha no canal e exacerbar a contenção, mesmo com um grau relativamente baixo de concorrência.
    * Se o caso se enquadra em qualquer uma dessas duas categorias (ou se houver suspeita de alta utilização da CPU), estas são possíveis mitigações:
        * Tente escalar o aplicativo para cima/para fora.
        * Além disso, os registros do SDK podem ser capturados através [do Trace Listener](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) para obter mais detalhes.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Alta latência da rede
A alta latência da rede pode ser identificada usando a [seqüência de diagnósticos](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) no V2 SDK ou [diagnósticos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) no V3 SDK.

Se não houver [intervalos](#request-timeouts) de tempo e os diagnósticos mostrarem solicitações `ResponseTime` `RequestStartTime`únicas onde a alta latência é evidente na diferença entre e , assim ( >300 milissegundos neste exemplo):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Essa latência pode ter múltiplas causas:

* Seu aplicativo não está sendo executado na mesma região que sua conta azure Cosmos DB.
* A configuração [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) ou [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) está incorreta e está tentando se conectar a uma região diferente para onde seu aplicativo está sendo executado no momento.
* Pode haver um gargalo na interface da Rede por causa do tráfego elevado. Se o aplicativo estiver sendo executado em Máquinas Virtuais do Azure, existem possíveis soluções de solução:
    * Considere o uso de uma [máquina virtual com rede acelerada ativada](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * [Habilite a rede acelerada em uma máquina virtual existente](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Considere usar uma [máquina virtual de ponta superior](../virtual-machines/windows/sizes.md).

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Esgotamento da porta SNAT (PAT) do Azure

Se o aplicativo for implantado em [Máquinas Virtuais Azure sem um endereço IP público,](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)por padrão [as portas Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) estabelecerão conexões em qualquer ponto final fora da VM. O número de conexões permitidas da VM para o ponto de extremidade do Azure Cosmos DB é limitado pela [configuração do Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Essa situação pode levar ao estrangulamento da conexão, ao fechamento da conexão ou aos tempos de solicitação acima [mencionados](#request-timeouts).

 As portas SNAT do Azure são usadas somente quando sua VM tem um endereço IP privado está se conectando a um endereço IP público. Existem duas soluçãos para evitar a limitação do Azure SNAT (desde que você já esteja usando uma única instância do cliente em todo o aplicativo):

* Adicione seu ponto de extremidade de serviço do Azure Cosmos DB para a sub-rede da sua rede virtual de Máquinas Virtuais do Azure. Para saber mais, consulte [pontos de extremidade de serviço de Rede Virtual do Microsoft Azure](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Quando o ponto de extremidade for habilitado, as solicitações não são mais enviadas de um IP público para o Azure Cosmos DB. Em vez disso, a rede virtual e a identidade de sub-rede são enviadas. Essa alteração poderá resultar em quedas de firewall se apenas IPs públicos forem permitidos. Se você usar um firewall, quando você habilitar o ponto de extremidade de serviço, adicione uma sub-rede para o firewall usando as [ACLs de Rede Virtual](../virtual-network/virtual-networks-acl.md).
* Atribua um [IP público à sua VM Azure](../load-balancer/load-balancer-outbound-connections.md#assignilpip).

### <a name="http-proxy"></a>Proxy HTTP
Se você usar um proxy HTTP, certifique-se que pode suportar o número de conexões configuradas no SDK `ConnectionPolicy`.
Caso contrário, você enfrentará problemas de conexão.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Taxa de solicitação grande demais
'Taxa de solicitação muito grande' ou código de erro 429 indica que suas solicitações estão sendo estranguladas, porque o throughput consumido (RU/s) excedeu o [throughput provisionado](set-throughput.md). O SDK irá tentar automaticamente solicitações com base na política de [repetição](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)especificada . Se você conseguir esse fracasso com frequência, considere aumentar o throughput na coleção. Verifique as [métricas do portal](use-metrics.md) para ver se você está recebendo 429 erros. Revise sua [chave de partição](partitioning-overview.md#choose-partitionkey) para garantir que ela resulte em uma distribuição uniforme do armazenamento e do volume de solicitação. 

### <a name="slow-query-performance"></a>Desempenho lento da consulta
As [métricas de consulta](sql-api-query-metrics.md) ajudarão a determinar onde a consulta está passando a maior parte do tempo. A partir das métricas de consulta, você pode ver quanto dele está sendo gasto no back-end versus o cliente.
* Se a consulta de back-end retornar rapidamente e gastar um grande tempo no cliente verifique a carga na máquina. É provável que não haja recursos suficientes e o SDK está esperando que os recursos estejam disponíveis para lidar com a resposta.
* Se a consulta back-end for lenta, tente [otimizar a consulta](optimize-cost-queries.md) e olhar para a política de [indexação](index-overview.md) atual 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Taxa de solicitação grande demais]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


