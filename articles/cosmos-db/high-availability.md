---
title: Alta disponibilidade no Azure Cosmos DB
description: Este artigo descreve como o Azure Cosmos DB fornece alta disponibilidade
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 0a67360ab840b7897c85a382dfc3965bd27f6a3e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81688069"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Alta disponibilidade com o Azure Cosmos DB

O Azure Cosmos DB replica de forma transparente seus dados em todas as regiões do Azure associadas à sua conta do Cosmos. O Cosmos DB utiliza várias camadas de redundância para seus dados, como mostra a imagem a seguir:

![Particionamento físico](./media/high-availability/cosmosdb-data-redundancy.png)

- Os dados em contêineres cosmos são [particionados horizontalmente](partitioning-overview.md).

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

- Contas de uma única região poderão perder disponibilidade após uma indisponibilidade regional. É sempre recomendável configurar **pelo menos duas regiões** (preferencialmente, pelo menos duas regiões de gravação) com sua conta do cosmos para garantir a alta disponibilidade em todos os momentos.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Contas de várias regiões com uma região de gravação única (interrupção da região de gravação)

- Durante uma interrupção da região de gravação, a conta Cosmos promoverá automaticamente uma região secundária para ser a nova região de gravação primária quando **habilitar o failover automático** for configurado na conta do Azure Cosmos. Quando habilitado, o failover ocorrerá em outra região na ordem de prioridade de região que você especificou.
- Quando a região afetada anteriormente estiver novamente online, todos os dados de gravação que não foram replicados quando a região falharem serão disponibilizados por meio do [feed de conflitos](how-to-manage-conflicts.md#read-from-conflict-feed). Os aplicativos podem ler o feed de conflitos, resolver os conflitos com base na lógica específica do aplicativo e gravar os dados atualizados de volta no contêiner Cosmos do Azure, conforme apropriado.
- Depois que a região de gravação anteriormente afetada se recupera, ela fica automaticamente disponível como uma região de leitura. Você pode alternar de volta para a região recuperada como a região de gravação. Você pode alternar as regiões usando o [PowerShell, CLI do Azure ou portal do Azure](how-to-manage-database-account.md#manual-failover). Não há **perda de dados ou disponibilidade** antes, durante ou depois que você alterna a região de gravação e seu aplicativo continua a ser altamente disponível.

> [!IMPORTANT]
> É altamente recomendável que você configure as contas do Azure Cosmos usadas para cargas de trabalho de produção para **habilitar o failover automático**. O failover manual requer conectividade entre a região de gravação secundária e primária para concluir uma verificação de consistência para garantir que não haja perda de dados durante o failover. Se a região primária não estiver disponível, essa verificação de consistência não poderá ser concluída e o failover manual não terá sucesso, resultando em perda de disponibilidade de gravação.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Contas de várias regiões com uma região de gravação única (interrupção da região de leitura)

- Durante uma interrupção da região de leitura, as contas do cosmos usando qualquer nível de consistência ou consistência forte com três ou mais regiões de leitura permanecerão altamente disponíveis para leituras e gravações.
- A região afetada é desconectada automaticamente e será marcada como offline. Os [SDKs de Azure Cosmos DB](sql-api-sdk-dotnet.md) redirecionarão as chamadas de leitura para a próxima região disponível na lista de regiões preferenciais.
- Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas retornarão automaticamente à região de gravação atual.
- Não é necessária nenhuma alteração no código do aplicativo para lidar com a interrupção da região de leitura. Quando a região de leitura afetada voltar a ficar online, ela será sincronizada automaticamente com a região de gravação atual e estará disponível novamente para atender às solicitações de leitura.
- Leituras subsequentes são redirecionadas para a região recuperada sem exigir nenhuma alteração ao código do aplicativo. Durante o failover e a rejunção de uma região com falha anteriormente, as garantias de consistência de leitura continuam a ser respeitadas pelo Cosmos DB.

> [!IMPORTANT]
> As contas do Azure Cosmos usando consistência forte com duas ou menos regiões de leitura perderão a disponibilidade de gravação durante uma interrupção da região de leitura, mas manterão a disponibilidade de leitura para as regiões restantes.

- Mesmo em um evento raro e infeliz quando a região do Azure for permanentemente irrecuperável, não haverá perda de dados se sua conta de Cosmos de várias regiões estiver configurada com consistência *forte* . No caso de uma região de gravação permanentemente irrecuperável, uma conta Cosmos de várias regiões configurada com consistência de desatualização limitada, a janela potencial de perda de dados é restrita à janela de desatualização (*k* ou *T*) em que k = 100.000 atualizações e T = 5 minutos. Para os níveis de sessão, de prefixo consistente e de consistência eventual, a janela potencial de perda de dados é restrita a um máximo de 15 minutos. Para obter mais informações sobre os destinos RTO e RPO para Azure Cosmos DB, consulte [níveis de consistência e durabilidade de dados](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>Suporte à zona de disponibilidade

Além da resiliência entre regiões, agora você pode habilitar a **redundância de zona** ao selecionar uma região para associar ao banco de dados Cosmos do Azure.

Com o suporte à zona de disponibilidade, Azure Cosmos DB garantirá que as réplicas sejam colocadas em várias zonas em uma determinada região para fornecer alta disponibilidade e resiliência durante falhas zonais. Não há nenhuma alteração na latência e outros SLAs nessa configuração. No caso de uma falha de zona única, a redundância de zona fornece durabilidade total de dados com RPO = 0 e disponibilidade com RTO = 0.

A redundância de zona é uma *funcionalidade suplementar* para o recurso de [replicação de vários mestres](how-to-multi-master.md) . A redundância de zona sozinha não pode ser confiável para atingir a resiliência regional. Por exemplo, no caso de interrupções regionais ou acesso de baixa latência em todas as regiões, é aconselhável ter várias regiões de gravação além da redundância de zona.

Ao configurar gravações de várias regiões para sua conta do Azure Cosmos, você pode aceitar a redundância de zona sem custo adicional. Caso contrário, consulte a observação abaixo sobre os preços do suporte à redundância de zona. Você pode habilitar a redundância de zona em uma região existente da sua conta do Azure Cosmos removendo a região e adicionando-a novamente com a redundância de zona habilitada.

Esse recurso está disponível nas seguintes regiões do Azure:

- Sul do Reino Unido

- Sudeste Asiático

- Leste dos EUA

- Leste dos EUA 2

- Centro dos EUA

- Europa Ocidental

- Oeste dos EUA 2

- Leste da Austrália

> [!NOTE]
> A habilitação de Zonas de Disponibilidade para uma única conta do Azure Cosmos de região resultará em encargos equivalentes à adição de uma região adicional à sua conta. Para obter detalhes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) e o custo de [várias regiões em artigos Azure Cosmos DB](optimize-cost-regions.md) .

A tabela a seguir resume a capacidade de alta disponibilidade de várias configurações de conta:

|KPI  |Região única sem Zonas de Disponibilidade (não AZ)  |Região única com Zonas de Disponibilidade (AZ)  |Gravações de várias regiões com Zonas de Disponibilidade (AZ, 2 regiões) – configuração mais recomendada |
|---------|---------|---------|---------|
|SLA de disponibilidade de gravação | 99,99% | 99,99% | 99,999% |
|SLA de leitura de disponibilidade  | 99,99% | 99,99% | 99,999% |
|Price | Taxa de cobrança de região única | Taxa de cobrança da zona de disponibilidade de região única | Taxa de cobrança de várias regiões |
|Falhas de zona – perda de dados | Perda de dados | Sem perda de dados | Sem perda de dados |
|Falhas de zona – disponibilidade | Perda de disponibilidade | Sem perda de disponibilidade | Sem perda de disponibilidade |
|Latência de leitura | Entre regiões | Entre regiões | Baixo |
|Latência de gravação | Entre regiões | Entre regiões | Baixo |
|Interrupção regional – perda de dados | Perda de dados |  Perda de dados | Perda de dados <br/><br/> Ao usar a consistência de desatualização limitada com vários mestres e mais de uma região, a perda de dados é limitada à desatualização limitada configurada em sua conta <br /><br />Você pode evitar a perda de dados durante uma interrupção regional Configurando uma forte consistência com várias regiões. Essa opção vem com compensações que afetam a disponibilidade e o desempenho. Ele pode ser configurado somente em contas configuradas para gravações de região única. |
|Interrupção regional – disponibilidade | Perda de disponibilidade | Perda de disponibilidade | Sem perda de disponibilidade |
|Produtividade | Taxa de transferência provisionada X RU/s | Taxa de transferência provisionada X RU/s | taxa de transferência provisionada de 2 RU/s <br/><br/> Esse modo de configuração requer duas vezes a quantidade de taxa de transferência quando comparado a uma única região com Zonas de Disponibilidade porque há duas regiões. |

> [!NOTE]
> Para habilitar o suporte à zona de disponibilidade para uma conta do Azure Cosmos de várias regiões, a conta deve ter gravações de vários mestres habilitadas.

Você pode habilitar a redundância de zona ao adicionar uma região a contas novas ou existentes do Azure Cosmos. Para habilitar a redundância de zona em sua conta do Azure Cosmos, você deve `isZoneRedundant` definir o `true` sinalizador como para um local específico. Você pode definir esse sinalizador dentro da propriedade Locations. Por exemplo, o seguinte trecho do PowerShell habilita a redundância de zona para a região "sudeste asiático":

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
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
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Você pode habilitar Zonas de Disponibilidade usando portal do Azure ao criar uma conta do Azure Cosmos. Ao criar uma conta, certifique-se de habilitar a **redundância geográfica**, as **gravações de várias regiões**e escolher uma região onde zonas de disponibilidade têm suporte:

![Habilitar Zonas de Disponibilidade usando portal do Azure](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Criando aplicativos altamente disponíveis

- Para garantir a alta disponibilidade de leitura e gravação, configure sua conta do Cosmos para abranger pelo menos duas regiões com várias regiões de gravação. Essa configuração fornecerá a mais alta disponibilidade, a menor latência e a melhor escalabilidade para leituras e gravações apoiadas por SLAs. Para saber mais, confira como [configurar sua conta do Cosmos com várias regiões de gravação](tutorial-global-distribution-sql-api.md).

- Para contas do Cosmos de várias regiões configuradas com uma única região de gravação, [habilite o failover automático usando a CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#automatic-failover). Depois de habilitar o failover automático, sempre que houver um desastre regional, o Cosmos DB fará o failover da sua conta automaticamente.  

- Mesmo que sua conta do Azure Cosmos esteja altamente disponível, seu aplicativo pode não ter sido projetado corretamente para permanecer altamente disponível. Para testar a alta disponibilidade de ponta a ponta de seu aplicativo, como parte de seus testes de aplicativo ou de recuperação de desastre (DR), desabilitar temporariamente o failover automático para a conta, invocar o [failover manual usando o PowerShell, CLI do Azure ou portal do Azure](how-to-manage-database-account.md#manual-failover), em seguida, monitorar o failover do aplicativo. Uma vez concluído, você pode fazer failover para a região primária e restaurar o failover automático para a conta.

- Em um ambiente de banco de dados distribuído globalmente, há uma relação direta entre o nível de consistência e a durabilidade dos dados na presença de uma interrupção em toda a região. À medida que você vai desenvolvendo o plano de continuidade dos negócios, precisará saber qual é o tempo máximo aceitável antes que o aplicativo se recupere completamente após um evento de interrupção. O tempo necessário para o aplicativo se recuperar totalmente é conhecido como RTO (objetivo de tempo de recuperação). Também é necessário saber o período máximo de atualizações de dados recentes que o aplicativo pode perder sem maiores problemas durante a recuperação após um evento de interrupção. O período de tempo de atualizações que você pode perder é conhecido como RPO (objetivo de ponto de recuperação). Para ver o RTO e o RPO do Azure Cosmos DB, confira [Níveis de consistência e durabilidade dos dados](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Próximas etapas

Em seguida, você poderá ler os artigos a seguir:

- [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
- [Taxa de transferência provisionada para dimensionamento global](scaling-throughput.md)
- [Distribuição global - nos bastidores](global-dist-under-the-hood.md)
- [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
- [Como configurar sua conta do cosmos com várias regiões de gravação](how-to-multi-master.md)
