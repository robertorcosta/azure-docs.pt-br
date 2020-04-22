---
title: Perguntas frequentes sobre a API azure Cosmos DB para Cassandra.
description: Obtenha respostas para perguntas frequentes sobre a API Azure Cosmos DB para Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 416f0c5f995a101298e84c81317c7d39fb5d43f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727379"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-db-api-for-cassandra"></a>Perguntas frequentes sobre a API Azure Cosmos DB para Cassandra

## <a name="what-are-some-key-differences-between-apache-cassandra-and-cassandra-api"></a>Quais são algumas diferenças importantes entre a Apache Cassandra e a API cassandra?

- Apache Cassandra recomenda um limite de 100 MB no tamanho de uma chave de partição. A API cassandra permite até 10GB por partição.
- Apache Cassandra permite que você desabilite compromissos duráveis - ou seja, pule a escrita para o registro de confirmação e vá diretamente para o Memtable(s). Isso pode levar à perda de dados se o nó diminuir antes de memtables serem liberados para SStables no disco. Cosmos DB sempre faz compromissos duráveis para que você nunca tenha perda de dados.
- Apache Cassandra pode ver desempenho reduzido se a carga de trabalho envolve um monte de substituições e/ou exclusões. A razão para isso são lápides que a carga de trabalho lida precisa pular para buscar os dados mais recentes. A API cassandra não verá o desempenho de leitura diminuído quando a carga de trabalho tiver muitas substituições e/ou exclusões.
- Durante cenários de alta substituição de carga de trabalho, a compactação precisa ser executada para mesclar SSTables no disco (a fusão é necessária porque as gravações do Apache Cassandra são apenas anexadas, assim, várias atualizações são armazenadas como entradas SSTable individuais que precisam ser periodicamente mescladas). Isso também pode levar a um desempenho de leitura reduzido durante a compactação. Isso não ocorre na API cassandra, pois não implementa compactação.
- Definir um fator de replicação de 1 é possível com Apache Cassandra. No entanto, leva à baixa disponibilidade se o único nó com os dados cair. Este não é um problema com a API Azure Cosmos DB Cassandra porque há sempre um fator de replicação de 4 (quórum de 3).
- Adicionar/remover nódulos no Apache Cassandra requer muita intervenção manual, mas também alta CPU no novo nó enquanto os nódulos existentes movem alguns de seus intervalos de tokens para o novo nó. Isso é o mesmo ao desativar um nó existente. No entanto, a escala ção é feita perfeitamente sob o capô na API Azure Cosmos DB Cassandra, sem quaisquer problemas observados no serviço/aplicativo.
- Não há necessidade de definir num_tokens em cada nó no aglomerado como em Apache Cassandra. Os nós e os intervalos de tokens são totalmente gerenciados pelo Cosmos DB.
- A azure Cosmos DB Cassandra API é totalmente gerenciada para que você não exija os comandos de nó, como reparo, descomissionamento etc. que são usados em Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Outras perguntas frequentes

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Qual é a versão de protocolo suportada pela API Azure Cosmos DB Cassandra? Existe um plano para oferecer suporte a outros protocolos?

A azure Cosmos DB Cassandra API suporta cql versão 3.x. Sua compatibilidade cql é baseada no [repositório público Apache Cassandra GitHub.](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile) Se você tiver comentários sobre o suporte a outros protocolos, conte-nos por meio dos [comentários da voz do usuário](https://feedback.azure.com/forums/263030-azure-cosmos-db) ou envie um email para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Por que é necessário escolher uma taxa de transferência para uma tabela?

O Azure Cosmos DB define a taxa de transferência padrão para seu contêiner com base em onde você cria a tabela - portal ou CQL.
O Azure Cosmos DB fornece garantias de desempenho e latência com limites superiores na operação. Essa garantia é possível quando o mecanismo pode impor a governança nas operações do locatário. Configurar a taxa de transferência assegura que você obtenha taxa de transferência e latência garantidas, pois a plataforma reserva essa capacidade e garante o sucesso da operação.
Você pode [mudar elasticamente o throughput](manage-scale-cassandra.md) para se beneficiar da sazonalidade de sua aplicação e economizar custos.

O conceito de taxa de transferência é explicado no artigo [Unidades de solicitação no Azure Cosmos DB](request-units.md). A taxa de transferência para uma tabela é distribuída igualmente entre as partições físicas subjacentes.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Qual é a RU/s padrão de uma tabela criada por meio de CQL? E se eu precisar alterá-la?

O Azure Cosmos DB usa unidades de solicitação por segundo (RU/s) como moeda para fornecer a taxa de transferência. As tabelas criadas por meio de CQL têm 400 RU. Você pode alterar a RU do portal.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>O que acontece quando a taxa de transferência é usada?

O Azure Cosmos DB fornece garantias de desempenho e latência com limites superiores na operação. Essa garantia é possível quando o mecanismo pode impor a governança nas operações do locatário. Isso é possível ao configurar a taxa de transferência, o que assegura que você obterá taxa de transferência e latência garantidas, pois a plataforma reserva essa capacidade e garante o sucesso da operação.
Ao exceder essa capacidade, você recebe uma mensagem de erro de sobrecarregamento indicando que sua capacidade foi usada.
0x1001 Sobrecarregado: a solicitação não pode ser processada porque "A taxa de solicitação é grande". Nesse momento, é essencial ver quais operações e respectivos volumes causam esse problema. Você pode ter uma ideia da capacidade consumida que excede a capacidade provisionada com a métrica no portal. Em seguida, você precisa garantir que a capacidade seja consumida de forma quase igual entre todas as partições subjacentes. Se você vir que a maior parte da taxa de transferência é consumida por uma partição, significa que há distorção de carga de trabalho.

Há uma métrica disponível para mostrar como a taxa de transferência é usada ao longo de horas, dias e por sete dias, nas partições ou no todo. Para obter mais informações, consulte [Monitoramento e depuração com métricas no Azure Cosmos DB](use-metrics.md).

Logs de diagnóstico são explicados no artigo [Logs de diagnóstico do Azure Cosmos DB](logging.md).

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>A chave primária mapeia para o conceito de chave de partição do Azure Cosmos DB?

Sim, a chave de partição é usada para colocar a entidade no local correto. No Azure Cosmos DB, ela é usada para localizar a partição lógica correta que é armazenada em uma partição física. O conceito de particionamento também é explicado no artigo [Partição e escala no Azure Cosmos DB](partition-data.md). A questão essencial aqui é que uma partição lógica não deve exceder o limite de 10 GB hoje.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>O que acontece quando recebo uma notificação de “cota cheia” indicando que uma partição está cheia?

O Azure Cosmos DB é um sistema baseado em SLA que fornece escala ilimitada com garantias de latência, produtividade, disponibilidade e consistência. Esse armazenamento ilimitado baseia-se na escala horizontal de dados usando o particionamento como o conceito chave. O conceito de particionamento também é explicado no artigo [Partição e escala no Azure Cosmos DB](partition-data.md).

Você deve respeitar o limite de 10 GB no número de entidades ou itens por partição lógica. Para garantir que seu aplicativo dimensiona bem, recomendamos que você *não* crie uma partição ativa armazenando todas as informações em uma partição e consultando-a. Esse erro poderá apenas surgir se os dados forem distorcidos: ou seja, muitos dados para uma chave de partição (mais do que 10&nbsp;GB). Você pode localizar a distribuição de dados usando o portal de armazenamento. Uma maneira de corrigir esse erro é recriando a tabela e escolhendo um primário granular (chave de partição), que permita uma melhor distribuição de dados.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>É possível usar a API do Cassandra como armazenamento de valor de chave com milhões ou bilhões de chaves de partições individuais?

O Azure Cosmos DB pode armazenar dados ilimitados expandindo o armazenamento. Isso é independente da taxa de transferência. Sim, sempre há a opção de usar a API do Cassandra para armazenar e recuperar  chave/valores especificando a chave primária/de partição correta. Essas chaves individuais possuem sua própria partição lógica e permanecem acima da partição física sem problemas.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>É possível criar mais de uma tabela com a API do Apache Cassandra do Azure Cosmos DB?

Sim, é possível criar mais de uma tabela com a API do Apache Cassandra. Cada uma dessas tabelas é tratada como unidade de taxa de transferência e armazenamento.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>É possível criar mais de uma tabela em sucessão?

O Azure Cosmos DB é um sistema de recursos administrado para atividades de plano de dados e controle. Contêineres como coleções e tabelas são entidades de runtime provisionadas para uma determinada capacidade de taxa de transferência. A criação desses contêineres em sucessão rápida não é uma atividade esperada e limitada. Se você tiver testes que removem/criam tabelas imediatamente, tente espaçá-los.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Qual é a quantidade máxima de tabelas que pode ser criada?

Não há limite físico no número de tabelas, envie um e-mail se [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) você tiver um grande número de tabelas (onde o tamanho total estável ultrapassa 10 TB de dados) que precisam ser criadas a partir dos 10 ou 100 usuais.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Qual é o número máximo de keyspace que podemos criar?

Não há limite físico no número de espaços-chave, pois são contêineres de metadados, envie um e-mail [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) se você tiver um grande número de espaços-chave por algum motivo.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>É possível colocar em uma grande quantidade de dados depois de iniciar de uma tabela normal?

Sim, assumindo partições distribuídas uniformemente, a capacidade de armazenamento é gerenciada automaticamente e aumenta à medida que você empurra mais dados. Portanto, você pode importar com confiança a quantidade de dados que precisar sem o gerenciamento e provisionamento de nós e muito mais. No entanto, se você está antecipando um monte de crescimento imediato de dados, faz mais sentido provisão diretamente [para o throughput antecipado](set-throughput.md) em vez de começar mais baixo e aumi-lo imediatamente.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>É possível fornecer configurações de arquivo yaml para configurar o comportamento da API Casssandra do Apache do Azure Cosmos DB?

A API do Apache Cassandra do Azure Cosmos DB é um serviço de plataforma. Ela fornece compatibilidade em nível de protocolo para a execução de operações. Ela oculta a complexidade de gerenciamento, monitoramento e configuração. Como desenvolvedor/usuário, você não precisa se preocupar quanto à disponibilidade, marcas de exclusão, cache de chaves, cache de linhas, filtro de bloom e uma variedade de outras configurações. A API do Apache Cassandra do Azure Cosmos DB se concentra em fornecer o desempenho de leitura e gravação de que você precisa sem a sobrecarga de configuração e gerenciamento.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>A API do Apache Cassandra do Azure Cosmos DB oferece suporte a comandos de adição de nós/cluster de nós/status de nós?

A API do Apache Cassandra é um serviço de plataforma que facilita o planejamento de capacidade, a resposta às demandas de elasticidade para taxa de transferência e o armazenamento. Com o Azure Cosmos DB, você provisiona a taxa de transferência de que precisa. Depois é possível escalá-la para cima e para baixo quantas vezes quiser por dia sem se preocupar com adição/exclusão de nós ou em gerenciá-los. Isso implica que você não precisa usar o nó, nem a ferramenta de gerenciamento de cluster.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>O que acontece em relação a várias definições de configuração para a criação de keyspaces como simples/rede?

O Azure Cosmos DB fornece distribuição global imediata por motivos de baixa latência e disponibilidade. Você não precisa instalar réplicas ou outras coisas. Todas as gravações são sempre duradouras quórum cometido em qualquer região onde você escreve enquanto fornece garantias de desempenho.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>O que acontece em relação a várias configurações para metadados de tabela, como filtro de bloom, cache, alteração de reparo de leitura, gc_grace, compactação memtable_flush_period, etc.?

O Azure Cosmos DB fornece desempenho para leituras/gravações e taxa de transferência sem a necessidade de mexer nas definições de configuração e acidentalmente manipulá-las.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Há suporte para o tempo de vida (TTL) para tabelas Cassandra?

Sim, há suporte para TTL.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>É possível monitorar status do nó, status de réplica, gc e parâmetros de sistema operacional mais cedo com diversas ferramentas? O que precisa ser monitorado agora?

O Azure Cosmos DB é um serviço de plataforma que ajuda a aumentar a produtividade e não se preocupar com o gerenciamento e monitoramento da infraestrutura. Você precisa cuidar da taxa de transferência que está disponível na métrica do portal para saber se está sendo restringido e aumentar ou diminuir essa taxa de transferência.
Monitora [SLAs](monitor-accounts.md).
Usar [Métrica](use-metrics.md) Usar [Logs de diagnóstico](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Quais SDKs de cliente podem funcionar com a API do Apache Cassandra do Azure Cosmos DB?

Os drivers clientes da Apache Cassandra SDK que usam CQLv3 foram usados para programas de clientes. Se você tiver outros drivers que você usa ou se [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)estiver enfrentando problemas, envie e-mail para .

### <a name="is-composite-partition-key-supported"></a>Há suporte para chave de partição de composição?

Sim, é possível usar a sintaxe regular para criar uma chave de partição de composição.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Posso usar sstableloader para carregamento de dados?

Não, sstableloader não é suportado.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Um grupo apache Cassandra pode ser emparelhado com a API Cassandra do Azure Cosmos DB?

No momento, o Azure Cosmos DB tem uma experiência otimizada para o ambiente em nuvem sem a sobrecarga de operações. Se você precisar de emparelhamento, envie e-mail para [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) com uma descrição do seu cenário. Estamos trabalhando na oferta para ajudar a emparelhar o cluster Cassandra no local/nuvem diferente para a API Cassandra da Cosomos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>A API do Cassandra fornece backups completos?

O Azure Cosmos DB hoje fornece dois backups completos gratuitos realizados em intervalos de quatro horas em todas as APIs. Isso garante que você não precise configurar uma agenda de backup e outras coisas.
Se você quiser modificar a retenção [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) e a frequência, envie um e-mail para ou levante um caso de suporte. As informações sobre a funcionalidade de backup são fornecidas no artigo [Backup e restauração online automáticos com o Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md).

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Como uma conta da API Cassandra lida com o failover caso uma região fique inativa?

A API Cassandra do Azure Cosmos DB pega emprestado da plataforma distribuída globalmente do Azure Cosmos DB. Para garantir que seu aplicativo pode tolerar o tempo de inatividade do data center, habilite pelo menos mais uma região para a conta no Portal do Azure Cosmos DB [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](high-availability.md). Defina a prioridade da região usando o portal [Desenvolvendo com contas em várias regiões do Azure Cosmos DB](high-availability.md).

Você pode adicionar quantas regiões quiser para a conta e controlar para onde ela pode fazer o failover fornecendo uma prioridade de failover. Para usar o banco de dados, você precisa fornecer um aplicativo lá também. Quando você fizer isso, os clientes não terão o tempo de inatividade.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>A API Cassandra indexa todos os atributos de uma entidade por padrão?

Não. A API de Cassandra suporta [índices secundários,](cassandra-secondary-index.md)que se comportam de forma muito semelhante à Apache Cassandra. Ele não indexa todos os atributos por padrão.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Posso usar o novo SDK da API Cassandra localmente com o emulador?

Sim, isso é suportado. Você pode encontrar detalhes de como habilitar isso [aqui](local-emulator.md#cassandra-api)


### <a name="how-can-i-migrate-data-from-their-apache-cassandra-clusters-to-cosmos-db"></a>Como posso migrar dados de seus grupos Apache Cassandra para Cosmos DB?

Você pode ler sobre opções de migração [aqui](cassandra-import-data.md).


### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Recurso X da API do Apache Cassandra comum não está funcionando hoje, onde posso deixar comentários a respeito?

Deixe comentários por meio dos [comentários da voz do usuário](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>Próximas etapas

- Comece com [o dimensionamento elástico de uma conta API Azure Cosmos DB Cassandra](manage-scale-cassandra.md).
