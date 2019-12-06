---
title: Alta disponibilidade no Azure Cosmos DB
description: Este artigo descreve como Azure Cosmos DB fornece alta disponibilidade, replicando de forma transparente os dados em todas as regiões associadas à conta do Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 1dab10592c8a34bc9df4425785e6dae95e44f219
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872087"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Alta disponibilidade com o Azure Cosmos DB

O Azure Cosmos DB replica de forma transparente seus dados em todas as regiões do Azure associadas à sua conta do Cosmos. O Cosmos DB utiliza várias camadas de redundância para seus dados, como mostra a imagem a seguir:

![Particionamento físico](./media/high-availability/cosmosdb-data-redundancy.png)

- Os dados em contêineres Cosmos são [particionados horizontalmente](partitioning-overview.md).

- Dentro de cada região, toda partição é protegida por um conjunto de réplicas com todas as gravações replicadas e confirmadas permanentemente pela maioria das réplicas. As réplicas são distribuídas em até 10 a 20 domínios de falha.

- Em todas as regiões, cada partição é replicada. Cada região contém todas as partições de dados de um contêiner do Cosmos e pode aceitar gravações e fornecer leituras.  

Se sua conta do cosmos for distribuída entre *n* regiões do Azure, haverá pelo menos *n* x 4 cópias de todos os seus dados. Além de fornecer acesso a dados de baixa latência e aumentar a taxa de transferência de gravação/leitura entre as regiões associadas à sua conta do cosmos, ter mais regiões (mais altas *N*) melhora ainda mais a disponibilidade.  

## <a name="slas-for-availability"></a>SLAs de disponibilidade

Como um banco de dados distribuído globalmente, o Cosmos DB fornece SLAs abrangentes que englobam a taxa de transferência, a latência no 99º percentil, a coerência e a alta disponibilidade. A tabela abaixo mostra as garantias de alta disponibilidade fornecidas pelo Cosmos DB para contas de uma e várias regiões. Para alta disponibilidade, sempre configure suas contas do cosmos para ter várias regiões de gravação.

|Tipo de operação  | Região única |Várias regiões (gravações de região única)|Várias regiões (gravações de várias regiões) |
|---------|---------|---------|-------|
|Gravações    | 99,99    |99,99   |99,999|
|Leituras     | 99,99    |99,999  |99,999|

> [!NOTE]
> Na prática, a disponibilidade de gravação real para desatualização limitada, sessão, prefixo consistente e modelos de consistência eventual é significativamente maior do que os SLAs publicados. A disponibilidade de leitura real para todos os níveis de coerência é significativamente mais alta do que os SLAs publicados.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Alta disponibilidade com Cosmos DB em caso de interrupções regionais

Interrupções regionais não são incomuns e o Azure Cosmos DB garante que seu banco de dados esteja sempre altamente disponível. Os detalhes a seguir capturam Cosmos DB comportamento durante uma interrupção, dependendo da configuração da sua conta do Cosmos:

- Com o Cosmos DB, antes que uma operação de gravação seja confirmada para o cliente, os dados serão confirmados por um quorum de réplicas dentro da região que aceitará as operações de gravação.

- Contas de várias regiões configuradas com várias regiões de gravação estarão altamente disponíveis para leituras e gravações. Failovers regionais são instantâneos e não exigem nenhuma alteração do aplicativo.

- **Contas de várias regiões com uma região de gravação única (interrupção da região de gravação):** 
  * Durante uma interrupção da região de gravação, essas contas permanecerão altamente disponíveis para leituras. Para que as solicitações de gravação tenham sucesso, você deve ativar a opção **habilitar failover automático** em sua conta do Azure Cosmos. Habilitar essa opção fará failover da região afetada para outra região na ordem de prioridade de região que você especificou. 
  * Quando a região afetada anteriormente estiver novamente online, todos os dados de gravação que não foram replicados quando a região falharem serão disponibilizados por meio do [feed de conflitos](how-to-manage-conflicts.md#read-from-conflict-feed). Os aplicativos podem ler o feed de conflitos, resolver os conflitos com base na lógica específica do aplicativo e gravar os dados atualizados de volta no contêiner Cosmos do Azure, conforme apropriado. 
  * Depois que a região de gravação anteriormente afetada se recupera, ela fica automaticamente disponível como uma região de leitura. Você pode alternar de volta para a região recuperada como a região de gravação. Você pode alternar as regiões usando [CLI do Azure ou portal do Azure](how-to-manage-database-account.md#manual-failover). Não há **perda de dados ou disponibilidade** antes, durante ou depois que você alterna a região de gravação e seu aplicativo continua a ser altamente disponível. 

- **Contas de várias regiões com uma região de gravação única (interrupção da região de leitura):** 
  * Durante uma interrupção de região de leitura, essas contas permanecerão altamente disponíveis para leituras e gravações. 
  * A região afetada será desconectada automaticamente da região de gravação e será marcada como offline. Os [SDKs de Azure Cosmos DB](sql-api-sdk-dotnet.md) redirecionarão as chamadas de leitura para a próxima região disponível na lista de regiões preferenciais. 
  * Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas retornarão automaticamente à região de gravação atual. 
  * Não é necessária nenhuma alteração no código do aplicativo para lidar com a interrupção da região de leitura. Por fim, quando a região afetada ficar online novamente, a região de leitura afetada anteriormente será automaticamente sincronizada com a região de gravação atual e estará disponível novamente para fornecer solicitações de leitura. 
  * Leituras subsequentes são redirecionadas para a região recuperada sem exigir nenhuma alteração ao código do aplicativo. Durante o failover e a rejunção de uma região com falha anteriormente, as garantias de consistência de leitura continuam a ser respeitadas pelo Cosmos DB.

- Contas de uma única região poderão perder disponibilidade após uma indisponibilidade regional. É sempre recomendável configurar **pelo menos duas regiões** (preferencialmente, pelo menos duas regiões de gravação) com sua conta do cosmos para garantir a alta disponibilidade em todos os momentos.

- Mesmo em um evento raro e infeliz quando a região do Azure for permanentemente irrecuperável, não haverá perda de dados se sua conta de Cosmos de várias regiões estiver configurada com o nível de consistência de *alta segurança*padrão. No caso de uma região de gravação permanentemente irrecuperável, para as contas Cosmos de várias regiões configuradas com consistência de desatualização limitada, a janela potencial de perda de dados é restrita à janela de desatualização (*K* ou *T*); para os níveis de sessão, de prefixo consistente e de consistência eventual, a janela potencial de perda de dados é restrita a um máximo de cinco segundos. 

## <a name="availability-zone-support"></a>Suporte à zona de disponibilidade

O Azure Cosmos DB é um serviço de banco de dados de vários mestres distribuído globalmente que fornece alta disponibilidade e resiliência durante interrupções regionais. Além da resiliência entre regiões, agora você pode habilitar a **redundância de zona** ao selecionar uma região para associar ao banco de dados Cosmos do Azure. 

Com o suporte à zona de disponibilidade, Azure Cosmos DB garantirá que as réplicas sejam colocadas em várias zonas em uma determinada região para fornecer alta disponibilidade e resiliência durante falhas zonais. Não há nenhuma alteração na latência e outros SLAs nessa configuração. No caso de uma falha de zona única, a redundância de zona fornece durabilidade total de dados com RPO = 0 e disponibilidade com RTO = 0. 

A redundância de zona é uma *funcionalidade suplementar* para o recurso de [replicação de vários mestres](how-to-multi-master.md) . Somente a redundância de zona não basta para se obter a resiliência regional. Por exemplo, no caso de interrupções regionais ou acesso de baixa latência em todas as regiões, é aconselhável ter várias regiões de gravação além da redundância de zona. 

Ao configurar gravações de várias regiões para sua conta do Azure Cosmos, você pode aceitar a redundância de zona sem custo adicional. Caso contrário, consulte a observação abaixo sobre os preços do suporte à redundância de zona. Você pode habilitar a redundância de zona em uma região existente da sua conta do Azure Cosmos removendo a região e adicionando-a novamente com a redundância da zona habilitada.

Esse recurso está disponível nas seguintes regiões do Azure:

* Sul do Reino Unido
* Sudeste Asiático 
* Leste dos EUA
* Leste dos EUA 2 
* EUA Central
* Oeste da Europa
* Oeste dos EUA 2

> [!NOTE] 
> A habilitação de Zonas de Disponibilidade para uma única conta do Azure Cosmos de região resultará em encargos equivalentes à adição de uma região adicional à sua conta. Para obter detalhes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) e o custo de [várias regiões em artigos Azure Cosmos DB](optimize-cost-regions.md) . 

A tabela a seguir resume a capacidade de alta disponibilidade de várias configurações de conta: 

|KPI  |Região única sem Zonas de Disponibilidade (não AZ)  |Região única com Zonas de Disponibilidade (AZ)  |Gravações de várias regiões com Zonas de Disponibilidade (AZ, 2 regiões) – configuração mais recomendada |
|---------|---------|---------|---------|
|SLA de disponibilidade de gravação     |   99,99%      |    99,99%     |  99,999%  |
|SLA de leitura de disponibilidade   |   99,99%      |   99,99%      |  99,999%       |
|Preço  |  Taxa de cobrança de região única |  Taxa de cobrança da zona de disponibilidade de região única |  Taxa de cobrança de várias regiões       |
|Falhas de zona – perda de dados   |  Perda de dados  |   Sem perda de dados |   Sem perda de dados  |
|Falhas de zona – disponibilidade |  Perda de disponibilidade  | Sem perda de disponibilidade  |  Sem perda de disponibilidade  |
|Latência de leitura    |  Entre regiões    |   Entre regiões   |    Baixo  |
|Latência de gravação    |   Entre regiões   |  Entre regiões    |   Baixo   |
|Interrupção regional – perda de dados    |   Perda de dados      |  Perda de dados       |   Perda de dados <br/><br/> Ao usar a consistência de desatualização limitada com vários mestres e mais de uma região, a perda de dados é limitada à desatualização limitada configurada em sua conta. <br/><br/> A perda de dados durante a interrupção regional pode ser evitada com a configuração de uma forte consistência com várias regiões. Essa opção é obtida com compensações que afetam a disponibilidade e o desempenho.      |
|Interrupção regional – disponibilidade  |  Perda de disponibilidade       |  Perda de disponibilidade       |  Sem perda de disponibilidade  |
|Produtividade    |  Taxa de transferência provisionada X RU/s      |  Taxa de transferência provisionada X RU/s       |  taxa de transferência provisionada de 2 RU/s <br/><br/> Esse modo de configuração requer duas vezes a quantidade de taxa de transferência quando comparado a uma única região com Zonas de Disponibilidade porque há duas regiões.   |

> [!NOTE] 
> Para habilitar o suporte à zona de disponibilidade para uma conta do Azure Cosmos de várias regiões, a conta deve ter gravações de vários mestres habilitadas.


Você pode habilitar a redundância de zona ao adicionar uma região a contas novas ou existentes do Azure Cosmos. Para habilitar a redundância de zona em sua conta do Azure Cosmos, você deve definir o sinalizador `isZoneRedundant` como `true` para um local específico. Você pode definir esse sinalizador dentro da propriedade Locations. Por exemplo, o seguinte trecho do PowerShell habilita a redundância de zona para a região "sudeste asiático":

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

O comando a seguir mostra como habilitar a redundância de zona para as regiões "Eastus" e "WestUS2":

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True \
  --enable-multiple-write-locations
```

Você pode habilitar Zonas de Disponibilidade usando portal do Azure ao criar uma conta do Azure Cosmos. Ao criar uma conta, certifique-se de habilitar a **redundância geográfica**, as **gravações de várias regiões**e escolher uma região onde zonas de disponibilidade têm suporte: 

![Habilitar Zonas de Disponibilidade usando portal do Azure](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Criando aplicativos altamente disponíveis

- Para garantir a alta disponibilidade de leitura e gravação, configure sua conta do Cosmos para abranger pelo menos duas regiões com várias regiões de gravação. Essa configuração fornecerá a mais alta disponibilidade, a menor latência e a melhor escalabilidade para leituras e gravações apoiadas por SLAs. Para saber mais, confira como [configurar sua conta do Cosmos com várias regiões de gravação](tutorial-global-distribution-sql-api.md).

- Para contas do Cosmos de várias regiões configuradas com uma única região de gravação, [habilite o failover automático usando a CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#automatic-failover). Depois de habilitar o failover automático, sempre que houver um desastre regional, o Cosmos DB fará o failover da sua conta automaticamente.  

- Mesmo se a conta do Cosmos estiver altamente disponível, seu aplicativo poderá não ser corretamente projetado para permanecer altamente disponível. Para testar a alta disponibilidade de ponta a ponta de seu aplicativo, invoque periodicamente o [failover manual usando CLI do Azure ou portal do Azure](how-to-manage-database-account.md#manual-failover), como parte de seus testes de aplicativo ou de recuperação de desastres (Dr).

- Em um ambiente de banco de dados distribuído globalmente, há uma relação direta entre o nível de consistência e a durabilidade dos dados na presença de uma interrupção em toda a região. À medida que você vai desenvolvendo o plano de continuidade dos negócios, precisará saber qual é o tempo máximo aceitável antes que o aplicativo se recupere completamente após um evento de interrupção. O tempo necessário para o aplicativo se recuperar totalmente é conhecido como RTO (objetivo de tempo de recuperação). Também é necessário saber o período máximo de atualizações de dados recentes que o aplicativo pode perder sem maiores problemas durante a recuperação após um evento de interrupção. O período de tempo de atualizações que você pode perder é conhecido como RPO (objetivo de ponto de recuperação). Para ver o RTO e o RPO do Azure Cosmos DB, confira [Níveis de consistência e durabilidade dos dados](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Próximos passos

Em seguida, você poderá ler os artigos a seguir:

* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Taxa de transferência provisionada para dimensionamento global](scaling-throughput.md)
* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)
* [Níveis de coerência no Azure Cosmos DB](consistency-levels.md)
* [Como configurar sua conta do cosmos com várias regiões de gravação](how-to-multi-master.md)
