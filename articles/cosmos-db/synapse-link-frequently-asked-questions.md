---
title: Perguntas frequentes sobre o Link do Azure Synapse para Azure Cosmos DB
description: Obtenha respostas para perguntas frequentes sobre o Link do Synapse para Azure Cosmos DB em áreas como cobrança, repositório analítico, segurança, tempo de vida no repositório analítico.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: b2fc13158d197aaa2e870d1b772386628ee3f9bc
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398794"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Perguntas frequentes sobre o Link do Azure Synapse para Azure Cosmos DB

O Link do Azure Synapse para Azure Cosmos DB cria uma integração perfeita entre o Azure Cosmos DB e o Azure Synapse Analytics. Ele permite que os clientes executem análises quase em tempo real em seus dados operacionais com isolamento de desempenho total de suas cargas de trabalho transacionais e sem um pipeline ETL. Este artigo responde perguntas frequentes sobre o Link do Synapse para Azure Cosmos DB.

## <a name="general-faq"></a>Perguntas frequentes gerais

### <a name="is-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Há suporte para o Link do Synapse para todas as APIs do Azure Cosmos DB?

Na versão de visualização pública, há suporte para o link Synapse para a API Azure Cosmos DB SQL (Core) e para a API Azure Cosmos DB para MongoDB. 

### <a name="is-synapse-link-supported-for-multi-region-azure-cosmos-accounts"></a>Há suporte para o Link do Synapse para contas do Azure Cosmos de várias regiões?

Sim, para contas de várias regiões do Azure Cosmos, os dados armazenados no repositório analítico também são distribuídos globalmente. Independentemente de região de gravação única (mestre único) ou de várias regiões de gravação (também conhecidas como vários mestres), as consultas analíticas executadas no Azure Synapse Analytics podem ser entregues a partir da região local mais próxima.

Quando planejar a configuração de uma conta do Azure Cosmos de várias regiões com suporte ao repositório analítico, é recomendável ter todas as regiões necessárias adicionadas no momento da criação da conta.

### <a name="can-i-choose-to-enable-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Posso optar por habilitar o Link do Synapse para apenas determinada região e não todas as regiões em uma configuração de conta de várias regiões?

Na versão de visualização, quando o Link do Synapse está habilitado para uma conta de várias regiões, o repositório analítico é criado em todas as regiões. Os dados subjacentes são otimizados para consistência de taxa de transferência e transacional no repositório transacional.

### <a name="is-backup-and-restore-supported-for-synapse-link-enabled-accounts"></a>O backup e a restauração têm suporte para contas habilitadas para o Synapse link?

Na visualização, para contas de banco de dados habilitadas para o Synapse link, não há suporte para backup e restauração de contêineres. Se você tiver cargas de trabalho de produção que exigem a funcionalidade de backup e restauração, recomendamos não habilitar o link Synapse nessas contas de banco de dados. 

### <a name="can-i-disable-the-synapse-link-feature-for-my-azure-cosmos-account"></a>Posso desabilitar o recurso de Link do Synapse para minha conta do Azure Cosmos?

Atualmente, depois que a funcionalidade de Link do Synapse é habilitada no nível da conta, você não poderá desabilitá-la.  Se você quiser desativar essa funcionalidade, será necessário excluir e recriar uma nova conta do Azure Cosmos.

Lembre-se que **não** há nenhuma implicação de cobrança para você se o recurso de Link do Synapse estiver habilitado no nível da conta, mas não houver contêineres habilitados para repositório analítico.

## <a name="azure-cosmos-db-analytical-store"></a>Repositório analítico do Azure Cosmos DB

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Posso habilitar o repositório analítico em contêineres existentes?

No momento, você pode habilitar o repositório analítico apenas para novos contêineres (em contas novas e existentes).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-containers-after-enabling-it-during-container-creation"></a>Posso desabilitar o repositório analítico nos meus contêineres do Azure Cosmos depois de habilitá-lo durante a criação do contêiner?

Atualmente, o repositório analítico não pode ser desabilitado em um contêiner do Azure Cosmos após ser habilitado durante a criação do contêiner.

### <a name="is-analytical-store-supported-for-azure-cosmos-containers-with-autoscale-provisioned-throughput"></a>O repositório analítico tem suporte para contêineres do Azure Cosmos com taxa de transferência provisionada de dimensionamento automático?

Sim, o repositório analítico pode ser habilitado em contêineres com taxa de transferência provisionada de dimensionamento automático.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Há algum efeito nas RUs provisionadas do repositório transacional do Azure Cosmos DB?

O Azure Cosmos DB garante o isolamento de desempenho entre as cargas de trabalho transacionais e analíticas. Habilitar o repositório analítico em um contêiner não afetará as RU/s provisionadas no repositório transacional do Azure Cosmos DB. As transações (leitura e gravação) e os custos de armazenamento para o repositório analítico serão cobrados separadamente. Consulte os [preços para o repositório analítico do Azure Cosmos DB](analytical-store-introduction.md#analytical-store-pricing) para obter mais detalhes.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>As operações de exclusão e atualização no repositório transacional se refletem no repositório analítico?

Sim, as operações de exclusão e atualização dos dados no repositório transacional serão refletidas no repositório analítico. Você pode configurar o tempo de vida (TTL) no contêiner para incluir dados históricos, de forma que o repositório analítico mantenha todas as versões de itens que atendam aos critérios de TTL analítico. Consulte a [visão geral sobre TTL analítico](analytical-store-introduction.md#analytical-ttl) para saber mais detalhes.

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Posso me conectar ao repositório analítico a partir de mecanismos de análise diferentes do Azure Synapse Analytics?

Você só pode acessar e executar consultas no repositório analítico usando os vários tempos de execução fornecidos pelo Azure Synapse Analytics. O repositório analítico pode ser consultado e analisado usando:

* Synapse Spark com suporte completo para Scala, Python, SparkSQL e C#. O Synapse Spark é fundamental para cenários de engenharia de dados e científicos
* O SQL sem servidor com linguagem T-SQL e suporte para ferramentas de BI familiares (por exemplo, Power BI Premium etc.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>Posso me conectar ao repositório analítico a partir do SQL do Synapse provisionado?

Neste momento, o repositório analítico não pode ser acessado a partir do SQL do Synapse provisionado.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Posso fazer write-back dos resultados da agregação de consulta do Synapse de volta para o repositório analítico?

O repositório analítico é um repositório somente de leitura em um contêiner do Azure Cosmos. Portanto, você não pode fazer write-back diretamente dos resultados de agregação no repositório analítico, mas pode gravá-los no repositório transacional do Azure Cosmos DB de outro contêiner, que posteriormente podem ser aproveitados como uma camada de serviço.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>A replicação de sincronização automática do repositório transacional para o repositório analítico é assíncrona ou síncrona e quais são as latências?

A latência de sincronização automática geralmente é de 2 minutos. Em casos de banco de dados de produtividade compartilhado com um grande número de contêineres, a latência de sincronização automática de contêineres individuais pode ser maior e levar até 5 minutos. Gostaríamos de saber mais sobre como essa latência se adapta a seus cenários. Para isso, entre em contato com a [equipe de Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>Há cenários em que os itens do repositório transacional não são propagados automaticamente para o repositório analítico?

Se itens específicos em seu contêiner violarem o [esquema bem definido de análise](analytical-store-introduction.md#analytical-schema), eles não serão incluídos no repositório analítico. Se você tiver cenários bloqueados pelo esquema bem definido de análise, envie um email para a [equipe do Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com) para obter ajuda.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Posso particionar os dados no repositório analítico de forma diferente do repositório transacional?

Os dados no repositório analítico são particionados com base no particionamento horizontal de fragmentos no repositório transacional. No momento, não é possível escolher uma estratégia de particionamento diferente para o repositório analítico.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>Posso personalizar ou substituir a maneira como os dados transacionais são transformados em formato de coluna no repositório analítico?

No momento, você não pode transformar os itens de dados quando eles são automaticamente propagados do repositório transacional para o repositório analítico. Se você tiver cenários bloqueados por essa limitação, envie um email para a [equipe do Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

## <a name="analytical-time-to-live-ttl"></a>Tempo de vida (TTL) analítico

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>O TTL de dados analíticos é compatível com os níveis de contêiner e de item?

Neste momento, o TTL para dados analíticos só pode ser configurado no nível de contêiner e não há suporte para definir TTL analítico no nível de item.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Depois de definir o TTL analítico de nível de contêiner em um contêiner do Azure Cosmos DB, posso mudar para um valor diferente mais tarde?

Sim, o TTL analítico pode ser atualizado para qualquer valor válido. Consulte o artigo [TTL analítico](analytical-store-introduction.md#analytical-ttl) para obter mais detalhes sobre o TTL analítico.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Posso atualizar ou excluir um item do repositório analítico após o seu TTL ter expirado no repositório transacional?

Todas as atualizações e exclusões transacionais são copiadas para o repositório analítico, mas se o item tiver sido limpo do repositório transacional, ele não poderá ser atualizado no repositório analítico. Para saber mais, confira o artigo [TTL analítico](analytical-store-introduction.md#analytical-ttl).

## <a name="billing"></a>Cobrança

### <a name="what-is-the-billing-model-of-synapse-link-for-azure-cosmos-db"></a>Qual é o modelo de cobrança do Link do Synapse para Azure Cosmos DB?

O [repositório analítico do Azure Cosmos DB](analytical-store-introduction.md) está disponível em visualização pública sem nenhum encargo para o repositório analítico até 30 de agosto de 2020. Synapse Spark e SQL do Synapse são cobrados por meio de [consumo do serviço Synapse](https://azure.microsoft.com/pricing/details/synapse-analytics/).

## <a name="security"></a>Segurança

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Quais são as maneiras de autenticar com o repositório analítico?

A autenticação com o repositório analítico é igual a de um repositório transacional. Para um determinado banco de dados, você pode autenticar com a chave mestra ou somente leitura. Você pode usar o serviço vinculado no Synapse Studio para evitar colar as chaves do Azure Cosmos DB nos notebooks do Spark. O acesso a esse serviço vinculado está disponível para todos que têm acesso ao espaço de trabalho.

## <a name="synapse-run-times"></a>Tempos de execução do Synapse

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Quais são os tempos de execução do Synapse com suporte, atualmente, para acessar o repositório analítico do Azure Cosmos DB?

|Tempo de execução do Synapse |Suporte atual |
|---------|---------|
|Pools do Synapse Spark | Leitura, gravação (por meio do repositório transacional), tabela, exibição temporária |
|Synapse SQL Server sem servidor    | Ler, exibir |
|SQL do Synapse provisionado   |  Não disponível |

### <a name="do-my-synapse-spark-tables-sync-with-my-synapse-sql-serverless-tables-the-same-way-they-do-with-azure-data-lake"></a>Minhas tabelas do Synapse Spark sincronizam com minhas tabelas SQL Server sem Synapse da mesma maneira que fazem com Azure Data Lake?

Esse recurso não está disponível no momento.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Posso fazer streaming estruturado do Spark a partir do repositório analítico?

Atualmente, o suporte a streaming estruturado do Spark para Azure Cosmos DB é implementado usando a funcionalidade de feed de alterações do repositório transacional e ainda não tem suporte do repositório analítico.

## <a name="synapse-studio"></a>Synapse Studio

### <a name="in-the-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>No Synapse Studio, como reconheço se estou conectado a um contêiner do Azure Cosmos DB com o repositório de análise habilitado?

Um contêiner do Azure Cosmos DB habilitado com o repositório analítico tem o seguinte ícone:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Ícone do contêiner do Azure Cosmos DB habilitado com o repositório analítico":::

Um contêiner de repositório transacional será representado com o seguinte ícone:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="Ícone do contêiner do Azure Cosmos DB com repositório transacional":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-synapse-studio"></a>Como você passa as credenciais do Azure Cosmos DB a partir do Synapse Studio?

Atualmente, as credenciais do Azure Cosmos DB são passadas durante a criação do serviço vinculado pelo usuário que tem acesso aos bancos de dados do Azure Cosmos DB. O acesso a esse repositório está disponível para outros usuários que tenham acesso ao espaço de trabalho.

## <a name="next-steps"></a>Próximas etapas

* Saber mais sobre os [benefícios do Link do Synapse](synapse-link.md#synapse-link-benefits)

* Saiba mais sobre a [integração entre o Link do Synapse e o Azure Cosmos DB](synapse-link.md#synapse-link-integration).
