---
title: Solucionar problemas Azure Cosmos DB HTTP 408 ou solicitar problemas de tempo limite com o SDK do .NET
description: Como diagnosticar e corrigir a exceção de tempo limite de solicitação do SDK do .NET
author: j82w
ms.service: cosmos-db
ms.date: 07/29/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 3d6fed539581b2d1add87ade92e34bcf2e1913e8
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87417600"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>Diagnosticar e solucionar problemas Azure Cosmos DB tempo limite de solicitação do SDK do .NET
O erro HTTP 408 ocorrerá se o SDK não puder concluir a solicitação antes de o tempo limite ser atingido.

## <a name="customizing-the-timeout-on-the-azure-cosmos-net-sdk"></a>Personalizando o tempo limite no SDK do .NET Cosmos do Azure

O SDK tem duas alternativas distintas para controlar os tempos limite, cada uma com um escopo diferente.

### <a name="requesttimeout"></a>RequestTimeout

A `CosmosClientOptions.RequestTimeout` configuração (ou `ConnectionPolicy.RequestTimeout` para o SDK v2) permite definir um tempo limite que afeta cada solicitação de rede individual.  Uma operação iniciada por um usuário pode abranger várias solicitações de rede (por exemplo, pode haver limitação) e essa configuração se aplicaria a cada solicitação de rede na nova tentativa. Esse não é um tempo limite de solicitação de operação de ponta a ponta.

### <a name="cancellationtoken"></a>CancellationToken

Todas as operações assíncronas no SDK têm um parâmetro CancellationToken opcional. Esse [CancellationToken](https://docs.microsoft.com/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) é usado em toda a operação, em todas as solicitações de rede. Entre as solicitações de rede, o CancellationToken pode estar marcado e uma operação cancelada se o token relacionado tiver expirado. CancellationToken deve ser usado para definir um tempo limite aproximado esperado no escopo da operação.

> [!NOTE]
> CancellationToken é um mecanismo em que a biblioteca verificará o cancelamento quando [não causar um estado inválido](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). A operação pode não ser cancelada exatamente quando o tempo definido no cancelamento estiver ativo, mas, em vez disso, depois que a hora for ativada, ela será cancelada quando for seguro.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A lista a seguir contém causas conhecidas e soluções para exceções de tempo limite de solicitação.

### <a name="1-high-cpu-utilization-most-common-case"></a>1. alta utilização da CPU (caso mais comum)
Para uma latência ideal, é recomendável que o uso da CPU seja de aproximadamente 40%. É recomendável usar 10 segundos como o intervalo para monitorar a utilização máxima (não média) da CPU. Os picos de CPU são mais comuns com consultas entre partições, em que ele pode fazer várias conexões para uma única consulta.

#### <a name="solution"></a>Solução:
O aplicativo cliente que usa o SDK deve ser escalado verticalmente/horizontalmente.

### <a name="2-socket--port-availability-might-be-low"></a>2. a disponibilidade de soquete/porta pode estar baixa
Ao executar no Azure, os clientes que usam o SDK do .NET podem atingir o esgotamento de porta de SNAT do Azure (PAT).

#### <a name="solution-1"></a>Solução 1:
Siga o [Guia de esgotamento de porta SNAT](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Solução 2:
Se você usar um proxy HTTP, certifique-se que pode suportar o número de conexões configuradas no SDK `ConnectionPolicy`.
Caso contrário, você enfrentará problemas de conexão.

### <a name="3-creating-multiple-client-instances"></a>3. criando várias instâncias de cliente
A criação de várias instâncias de cliente pode levar à contenção de conexão e a problemas de tempo limite.

#### <a name="solution"></a>Solução:
Siga as [dicas de desempenho](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)e use uma única instância de CosmosClient em todo o processo.

### <a name="4-hot-partition-key"></a>4. chave de partição ativa
Azure Cosmos DB distribui a taxa de transferência provisionada de maneira uniforme entre as partições físicas. Quando há uma partição ativa, uma ou mais chaves de partição lógica em uma partição física estão consumindo todas as RU/s da partição física, enquanto os RU/s em outras partições físicas ficam inutilizados. Como sintoma, o total de RU/s consumido será menor do que o RU/s provisionado geral no banco de dados ou contêiner, mas você ainda verá limitação (429s) nas solicitações em relação à chave de partição lógica ativa. Use a [métrica de consumo de ru normalizada](monitor-normalized-request-units.md) para ver se a carga de trabalho está encontrando uma partição ativa. 

#### <a name="solution"></a>Solução:
Escolha uma boa chave de partição que distribua uniformemente o volume de solicitação e o armazenamento. Saiba como [alterar sua chave de partição](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="5-high-degree-of-concurrency"></a>5. alto grau de simultaneidade
O aplicativo está fazendo um alto nível de simultaneidade, o que pode levar à contenção no canal

#### <a name="solution"></a>Solução:
O aplicativo cliente que usa o SDK deve ser escalado verticalmente/horizontalmente.

### <a name="6-large-requests-andor-responses"></a>6. solicitações e/ou respostas grandes
Solicitações ou respostas grandes podem levar ao bloqueio de cabeçalho de linha no canal e exacerbar a contenção, mesmo com um grau relativamente baixo de simultaneidade.

#### <a name="solution"></a>Solução:
O aplicativo cliente que usa o SDK deve ser escalado verticalmente/horizontalmente.

### <a name="7-failure-rate-is-within-cosmos-db-sla"></a>7. a taxa de falhas está dentro do SLA Cosmos DB
O aplicativo deve ser capaz de lidar com falhas transitórias e tentar novamente quando necessário. 408 exceções não são repetidas porque, em criar caminhos, não é possível saber se o serviço criou o item ou se ele não foi feito. O envio do mesmo item novamente para Create causará uma exceção de conflito. A lógica de negócios dos aplicativos do usuário pode ter uma lógica personalizada para lidar com conflitos, o que pode ser interrompido da ambiguidade de um item existente vs em conflito de uma repetição de criação.

### <a name="8-failure-rate-is-violating-the-cosmos-db-sla"></a>8. a taxa de falhas está violando o SLA de Cosmos DB
Entre em contato com o suporte do Azure.

## <a name="next-steps"></a>Próximas etapas
* [Diagnosticar e solucionar](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md)
