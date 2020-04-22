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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688069"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Alta disponibilidade com o Azure Cosmos DB

O Azure Cosmos DB replica de forma transparente seus dados em todas as regiões do Azure associadas à sua conta do Cosmos. O Cosmos DB utiliza várias camadas de redundância para seus dados, como mostra a imagem a seguir:

![Particionamento físico](./media/high-availability/cosmosdb-data-redundancy.png)

- Os dados nos contêineres cosmos são [horizontalmente particionados](partitioning-overview.md).

- Dentro de cada região, toda partição é protegida por um conjunto de réplicas com todas as gravações replicadas e confirmadas permanentemente pela maioria das réplicas. As réplicas são distribuídas em até 10 a 20 domínios de falha.

- Em todas as regiões, cada partição é replicada. Cada região contém todas as partições de dados de um contêiner do Cosmos e pode aceitar gravações e fornecer leituras.  

Se sua conta Cosmos for distribuída em regiões *do N* Azure, haverá pelo menos *N* x 4 cópias de todos os seus dados. Além de fornecer acesso a dados de baixa latência e dimensionamento do throughput de gravação/leitura em todas as regiões associadas à sua conta Cosmos, ter mais regiões *(N*mais altas ) melhora ainda mais a disponibilidade.  

## <a name="slas-for-availability"></a>SLAs de disponibilidade

Como um banco de dados distribuído globalmente, o Cosmos DB fornece SLAs abrangentes que englobam a taxa de transferência, a latência no 99º percentil, a coerência e a alta disponibilidade. A tabela abaixo mostra as garantias de alta disponibilidade fornecidas pelo Cosmos DB para contas de uma e várias regiões. Para alta disponibilidade, configure sempre suas contas Cosmos para ter várias regiões de gravação.

|Tipo de operação  | Região única |Várias regiões (gravações de região única)|Várias regiões (gravações de várias regiões) |
|---------|---------|---------|-------|
|Gravações    | 99,99    |99,99   |99,999|
|Leituras     | 99,99    |99,999  |99,999|

> [!NOTE]
> Na prática, a disponibilidade real de gravação para obsoletos, sessões, prefixo consistente e eventuais modelos de consistência é significativamente maior do que os SLAs publicados. A disponibilidade de leitura real para todos os níveis de coerência é significativamente mais alta do que os SLAs publicados.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Alta disponibilidade com Cosmos DB em caso de interrupções regionais

Interrupções regionais não são incomuns e o Azure Cosmos DB garante que seu banco de dados esteja sempre altamente disponível. Os detalhes a seguir capturam o comportamento do Cosmos DB durante uma paralisação, dependendo da configuração da sua conta Cosmos:

- Com o Cosmos DB, antes que uma operação de gravação seja confirmada para o cliente, os dados serão confirmados por um quorum de réplicas dentro da região que aceitará as operações de gravação.

- Contas de várias regiões configuradas com várias regiões de gravação estarão altamente disponíveis para leituras e gravações. Failovers regionais são instantâneos e não exigem nenhuma alteração do aplicativo.

- Contas de uma única região poderão perder disponibilidade após uma indisponibilidade regional. É sempre recomendável configurar **pelo menos duas regiões** (de preferência, pelo menos duas regiões de gravação) com sua conta Cosmos para garantir alta disponibilidade em todos os momentos.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Contas de várias regiões com uma região de gravação única (paralisação da região de gravação)

- Durante uma paralisação da região de gravação, a conta Cosmos promoverá automaticamente uma região secundária para ser a nova região de gravação primária quando **ativar failover automático** é configurado na conta Do Azure Cosmos. Quando ativado, o failover ocorrerá para outra região na ordem de prioridade da região especificada.
- Quando a região anteriormente impactada voltar a funcionar, todos os dados de gravação que não foram replicados quando a região falhou, são disponibilizados através do [feed de conflitos](how-to-manage-conflicts.md#read-from-conflict-feed). Os aplicativos podem ler o feed de conflitos, resolver os conflitos com base na lógica específica do aplicativo e gravar os dados atualizados de volta ao contêiner Azure Cosmos conforme apropriado.
- Depois que a região de gravação anteriormente afetada se recupera, ela fica automaticamente disponível como uma região de leitura. Você pode voltar para a região recuperada como a região de gravação. Você pode alternar as regiões usando [o portal PowerShell, Azure CLI ou Azure](how-to-manage-database-account.md#manual-failover). Não **há perda de dados ou disponibilidade** antes, durante ou depois de alternar a região de gravação e seu aplicativo continua altamente disponível.

> [!IMPORTANT]
> É fortemente recomendável configurar as contas do Azure Cosmos usadas para cargas de trabalho de produção para **permitir failover automático**. O failover manual requer conectividade entre a região de gravação secundária e primária para completar uma verificação de consistência para garantir que não haja perda de dados durante o failover. Se a região primária não estiver disponível, essa verificação de consistência não poderá ser concluída e o failover manual não será bem sucedido, resultando em perda de disponibilidade de gravação.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Contas de várias regiões com uma região de gravação única (leia paralisação da região)

- Durante uma paralisação da região de leitura, as contas do Cosmos usando qualquer nível de consistência ou consistência forte com três ou mais regiões de leitura permanecerão altamente disponíveis para leituras e gravações.
- A região impactada é automaticamente desconectada e será marcada offline. Os [SDKs Azure Cosmos DB](sql-api-sdk-dotnet.md) redirecionarão as chamadas de leitura para a próxima região disponível na lista de regiões preferidas.
- Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas retornarão automaticamente à região de gravação atual.
- Não é necessária nenhuma alteração no código do aplicativo para lidar com a interrupção da região de leitura. Quando a região de leitura impactada estiver novamente on-line, ela será sincronizada automaticamente com a região de gravação atual e estará disponível novamente para atender às solicitações de leitura.
- Leituras subsequentes são redirecionadas para a região recuperada sem exigir nenhuma alteração ao código do aplicativo. Durante o failover e a rejunção de uma região anteriormente fracassada, as garantias de consistência de leitura continuam a ser honradas pelo Cosmos DB.

> [!IMPORTANT]
> As contas do Azure Cosmos usando forte consistência com duas ou menos regiões de leitura perderão a disponibilidade de gravação durante uma paralisação da região de leitura, mas manterão a disponibilidade de leitura para regiões restantes.

- Mesmo em um evento raro e infeliz quando a região do Azure é permanentemente irrecuperável, não há perda de dados se sua conta Cosmos de várias regiões estiver configurada com *forte* consistência. No caso de uma região de gravação permanentemente irrecuperável, uma conta Cosmos de várias regiões configurada com consistência de staleness delimitado, a janela de perda de dados potencial é restrita à janela de obsoleto *(K* ou *T)* onde K=100.000 atualizações e T=5 minutos. Para níveis de sessão, prefixo consistente e eventual consistência, a janela de perda de dados potencial é restrita a um máximo de 15 minutos. Para obter mais informações sobre os alvos RTO e RPO para o Azure Cosmos DB, consulte [níveis de consistência e durabilidade dos dados](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>Suporte à Zona de Disponibilidade

Além da resiliência entre as regiões, agora você pode habilitar **a redundância de região** ao selecionar uma região para se associar ao seu banco de dados Do Azure Cosmos.

Com o suporte à Zona de Disponibilidade, o Azure Cosmos DB garantirá que as réplicas sejam colocadas em várias regiões dentro de uma determinada região para fornecer alta disponibilidade e resiliência durante falhas zonais. Não há alterações na latência e em outros SLAs nesta configuração. No caso de uma falha de uma única zona, a redundância da zona fornece durabilidade total dos dados com RPO=0 e disponibilidade com RTO=0.

A redundância de região é uma *capacidade suplementar* para o recurso [de replicação multi-master.](how-to-multi-master.md) A redundância da zona, por si só, não pode ser confiada para alcançar a resiliência regional. Por exemplo, no caso de paralisações regionais ou baixo acesso à latência em todas as regiões, é aconselhável ter várias regiões de gravação, além de redundância de zona.

Ao configurar gravações de várias regiões para sua conta do Azure Cosmos, você pode optar pela redundância de zona sem custo adicional. Caso contrário, consulte a nota abaixo sobre os preços para suporte à redundância de zona. Você pode habilitar a redundância de região em uma região existente de sua conta Do Azure Cosmos removendo a região e adicionando-a de volta com a redundância de região ativada.

Este recurso está disponível nas seguintes regiões do Azure:

- Sul do Reino Unido

- Sudeste Asiático

- Leste dos EUA

- Leste dos EUA 2

- Centro dos EUA

- Europa Ocidental

- Oeste dos EUA 2

- Leste da Austrália

> [!NOTE]
> Habilitar zonas de disponibilidade para uma única região A conta Do Azure Cosmos resultará em encargos equivalentes a adicionar uma região adicional à sua conta. Para obter detalhes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) e o custo de várias regiões nos artigos do [Azure Cosmos DB.](optimize-cost-regions.md)

A tabela a seguir resume a capacidade de alta disponibilidade de várias configurações da conta:

|KPI  |Região única sem Zonas de Disponibilidade (Não-AZ)  |Região única com Zonas de Disponibilidade (AZ)  |Gravações multi-regiões com Zonas de Disponibilidade (AZ, 2 regiões) – Configuração mais recomendada |
|---------|---------|---------|---------|
|SLA de disponibilidade de gravação | 99,99% | 99,99% | 99,999% |
|Leia a disponibilidade de SLA  | 99,99% | 99,99% | 99,999% |
|Price | Taxa de faturamento de uma única região | Taxa de faturamento da Zona de Disponibilidade de região única | Taxa de faturamento multi-região |
|Falhas na zona – perda de dados | Perda de dados | Sem perda de dados | Sem perda de dados |
|Falhas na região – disponibilidade | Perda de disponibilidade | Sem perda de disponibilidade | Sem perda de disponibilidade |
|Leia latência | Região transversal | Região transversal | Baixo |
|Escrever latência | Região transversal | Região transversal | Baixo |
|Paralisação regional – perda de dados | Perda de dados |  Perda de dados | Perda de dados <br/><br/> Ao usar a consistência de obsoleto limitado com multi mestre e mais de uma região, a perda de dados é limitada à obsoleto limitado configurada em sua conta <br /><br />Você pode evitar a perda de dados durante uma paralisação regional configurando forte consistência com várias regiões. Essa opção vem com trade-offs que afetam a disponibilidade e o desempenho. Ele só pode ser configurado em contas configuradas para gravações de uma região única. |
|Paralisação regional – disponibilidade | Perda de disponibilidade | Perda de disponibilidade | Sem perda de disponibilidade |
|Produtividade | X RU/s provisionado throughput | X RU/s provisionado throughput | 2X RU/s provisionado throughput <br/><br/> Este modo de configuração requer o dobro da quantidade de throughput quando comparado a uma única região com Zonas de Disponibilidade porque existem duas regiões. |

> [!NOTE]
> Para habilitar o suporte à Zona de Disponibilidade para uma conta Azure Cosmos de várias regiões, a conta deve ter gravações multi-master ativadas.

Você pode habilitar a redundância de região ao adicionar uma região a contas azure Cosmos novas ou existentes. Para habilitar a redundância de região em sua conta `isZoneRedundant` do `true` Azure Cosmos, você deve definir o sinalizador para um local específico. Você pode definir esta bandeira dentro da propriedade de locais. Por exemplo, o seguinte trecho do PowerShell permite a redundância da zona para a região "Sudeste Asiático":

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

O comando a seguir mostra como ativar a redundância de zona para as regiões "EastUS" e "WestUS2":

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Você pode habilitar Zonas de Disponibilidade usando o portal Azure ao criar uma conta do Azure Cosmos. Ao criar uma conta, **certifique-se de**ativar a geo-redundância , **Gravações multi-regiões**e escolher uma região onde as Zonas de Disponibilidade são suportadas:

![Habilitar zonas de disponibilidade usando o portal Azure](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Criando aplicativos altamente disponíveis

- Para garantir a alta disponibilidade de leitura e gravação, configure sua conta do Cosmos para abranger pelo menos duas regiões com várias regiões de gravação. Essa configuração fornecerá a maior disponibilidade, menor latência e melhor escalabilidade para leituras e gravações apoiadas por SLAs. Para saber mais, confira como [configurar sua conta do Cosmos com várias regiões de gravação](tutorial-global-distribution-sql-api.md).

- Para contas do Cosmos de várias regiões configuradas com uma única região de gravação, [habilite o failover automático usando a CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#automatic-failover). Depois de habilitar o failover automático, sempre que houver um desastre regional, o Cosmos DB fará o failover da sua conta automaticamente.  

- Mesmo que sua conta do Azure Cosmos esteja altamente disponível, seu aplicativo pode não estar corretamente projetado para permanecer altamente disponível. Para testar a alta disponibilidade de ponta a ponta do seu aplicativo, como parte das brocas de teste de aplicativos ou recuperação de desastres (DR), desabilite temporariamente o failover automático para a conta, invoque o [failover manual usando o portal PowerShell, Azure CLI ou Azure,](how-to-manage-database-account.md#manual-failover)e monitore o failover do aplicativo. Uma vez concluído, você pode falhar de volta para a região principal e restaurar failover automático para a conta.

- Dentro de um ambiente de banco de dados distribuído globalmente, há uma relação direta entre o nível de consistência e a durabilidade dos dados na presença de uma paralisação em toda a região. À medida que você vai desenvolvendo o plano de continuidade dos negócios, precisará saber qual é o tempo máximo aceitável antes que o aplicativo se recupere completamente após um evento de interrupção. O tempo necessário para o aplicativo se recuperar totalmente é conhecido como RTO (objetivo de tempo de recuperação). Também é necessário saber o período máximo de atualizações de dados recentes que o aplicativo pode perder sem maiores problemas durante a recuperação após um evento de interrupção. O período de tempo de atualizações que você pode perder é conhecido como RPO (objetivo de ponto de recuperação). Para ver o RTO e o RPO do Azure Cosmos DB, confira [Níveis de consistência e durabilidade dos dados](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Próximas etapas

Em seguida, você poderá ler os artigos a seguir:

- [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
- [Taxa de transferência provisionada para dimensionamento global](scaling-throughput.md)
- [Distribuição global - nos bastidores](global-dist-under-the-hood.md)
- [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
- [Como configurar sua conta Cosmos com várias regiões de gravação](how-to-multi-master.md)
