---
title: Guia de desempenho e ajuste do fluxo de dados de mapeamento
description: Saiba mais sobre os principais fatores que afetam o desempenho de fluxos de dados de mapeamento no Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 07/06/2020
ms.openlocfilehash: 1c63568418f21da0556ced0d004e04e7909118fb
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042621"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Guia de desempenho e ajuste de fluxos de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Os fluxos de dados de mapeamento no Azure Data Factory fornecem uma interface sem código para projetar, implantar e orquestrar transformações de dados em escala. Se você não conhecer bem os fluxos de dados de mapeamento, consulte a [Visão geral do fluxo de dados de mapeamento](concepts-data-flow-overview.md).

Ao projetar e testar fluxos de dados do UX do Azure Data Factory, ligue o modo de depuração para executar os fluxos de dados em tempo real sem esperar o aquecimento de um cluster. Para saber mais, consulte [Modo de depuração](concepts-data-flow-debug-mode.md).

Este vídeo mostra alguns exemplos de tempo de transformação de dados com os fluxos de dados:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Monitoramento de desempenho do fluxo de dados

Ao criar fluxos de dados de mapeamento, você testar a unidade de cada transformação clicando na guia de visualização de dados no painel de configuração. Depois de verificar sua lógica, teste o fluxo de dados de ponta a ponta como uma atividade em um pipeline. Adicione uma atividade Executar Fluxo de Dados e use o botão Depurar para testar o desempenho do fluxo de dados. Para abrir o plano de execução e o perfil de desempenho de seu fluxo de dados, clique no ícone de óculos em “ações” na guia de saída do pipeline.

![Monitor do Fluxo de Dados](media/data-flow/mon002.png "Monitor de Fluxo de Dados 2")

 Você pode usar essas informações para estimar o desempenho do fluxo de dados comparando com fontes de dados de tamanhos diferentes. Para saber mais, consulte [Monitoramento de fluxos de dados de mapeamento](concepts-data-flow-monitoring.md).

![Monitoramento de fluxo de dados](media/data-flow/mon003.png "Monitor de Fluxo de Dados 3")

 Para execuções de depuração de pipeline, é necessário cerca de um minuto de tempo de configuração de cluster em seus cálculos de desempenho geral para um cluster aquecido. Se estiver inicializando o Azure Integration Runtime padrão, a criação poderá levar cerca de 5 minutos.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Aumentar o tamanho da computação no Azure Integration Runtime

Um Integration Runtime com mais núcleos aumenta o número de nós nos ambientes de computação do Spark e fornece mais capacidade de processamento para ler, gravar e transformar seus dados. Os Fluxos de Dados do Azure Data Factory utilizam o Spark para o mecanismo de computação. O ambiente Spark funciona muito bem em recursos com otimização de memória.

É recomendável usar **memória otimizada** para a maioria das cargas de trabalho de produção. Você poderá armazenar mais dados na memória e minimizar os erros de memória insuficiente. A memória otimizada tem um ponto de preço mais alto por núcleo do que a computação otimizada, mas provavelmente resultará em velocidades de transformação mais rápidas e em pipelines mais bem-sucedidos. Se você tiver erros de memória insuficiente ao executar seus fluxos de dados, mude para uma configuração do Azure IR otimizada para memória.

A **computação otimizada** pode ser suficiente para depuração e visualização de dados de um número limitado de linhas de dados. A computação otimizada provavelmente não será executada também com cargas de trabalho de produção.

![Novo IR](media/data-flow/ir-new.png "Novo IR")

Para saber mais sobre como criar um Integration Runtime, consulte [Integration Runtime no Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Aumentar o tamanho do cluster de depuração

Por padrão, a ativação da depuração usará Azure IR padrão criado automaticamente para cada data factory. Esse Azure IR padrão é configurado para oito núcleos, quatro para um nó de driver e quatro para um nó de trabalho, usando propriedades de computação geral. Ao testar com dados maiores, você pode aumentar o tamanho do cluster de depuração criando um Azure IR com configurações maiores e escolher esse novo Azure IR quando você ativar a depuração. Isso instruirá o Azure Data Factory a usar esse Azure IR para versão preliminar de dados e depuração de pipeline com fluxos de dados.

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>Diminuir o tempo de inicialização de computação do cluster com TTL

Há uma propriedade no Azure IR em Propriedades de Fluxo de Dados que permitirá preparar um pool de recursos de computação de cluster para seu alocador. Com esse pool, você pode enviar sequencialmente atividades de fluxo de dados para execução. Depois que o pool for estabelecido, cada trabalho subsequente levará de 1 a 2 minutos até que o cluster do Spark sob demanda execute seu trabalho. A configuração inicial do pool de recursos levará cerca de 6 minutos. Especifique a quantidade de tempo que você deseja manter o pool de recursos na configuração de vida útil (TTL).

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>Otimização para o Banco de Dados SQL do Azure e o Azure SQL Data Warehouse Synapse

### <a name="partitioning-on-source"></a>Particionamento na origem

1. Vá para a guia **Otimizar** e selecione **Definir particionamento**
1. Selecione **Origem**.
1. Em **Número de partições**, defina o número máximo de conexões com o Banco de Dados SQL do Azure. Você pode tentar uma configuração mais alta para obter conexões paralelas com seu banco de dados. No entanto, alguns casos podem resultar em um desempenho melhor com um número limitado de conexões.
1. Selecione se deseja particionar por uma coluna de tabela ou uma consulta específica.
1. Se você selecionou **Coluna**, escolha a coluna de partição.
1. Se você selecionou **Consulta**, insira uma consulta que corresponda ao esquema de particionamento da tabela de banco de dados. Essa consulta permite que o mecanismo de banco de dados de origem aproveite a eliminação de partição. As tabelas do banco de dados de origem não precisam ser particionadas. Se sua fonte não estiver particionada, o Azure Data Factory usará o particionamento de dados no ambiente de transformação do Spark com base na chave que selecionada na transformação de origem.

![Bloco de origem](media/data-flow/sourcepart3.png "Bloco de origem")

> [!NOTE]
> Um bom guia para escolher o número de partições para sua fonte é usar o número de núcleos que você definiu para o Azure Integration Runtime e multiplicar esse número por cinco. Por exemplo, se você estiver transformando uma série de arquivos em suas pastas do ADLS e for utilizar um Azure IR de 32 núcleos, o número de partições que você terá como objetivo será 32 x 5 = 160 partições.

### <a name="source-batch-size-input-and-isolation-level"></a>Tamanho do lote de origem, entrada e nível de isolamento

Em **Opções de origem** na transformação de origem, as seguintes configurações podem afetar o desempenho:

* O tamanho do lote instrui o Azure Data Factory a armazenar dados em conjuntos na memória do Spark em vez de linha por linha. O tamanho do lote é uma configuração opcional e você pode ficar sem recursos nos nós de computação se eles não forem dimensionados corretamente. Se não for definida, essa propriedade usará os padrões do lote do cache do Spark.
* A definição de uma consulta pode permitir filtragem de linhas na origem antes que elas cheguem ao Fluxo de Dados para processamento. Isso pode acelerar a aquisição inicial de dados. Se você usar uma consulta, poderá adicionar dicas de consulta opcionais para seu Banco de Dados SQL do Azure, como READ UNCOMMITTED (leitura não confirmada).
* A leitura não confirmada fornecerá resultados de consulta mais rápidos na transformação de origem

![Origem](media/data-flow/source4.png "Fonte")

### <a name="sink-batch-size"></a>Tamanho do lote do coletor

Para evitar que seus fluxos de dados sejam processados linha a linha, defina **Tamanho do lote** na guia Configurações para o Banco de Dados SQL do Azure e coletores DW do SQL do Azure. Se o tamanho do lote for definido, o ADF processará gravações de banco de dados em lotes com base no tamanho informado. Se não for definida, essa propriedade usará os padrões do lote do cache do Spark.

![Coletor](media/data-flow/sink4.png "Coletor")

### <a name="partitioning-on-sink"></a>Particionamento no coletor

Mesmo que você não tenha seus dados particionados nas suas tabelas de destino, recomendamos ter dados particionados na transformação do coletor. Os dados particionados geralmente tornam o carregamento muito mais rápido ao forçar todas as conexões a usarem um único nó/partição. Vá para a guia Otimizar do seu coletor e selecione o particionamento *Round Robin* para selecionar o número ideal de partições a serem gravadas no coletor.

### <a name="disable-indexes-on-write"></a>Desabilitar índices na gravação

Em seu pipeline, adicione uma [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) antes da atividade de Fluxo de Dados que desabilita os índices nas tabelas de destino gravadas do coletor. Após a atividade de Fluxo de Dados, adicione outra atividade de procedimento armazenado que habilite esses índices. Ou utilize os scripts de pré-processamento e pós-processamento em um coletor de banco de dados.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Aumentar o tamanho do seu Banco de Dados SQL do Azure e do DW

Agende um redimensionamento do Banco de Dados SQL do Azure e do DW da origem e do coletor antes de executar o pipeline para aumentar a taxa de transferência e minimizar a limitação do Azure depois de atingir os limites de DTU. Depois que a execução do pipeline for concluída, redimensione os bancos de dados de volta à sua taxa de execução normal.

* A tabela de origem do Banco de Dados SQL com 887.000 linhas e 74 colunas para uma tabela do Banco de Dados SQL com uma única transformação de coluna derivada leva cerca de 3 minutos de ponta a ponta, usando Azure IRs de depuração de 80 núcleos otimizados para memória.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Somente DW do SQL do Azure Synapse] Usar preparo para carregar dados em massa por meio do Polybase

Para evitar inserir linha por linha em seu DW, verifique **Habilitar preparo** nas configurações de Coletor para que o Azure Data Factory possa usar [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). O PolyBase permite que o Azure Data Factory carregue os dados em massa.
* Ao executar a atividade de fluxo de dados de um pipeline, você precisará selecionar um blob ou local de armazenamento ADLS Gen2 para preparar seus dados durante o carregamento em massa.

* A origem de arquivo de 421 Mb com 74 colunas para uma tabela de Synapse e uma única transformação de coluna derivada leva cerca de 4 minutos de ponta a ponta usando Azure IRs de depuração de 80 núcleos otimizados para memória.

## <a name="optimizing-for-files"></a>Otimizar para arquivos

Em cada transformação, você pode definir o esquema de particionamento que deseja que data factory use na guia Otimizar. É uma prática recomendada testar primeiro os coletores baseados em arquivo, mantendo o particionamento e as otimizações padrão. Deixar o particionamento para "particionamento atual" no coletor para um destino de arquivo permitirá que o Spark defina um particionamento padrão apropriado para suas cargas de trabalho. O particionamento padrão usa 128Mb por partição.

* Para arquivos menores, escolher menos partições pode, às vezes, ser melhor e mais rápido do que pedir que o Spark particione seus arquivos pequenos.
* Se você não tiver informações suficientes sobre seus dados de origem, escolha o particionamento *Round Robin* e defina o número de partições.
* Se seus dados tiverem colunas que podem ser boas chaves de hash, escolha *particionamento Hash*.

* A origem do arquivo com o coletor de arquivos de 421 Mb com 74 colunas e uma única transformação de coluna derivada leva cerca de 2 minutos de ponta a ponta usando Azure IRs de depuração de 80 núcleos otimizados para memória.

Durante a depuração na versão prévia de dados e na depuração de pipeline, os tamanhos de limite e amostragem para conjuntos de dados de origem baseados em arquivo se aplicam apenas ao número de linhas retornadas, e não ao número de linhas lidas. Isso pode afetar o desempenho das suas execuções de depuração e causar falha no fluxo.
* Os clusters de depuração são por padrão pequenos clusters de nó único e recomendamos o uso de arquivos pequenos de exemplo para depuração. Vá para Configurações de depuração e aponte para um pequeno subconjunto de dados usando um arquivo temporário.

    ![Configurações de depuração](media/data-flow/debugsettings3.png "Configurações de depuração")

### <a name="file-naming-options"></a>Opções de nomenclatura de arquivo

A maneira mais comum de gravar dados transformados em fluxos de dados de mapeamento é gravar armazenamento de arquivos do ADLS ou blobs. No seu coletor, você deve selecionar um conjunto de dados que aponte para um contêiner ou pasta, não um arquivo nomeado. Como o fluxo de dados de mapeamento usa o Spark para execução, sua saída é dividida em vários arquivos com base no esquema de particionamento.

Um esquema de particionamento comum é escolher _Saída para um único arquivo_, que mescla todos os arquivos PART de saída em um único arquivo em seu coletor. Essa operação requer que a saída seja reduzida para uma única partição em um único nó de cluster. Você poderá ficar sem recursos de nó de cluster se estiver combinando muitos arquivos de origem grandes em um único arquivo de saída.

Para evitar esgotar os recursos do nó de computação, mantenha o esquema padrão otimizado no fluxo de dados e adicione uma atividade de cópia em seu pipeline que mescle todos os arquivos PART da pasta de saída em um novo arquivo único. Essa técnica separa a ação de transformação da mesclagem de arquivos e obtém o mesmo resultado que a configuração _Saída para um único arquivo_.

### <a name="looping-through-file-lists"></a>Loop nas listas de arquivos

Um fluxo de dados de mapeamento será executado melhor quando a transformação de origem iterar em vários arquivos em vez de executar um loop por meio de atividade “For Each”. Recomendamos usar caracteres curinga ou listas de arquivos em sua transformação de origem. O processo de Fluxo de Dados será executado mais rapidamente, permitindo que o loop ocorra dentro do cluster do Spark. Para saber mais, consulte [Usar caractere curinga na transformação de origem](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Por exemplo, se você tiver uma lista de arquivos de dados de julho de 2019 que deseja processar em uma pasta no armazenamento de blobs, você pode usar o caractere curinga abaixo na sua transformação de origem.

```DateFiles/*_201907*.txt```

Ao usra o curinga, seu pipeline conterá apenas uma atividade de Fluxo de Dados. Isso terá um desempenho melhor do que uma pesquisa no armazenamento de blob que, em seguida, itera em todos os arquivos correspondentes usando um ForEach com uma atividade Executar Fluxo de Dados interna.

O pipeline For Each em modo paralelo gera vários clusters ao criar clusters de trabalho para cada atividade de fluxo de dados executada. Isso pode causar a limitação de serviço do Azure com um grande número de execuções simultâneas. No entanto, o uso de Executar Fluxo de Dados dentro de um For Each com conjunto Sequencial no pipeline evitará a limitação e o esgotamento de recursos. Isso forçará o Data Factory a executar cada um de seus arquivos em um fluxo de dados sequencialmente.

Se você usar For Each um com um fluxo de dados em sequência, recomendamos a configuração TTL no Azure Integration Runtime. Isso porque cada arquivo incorrerá em um tempo de inicialização de cluster completo de 5 minutos dentro do iterador.

### <a name="optimizing-for-cosmosdb"></a>Otimização para CosmosDB

Definir as propriedades de taxa de transferência e de lote em coletores CosmosDB só entrará em vigor durante a execução desse fluxo de dados de uma atividade de fluxo de dados de pipeline. As configurações originais da coleção serão respeitadas pelo CosmosDB após a execução do fluxo de dados.

* Tamanho do lote: Calcule o tamanho aproximado de linhas dos dados e verifique se o tamanho do lote de rowSize * é menor que 2 milhões. Se for, aumente o tamanho do lote para obter uma melhor taxa de transferência
* Taxa de transferência: Defina uma configuração de taxa de transferência mais alta para permitir que os documentos sejam gravados mais rapidamente no CosmosDB. Tenha em mente o aumento de custos de RU com base em uma configuração de taxa de transferência alta.
*   Orçamento de taxa de transferência de gravação: Use um valor que seja menor do que o total de RUs por minuto. Se você tiver um fluxo de dados com um número alto de partições do Spark, definir uma taxa de transferência de orçamento permitirá mais equilíbrio entre essas partições.

## <a name="join-and-lookup-performance"></a>Desempenho de junção e pesquisa

O gerenciamento do desempenho de junções em seu fluxo de dados é uma operação muito comum que você executará durante todo o ciclo de vida de suas transformações de dados. No Azure Data Factory, os fluxos de dados não exigem que os dados sejam classificados antes de junções, pois essas operações são executadas como junções de hash no Spark. No entanto, você pode se beneficiar do desempenho aprimorado com a otimização de junção de “transmissão” que se aplica às transformações Joins, Exists e Lookup.

Isso evitará ordens aleatórias instantâneas efetuando o pushdown do conteúdo de cada lado da relação de junção no nó do Spark. Isso funciona bem para tabelas menores usadas para pesquisas de referência. Tabelas maiores, que podem não se ajustar à memória do nó, não são boas candidatas para a otimização da transmissão.

A configuração recomendada para fluxos de dados com muitas operações de junção é manter a otimização definida como "auto" para "difusão" e usar uma configuração de Azure Integration Runtime com ***otimização de memória*** . Se ocorrerem erros de memória insuficiente ou de tempos limite de transmissão durante execuções de fluxo de dados, você poderá desativar a otimização de transmissão. No entanto, fará com que os fluxos de dados fiquem mais lentos. Você também tem a opção de instruir o fluxo de dados para efetuar pushdown somente do lado esquerdo ou direito da junção, ou ambos.

![Configurações de transmissão](media/data-flow/newbroad.png "Configurações de transmissão")

Outra otimização de junção é criar junções de forma a evitar a tendência do Spark de implementar junções cruzadas. Por exemplo, quando você inclui valores literais em suas condições de junção, o Spark pode interpretar isso como um requisito para executar um produto cartesiano completo primeiro, e depois filtrar os valores associados. Mas se você tiver certeza de que tem valores de coluna em ambos os lados da sua condição de junção, poderá evitar esse produto cartesiano induzido pelo Spark e melhorar o desempenho de suas junções e fluxos de dados.

## <a name="next-steps"></a>Próximas etapas

Consulte outros artigos sobre Fluxo de Dados relacionados ao desempenho:

- [Guia Otimizar Fluxo de Dados](concepts-data-flow-overview.md#optimize)
- [Atividade de Fluxo de Dados](control-flow-execute-data-flow-activity.md)
- [Monitorar o desempenho de Fluxo de Dados](concepts-data-flow-monitoring.md)
