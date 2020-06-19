---
title: O que é o repositório analítico do Azure Cosmos DB (versão prévia)?
description: Saiba mais sobre o repositório transacional (baseado em linha) e analítico (baseado em coluna) do Azure Cosmos DB. Benefícios do repositório analítico, impacto no desempenho para cargas de trabalho de larga escala e sincronização automática de dados do repositório transacional para o repositório analítico
author: SriChintala
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: srchi
ms.openlocfilehash: f00acf3580130d85d6eaeaee4d52eb748d20aa7b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656640"
---
# <a name="what-is-azure-cosmos-db-analytical-store-preview"></a>O que é o repositório analítico do Azure Cosmos DB (versão prévia)?

> [!IMPORTANT]
> O repositório analítico do Azure Cosmos DB está atualmente em versão prévia. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte os [Termos de uso complementares de versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O repositório analítico do Azure Cosmos DB é um repositório de coluna totalmente isolado para habilitar a análise em grande escala em relação a dados operacionais em seu Azure Cosmos DB, sem nenhum impacto sobre suas cargas de trabalho transacionais.  

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Desafios com análise em larga escala sobre dados operacionais

Os dados operacionais multimodelo em um contêiner do Azure Cosmos DB são armazenados internamente em um "repositório transacional" baseado em linha e indexado. O formato de repositório de linha foi projetado para permitir leituras e gravações transacionais rápidas nos tempos de resposta na casa de milissegundos e consultas operacionais. Se seu conjunto de dados gera consultas analíticas grandes e complexas, isso poderá ser caro em termos de taxa de transferência provisionada nos dados armazenados nesse formato. Por outro lado, o alto consumo de produtividade provisionada afeta o desempenho de cargas de trabalho transacionais que são usadas por seus aplicativos e serviços em tempo real.

Tradicionalmente, para analisar grandes quantidades de dados, os dados operacionais são extraídos do repositório transacional do Azure Cosmos DB e armazenados em uma camada de dados separada. Por exemplo, os dados são armazenados em um data warehouse ou em um data lake em um formato adequado. Esses dados são usados posteriormente para a análise em larga escala e são analisados usando o um mecanismo de computação, como os clusters do Apache Spark. Essa separação do armazenamento analítico e das camadas de computação dos dados operacionais resulta em latência adicional, pois os pipelines de ETL (extração, transformação e carregamento) são executados com menos frequência para minimizar o impacto potencial em suas cargas de trabalho transacionais.

Os pipelines de ETL também se tornam complexos ao lidar com atualizações dos dados operacionais quando comparados ao tratamento apenas de dados operacionais recentemente ingeridos. 

## <a name="column-oriented-analytical-store"></a>Repositório analítico orientado por coluna

O repositório analítico do Azure Cosmos DB aborda os desafios de complexidade e latência que ocorrem com os pipelines de ETL tradicionais. O repositório analítico do Azure Cosmos DB pode sincronizar automaticamente seus dados operacionais em um repositório de coluna separado. O formato de repositório de coluna é adequado para consultas analíticas de larga escala a serem executadas de maneira otimizada, resultando na melhoria da latência dessas consultas.

Agora, ao usar o Link do Azure Synapse, você pode criar soluções de HTAP sem ETL ao vincular diretamente ao repositório analítico do Azure Cosmos DB a partir do Synapse Analytics. Isso permite que você execute análises de larga escala quase em tempo real em seus dados operacionais.

## <a name="analytical-store-details"></a>Detalhes do repositório analítico

Quando você habilitar o repositório analítico em um contêiner do Azure Cosmos DB, um novo repositório de coluna será criado internamente com base nos dados operacionais em seu contêiner. Esse repositório de coluna é persistido separadamente do repositório transacional orientado por linha desse contêiner. As inserções, atualizações e exclusões dos seus dados operacionais são sincronizadas automaticamente ao repositório analítico. Você não precisa do feed de alterações ou da ETL para sincronizar os dados.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Repositório de coluna para cargas de trabalho analíticas em dados operacionais

As cargas de trabalho analíticas normalmente envolvem agregações e exames sequenciais dos campos selecionados. Ao armazenar os dados em uma ordem de coluna principal, o repositório analítico permite que um grupo de valores de cada campo seja serializado em conjunto. Esse formato reduz as IOPS necessárias para verificar ou computar estatísticas em campos específicos. Ele melhora significativamente os tempos de resposta de consulta para verificações em grandes conjuntos de dados. 

Por exemplo, se suas tabelas operacionais estiverem no seguinte formato:

![Exemplo de tabela operacional](./media/analytical-store-introduction/sample-operational-data-table.png)

O repositório de linha persiste os dados acima em um formato serializado, por linha, no disco. Esse formato permite leituras, gravações e consultas operacionais transacionais mais rápidas, como “Retornar informações sobre Product1”. No entanto, como o conjunto de dados aumenta e se você quiser executar consultas analíticas complexas sobre eles, isso pode se tornar caro. Por exemplo, se você quiser saber “as tendências de vendas de um produto sob a categoria chamada ‘equipamentos’ entre diferentes unidades de negócios e meses”, você precisará executar uma consulta complexa. Grandes exames nesse conjunto de recursos podem ser caros em termos de taxa de transferência provisionada e também podem impactar o desempenho das cargas de trabalho transacionais, capacitando seus aplicativos e serviços em tempo real.

O repositório analítico, que é um repositório de coluna, é mais adequado para essas consultas porque ele serializa campos semelhantes de dados juntos e reduz as IOPS de disco.

A imagem a seguir mostra o repositório de linhas transacional em comparação ao repositório de colunas analíticas no Azure Cosmos DB:

![Repositório de linhas transacional em comparação ao repositório de colunas analíticas no Azure Cosmos DB](./media/analytical-store-introduction/transactional-analytical-data-stores.png)

### <a name="decoupled-performance-for-analytical-workloads"></a>Desempenho separado para cargas de trabalho analíticas

Não há nenhum impacto no desempenho de suas cargas de trabalho transacionais devido a consultas analíticas, pois o repositório analítico fica separado do repositório transacional.  O repositório analítico não precisa de RUs (unidades de solicitação) separadas para ser alocado.

### <a name="auto-sync"></a>Sincronização automática

A sincronização automática refere-se à capacidade totalmente gerenciada do Azure Cosmos DB em que as inserções, atualizações e exclusões em relação a dados operacionais são sincronizadas automaticamente do repositório transacional para o repositório analítico em tempo quase real dentro de 5 minutos.

O recurso de sincronização automática, junto com o repositório analítico, fornece os seguintes benefícios principais:

#### <a name="scalability--elasticity"></a>Escalabilidade e elasticidade

Ao usar o particionamento horizontal, o repositório transacional do Azure Cosmos DB pode escalar elasticamente o armazenamento e a taxa de transferência sem nenhum tempo de inatividade. O particionamento horizontal no repositório transacional fornece escalabilidade e elasticidade na sincronização automática para garantir que os dados sejam sincronizados com o repositório analítico quase em tempo real. A sincronização de dados ocorre independentemente da taxa de transferência do tráfego transacional, sejam 1.000 ou 1 milhão de operações/s, e não impacta a taxa de transferência provisionada no repositório transacional. 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Lidar automaticamente com atualizações de esquema

O repositório transacional do Azure Cosmos DB é independente de esquema e permite que você faça uma iteração nos seus aplicativos transacionais sem a necessidade de lidar com gerenciamento de índices ou de esquemas. De modo contrastante, o repositório analítico do Azure Cosmos DB é esquematizado para otimizar o desempenho de consultas analíticas. Com o recurso de sincronização automática, o Azure Cosmos DB gerencia a inferência de esquema sobre as atualizações mais recentes do repositório transacional.  Ele também gerencia a representação do esquema no repositório analítico pronto para uso, que inclui o tratamento de tipos de dados aninhados.

Nele, há uma evolução de esquema em que novas propriedades são adicionadas ao longo do tempo, e o repositório analítico apresenta automaticamente um esquema unido entre todos os esquemas históricos no repositório transacional.

Se todos os dados operacionais no Azure Cosmos DB seguirem um esquema bem definido para análise, o esquema será automaticamente inferido e representado corretamente no repositório analítico. Se o esquema bem definido de análise, conforme definido abaixo, for violado por determinados itens, eles não serão incluídos no repositório analítico. Se você tiver cenários bloqueados pelo esquema bem definido de definição de análise, envie um email para a [equipe do Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

Um esquema bem definido para a análise é definido com as seguintes considerações:

* Uma propriedade sempre tem o mesmo tipo em vários itens

  * Por exemplo, `{"a":123} {"a": "str"}` não tem um esquema bem definido porque, às vezes, `"a"` é uma cadeia de caracteres e, outras vezes, um número. 
  
    Nesse caso, o repositório analítico registra o tipo de dados de `“a”` como o tipo de dados de `“a”` no primeiro item que ocorre no tempo de vida do contêiner. Os itens em que o tipo de dados de `“a”` diferirem não serão incluídos no repositório analítico.
  
    Essa condição não se aplica a propriedades nulas. Por exemplo, `{"a":123} {"a":null}` ainda é bem definido.

* Tipos de matriz devem conter um único tipo repetido

  * Por exemplo, `{"a": ["str",12]}` não é um esquema bem definido porque a matriz contém uma combinação de tipos inteiros e cadeias de caracteres

* Há um máximo de 200 propriedades em qualquer nível de aninhamento de um esquema, além de uma profundidade máxima de aninhamento de 5

  * Um item com 201 propriedades no nível superior não tem um esquema bem definido.

  * Um item com mais de cinco níveis aninhados no esquema também não tem um esquema bem definido. Por exemplo, `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* Nomes de propriedade são exclusivos quando comparados em uma maneira que não diferencia maiúsculas de minúsculas

  * Por exemplo, os itens a seguir não têm um esquema bem definido: `{"Name": "fred"} {"name": "john"}` – `"Name"` e `"name"` são os mesmos quando comparados em uma maneira que não diferencia maiúsculas de minúsculas

### <a name="cost-effective-archival-of-historical-data"></a>Arquivamento econômico de dados históricos

Uma camada de dados se refere à separação de dados entre as infraestruturas de armazenamento otimizadas para cenários diferentes. Dessa forma, melhora o desempenho geral e a economia da pilha de dados de ponta a ponta. Com o repositório analítico, agora o Azure Cosmos DB tem suporte para camadas automáticas de dados do repositório transacional para o repositório analítico com diferentes layouts de dados. Com o repositório analítico otimizado em termos de custo de armazenamento em comparação ao repositório transacional, você pode reter horizontes muito maiores de dados operacionais para análise histórica.

Depois que o repositório analítico estiver habilitado, com base nas necessidades de retenção de dados das cargas de trabalho transacionais, você poderá configurar a propriedade “vida útil do repositório transacional (TTL transacional)” para que os registros sejam excluídos automaticamente do repositório transacional após um determinado período de tempo. De modo semelhante, a “vida útil do repositório analítico (TTL analítica)” permite que você gerencie o ciclo de vida dos dados retidos no repositório analítico independentemente do repositório transacional. Ao habilitar o repositório analítico e configurar as propriedades de TTL, você pode criar camadas e definir o período de retenção de dados para os dois repositórios perfeitamente.

### <a name="global-distribution"></a>Distribuição Global

Se você tiver uma conta do Azure Cosmos DB distribuída globalmente, depois de habilitar o repositório analítico para um contêiner, ele ficará disponível em todas as regiões dessa conta.  Quaisquer alterações nos dados operacionais são replicadas globalmente em todas as regiões. Você pode executar consultas analíticas efetivamente na cópia regional mais próxima dos seus dados no Azure Cosmos DB.

### <a name="security"></a>Segurança

A autenticação com o repositório analítico é igual a de um repositório transacional para um determinado banco de dados. Você pode usar chaves mestras ou somente leitura para a autenticação. Você pode usar o serviço vinculado no Synapse Studio para evitar colar as chaves do Azure Cosmos DB nos notebooks do Spark. O acesso a esse serviço vinculado está disponível para todos que tenham acesso ao espaço de trabalho.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Suporte para vários runtimes do Azure Synapse Analytics

O repositório analítico é otimizado para fornecer escalabilidade, elasticidade e desempenho para cargas de trabalho analíticas sem qualquer dependência dos tempos de execução de computação. A tecnologia de armazenamento é autogerenciada para otimizar suas cargas de trabalho de análise se esforços manuais.

Ao desacoplar o sistema de repositório analítico do sistema de computação analítica, os dados no repositório analítico do Azure Cosmos DB podem ser consultados simultaneamente em diferentes runtimes de análise com suporte do Azure Synapse Analytics. A partir de hoje, o Synapse Analytics tem suporte para o Apache Spark e o SQL sem servidor com o repositório analítico do Azure Cosmos DB.

> [!NOTE]
> Você só pode ler o repositório analítico usando o tempo de execução do Synapse Analytics. Você pode gravar os dados de volta no seu repositório transacional como uma camada de serviço.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Preços

O repositório analítico segue um modelo de preço baseado em consumo no qual você é cobrado por:

* Armazenamento: o volume dos dados retidos no repositório analítico todos os meses, incluindo os dados históricos, conforme definido pela TTL analítica.

* Operações de gravação analítica: a sincronização totalmente gerenciada de atualizações de dados operacionais para o repositório analítico a partir do repositório transacional (sincronização automática).

* Operações de leitura analítica: as operações de leitura executadas no repositório analítico do Synapse Analytics Spark e os tempos de execução do SQL sem servidor.

> [!NOTE]
> O repositório analítico do Azure Cosmos DB está disponível em visualização pública sem nenhum encargo até 30 de agosto de 2020.

O preço do repositório analítico é separado do modelo de preços do repositório transacional. Não há nenhum conceito de RUs provisionadas no repositório analítico. Consulte a [página de preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter detalhes completos sobre o modelo de preços do repositório analítico.

Para obter uma estimativa de custo de alto nível para habilitar o repositório analítico em um contêiner do Azure Cosmos DB, você pode usar o [planejador de capacidade do Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) e obter uma estimativa dos seus custos de armazenamento analítico e de operações de gravação. Os custos de operações de leitura analítica dependem das características da carga de trabalho analítica, mas como uma estimativa de alto nível, o exame de 1 TB de dados no repositório analítico geralmente resulta em 130.000 operações de leitura analítica e em um custo de US$ 0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> TTL (vida útil) analítica

A TTL analítica indica por quanto tempo os dados devem ser retidos em seu repositório analítico, para um contêiner. 

As inserções, atualizações, exclusões em dados operacionais são sincronizadas automaticamente do repositório transacional para o repositório analítico, independentemente da configuração da TTL transacional. A retenção desses dados operacionais no repositório analítico pode ser controlada pelo valor da TTL Analítico no nível de contêiner, conforme especificado abaixo:

A TTL Analítico em um contêiner é definida usando a propriedade `AnalyticalStoreTimeToLiveInSeconds`:

* Se o valor for definido como "0", ausente (ou definido como nulo): o repositório analítico será desabilitado, e nenhum dado será replicado do repositório transacional para o repositório analítico

* Se presente e o valor for definido como "-1": o repositório analítico manterá todos os dados históricos, independentemente da retenção dos dados no repositório transacional. Essa configuração indica que o repositório analítico tem retenção infinita dos seus dados operacionais

* Se presente e o valor for definido como algum número positivo “n”: os itens expirarão do repositório analítico “n” segundos após a hora da última modificação no repositório transacional. Essa configuração poderá ser usada se você quiser manter seus dados operacionais por um período de tempo limitado no repositório analítico, independentemente da retenção dos dados no repositório transacional.

Considere o seguinte:
*   Depois que o repositório analítico for habilitado com um determinado valor de TTL analítico, é possível atualizá-lo posteriormente com um valor válido diferente. 
*   Embora a TTL transacional possa ser definida no nível de contêiner ou de item, atualmente, a TTL analítica só pode ser definida no nível de contêiner.
*   Você pode obter uma retenção mais longa de seus dados operacionais no repositório analítico ao definir a TTL analítica > = TTL transacional no nível de contêiner.
*   O repositório analítico pode ser feito para espelhar o armazenamento transacional ao definir a TTL analítica = TTL transacional.

Para saber mais, confira [Como configurar a TTL analítica em um contêiner](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte a seguinte documentação:

* [Link do Azure Synapse para Azure Cosmos DB](synapse-link.md)

* [Introdução ao Link do Azure Synapse para Azure Cosmos DB](configure-synapse-link.md)

* [Perguntas frequentes sobre o Link do Synapse para Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Casos de uso do Link do Azure Synapse para Azure Cosmos DB](synapse-link-use-cases.md)
