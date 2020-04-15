---
title: Mapeamento do desempenho do fluxo de dados e guia de ajuste
description: Conheça os principais fatores que afetam o desempenho dos fluxos de dados de mapeamento na Fábrica de Dados do Azure.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 18f8b0732e4af0229ff225d9c3b423e27bf342a8
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382792"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mapeamento de fluxos de dados e guia de ajuste

O mapeamento de fluxos de dados na Fábrica de Dados Do Azure fornece uma interface sem código para projetar, implantar e orquestrar transformações de dados em escala. Se você não estiver familiarizado com o mapeamento de fluxos de dados, consulte a [visão geral do fluxo de dados de mapeamento](concepts-data-flow-overview.md).

Ao projetar e testar fluxos de dados do ADF UX, certifique-se de ligar o modo de depuração para executar seus fluxos de dados em tempo real sem esperar que um cluster se aqueça. Para obter mais informações, consulte [Modo de depuração](concepts-data-flow-debug-mode.md).

Este vídeo mostra alguns tempos de amostra transformando dados com fluxos de dados:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Monitorando o desempenho do fluxo de dados

Ao projetar fluxos de dados de mapeamento, você pode testar cada transformação clicando na guia de visualização de dados no painel de configuração. Depois de verificar sua lógica, teste seu fluxo de dados de ponta a ponta como uma atividade em um pipeline. Adicione uma atividade Execute Data Flow e use o botão Depurar para testar o desempenho do fluxo de dados. Para abrir o plano de execução e o perfil de desempenho do seu fluxo de dados, clique no ícone dos óculos em 'ações' na guia de saída do seu pipeline.

![Monitor de fluxo de dados](media/data-flow/mon002.png "Monitor de fluxo de dados 2")

 Você pode usar essas informações para estimar o desempenho do seu fluxo de dados em diferentes fontes de dados. Para obter mais informações, consulte [Monitorando fluxos de dados de mapeamento](concepts-data-flow-monitoring.md).

![Monitoramento do fluxo de dados](media/data-flow/mon003.png "Monitor de fluxo de dados 3")

 Para a depuração do pipeline, cerca de um minuto de tempo de configuração do cluster em seus cálculos gerais de desempenho é necessário para um cluster quente. Se você estiver iniciando o Tempo de Execução de Integração padrão do Azure, o tempo de rotação pode levar cerca de 5 minutos.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Aumento do tamanho da computação no Tempo de Execução da Integração do Azure

Um Tempo de Execução de Integração com mais núcleos aumenta o número de nós nos ambientes de computação Spark e fornece mais poder de processamento para ler, escrever e transformar seus dados. O ADF Data Flows utiliza o Spark para o mecanismo de computação. O ambiente Spark funciona muito bem com recursos otimizados para a memória.
* Experimente um **cluster Otimizado computado** se quiser que sua taxa de processamento seja maior do que a sua taxa de entrada.
* Tente um cluster **otimizado de memória** se quiser armazenar mais dados na memória. A memória otimizada tem um preço-ponto por núcleo mais alto do que o Compute Optimized, mas provavelmente resultará em velocidades de transformação mais rápidas.

![Novo RI](media/data-flow/ir-new.png "Novo RI")

Para obter mais informações sobre como criar um Tempo de Execução de [Integração, consulte Integration Runtime na Fábrica de Dados do Azure](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Aumente o tamanho do seu cluster de depuração

Por padrão, ligar a depuração usará o tempo de execução padrão do Azure Integration que é criado automaticamente para cada fábrica de dados. Este Azure IR padrão é definido para oito núcleos, quatro para um nó de driver e quatro para um nó de trabalhador, usando propriedades da General Compute. Ao testar com dados maiores, você pode aumentar o tamanho do seu cluster de depuração criando um Azure IR com configurações maiores e escolher este novo Azure IR ao ligar a depuração. Isso instruirá a ADF a usar este Azure IR para visualização de dados e depuração de pipeline com fluxos de dados.

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>Diminuir o tempo de inicialinicializar do cluster compute com o TTL

Há uma propriedade no Azure IR em Propriedades de Fluxo de Dados que permitirá que você levante um pool de recursos de computação de cluster para sua fábrica. Com este pool, você pode enviar sequencialmente atividades de fluxo de dados para execução. Uma vez estabelecido o pool, cada trabalho subsequente levará de 1 a 2 minutos para que o cluster Spark sob demanda execute seu trabalho. A configuração inicial do pool de recursos levará cerca de 6 minutos. Especifique a quantidade de tempo que deseja manter o pool de recursos na configuração TTL (time-to-live).

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>Otimização para o Banco de Dados SQL do Azure e o Synapse do Data Warehouse Do Zure SQL

### <a name="partitioning-on-source"></a>Particionamento na fonte

1. Vá para a guia **Otimizar** e selecione **'Particionamento de conjunto'**
1. Selecione **Origem**.
1. Em **Número de partições,** defina o número máximo de conexões ao seu Azure SQL DB. Você pode tentar uma configuração mais alta para obter conexões paralelas ao seu banco de dados. No entanto, alguns casos podem resultar em um desempenho mais rápido com um número limitado de conexões.
1. Selecione se será partição por uma coluna de tabela específica ou por uma consulta.
1. Se você selecionou **Coluna,** escolha a coluna de partição.
1. Se você selecionou **Consulta,** digite uma consulta que corresponda ao esquema de particionamento da tabela do banco de dados. Esta consulta permite que o mecanismo de banco de dados de origem aproveite a eliminação da partição. Suas tabelas de banco de dados de origem não precisam ser particionadas. Se sua fonte ainda não estiver particionada, o ADF ainda usará particionamento de dados no ambiente de transformação do Spark com base na chave que você selecionar na transformação De Origem.

![Parte da Fonte](media/data-flow/sourcepart3.png "Parte da Fonte")

> [!NOTE]
> Um bom guia para ajudá-lo a escolher o número de partições para sua fonte é baseado no número de núcleos que você definiu para o seu Tempo de Execução de Integração do Azure e multiplica esse número por cinco. Então, por exemplo, se você está transformando uma série de arquivos em suas pastas ADLS e você vai utilizar um IR Azure de 32 núcleos, o número de partições que você direcionaria é 32 x 5 = 160 partições.

### <a name="source-batch-size-input-and-isolation-level"></a>Tamanho do lote de origem, entrada e nível de isolamento

Em **Opções de origem** na transformação de origem, as seguintes configurações podem afetar o desempenho:

* O tamanho do lote instrui o ADF a armazenar dados em conjuntos na memória Spark em vez de linha por linha. O tamanho do lote é uma configuração opcional e você pode ficar sem recursos nos nós de computação se eles não forem dimensionados corretamente. A não configuração desta propriedade utilizará padrões de lote de cache spark.
* A definição de uma consulta pode permitir que você filtre linhas na fonte antes que elas cheguem ao Fluxo de Dados para processamento. Isso pode tornar a aquisição inicial de dados mais rápida. Se você usar uma consulta, você pode adicionar dicas opcionais de consulta para o seu Azure SQL DB, como READ UNCOMMITTED.
* Ler não comprometido fornecerá resultados de consulta mais rápidos sobre a transformação da fonte

![Fonte](media/data-flow/source4.png "Fonte")

### <a name="sink-batch-size"></a>Tamanho do lote da pia

Para evitar o processamento linha por linha de seus fluxos de dados, defina o **tamanho do lote** na guia Configurações para afundamentos Azure SQL DB e Azure SQL DW. Se o tamanho do lote estiver definido, o banco de dados de processos ADF será gravado em lotes com base no tamanho fornecido. A não configuração desta propriedade utilizará padrões de lote de cache spark.

![Coletor](media/data-flow/sink4.png "Coletor")

### <a name="partitioning-on-sink"></a>Particionamento na pia

Mesmo que você não tenha seus dados divididos em suas tabelas de destino, é recomendável ter seus dados divididos na transformação do pia. Dados particionados geralmente resultam em carregamento muito mais rápido sobre forçar todas as conexões a usar um único nó/partição. Vá para a guia Otimizar da sua pia e selecione Particionamento *Round Robin* para selecionar o número ideal de partições para escrever na sua pia.

### <a name="disable-indexes-on-write"></a>Desativar índices na gravação

Em seu pipeline, adicione uma [atividade de procedimento armazenado](transform-data-using-stored-procedure.md) antes da atividade de fluxo de dados que desativa índices em suas tabelas de destino escritas a partir de seu Sink. Após a atividade de fluxo de dados, adicione outra atividade de procedimento armazenado que habilite esses índices. Ou utilizar os scripts de pré-processamento e pós-processamento em um dissipador de banco de dados.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Aumente o tamanho do seu Azure SQL DB e DW

Agende um redimensionamento da sua fonte e afunde o Azure SQL DB e DW antes que o pipeline seja executado para aumentar o throughput e minimizar o estrangulamento do Azure quando atingir os limites do DTU. Depois que a execução do pipeline estiver concluída, redimensione seus bancos de dados de volta à taxa de execução normal.

* A tabela de origem sql DB com linhas de 887k e 74 colunas para uma tabela SQL DB com uma única transformação de coluna derivada leva cerca de 3 minutos de ponta a ponta usando a memória otimizada de 80 núcleos debug Azure IRs.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Somente Azure Synapse SQL DW] Use staging para carregar dados em massa via Polybase

Para evitar inserções de linha por linha em seu DW, verifique **Ativar a encenação** nas configurações do Sink para que o ADF possa usar [o PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). O PolyBase permite que o ADF carregue os dados em massa.
* Quando você executar sua atividade de fluxo de dados a partir de um pipeline, você precisará selecionar um local de armazenamento Blob ou ADLS Gen2 para encenar seus dados durante o carregamento em massa.

* Fonte de arquivo de arquivo de 421Mb com 74 colunas para uma tabela Sinapse e uma única transformação de coluna derivada leva cerca de 4 minutos de ponta a ponta usando a memória otimizada de 80 núcleos depurar IRs DoZure.

## <a name="optimizing-for-files"></a>Otimização para arquivos

A cada transformação, você pode definir o esquema de particionamento que deseja que a fábrica de dados use na guia Otimizar. É uma boa prática testar primeiro pias baseados em arquivos mantendo a particionamento padrão e otimizações.

* Para arquivos menores, você pode encontrar escolher menos partições às vezes pode funcionar melhor e mais rápido do que pedir ao Spark para particionar seus pequenos arquivos.
* Se você não tiver informações suficientes sobre seus dados de origem, escolha o particionamento *de Round Robin* e defina o número de partições.
* Se seus dados têm colunas que podem ser boas chaves hash, escolha *particionamento hash*.

* Fonte de arquivo com pia de arquivo de um arquivo de 421Mb com 74 colunas e uma única transformação de coluna derivada leva cerca de 2 minutos de ponta a ponta usando memória otimizada 80 núcleos depuração Azure IRs.

Ao depurar a visualização de dados e a depuração do pipeline, os tamanhos de limite e amostragem para conjuntos de dados de origem baseados em arquivos só se aplicam ao número de linhas retornadas, não ao número de linhas lidas. Isso pode afetar o desempenho de suas execuções de depuração e possivelmente causar o fracasso do fluxo.
* Os clusters de depuração são pequenos clusters de nó único por padrão e recomendamos o uso de pequenos arquivos de amostra para depuração. Vá para Configurações de depuração e aponte para um pequeno subconjunto de seus dados usando um arquivo temporário.

    ![Configurações de depuração](media/data-flow/debugsettings3.png "Configurações de depuração")

### <a name="file-naming-options"></a>Opções de nomeação de arquivos

A maneira mais comum de escrever dados transformados no mapeamento de fluxos de dados escrevendo Blob ou ADLS file store. Na pia, você deve selecionar um conjunto de dados que aponte para um contêiner ou pasta, não um arquivo nomeado. À medida que o fluxo de dados de mapeamento usa o Spark para execução, sua saída é dividida sobre vários arquivos com base no seu esquema de particionamento.

Um esquema comum de particionamento é escolher _Saída para arquivo único,_ que mescla todos os arquivos PART de saída em um único arquivo em seu dissipador. Esta operação requer que a saída reduza a uma única partição em um único nó de cluster. Você pode ficar sem recursos de nó de cluster se estiver combinando muitos arquivos de origem grandes em um único arquivo de saída.

Para evitar esgotar os recursos do nó de computação, mantenha o esquema padrão e otimizado no fluxo de dados e adicione uma Atividade de Cópia em seu pipeline que mescla todos os arquivos PART da pasta de saída para um novo arquivo único. Esta técnica separa a ação de transformação da fusão de arquivos e obtém o mesmo resultado que definir _Saída para um único arquivo_.

### <a name="looping-through-file-lists"></a>Looping através de listas de arquivos

Um fluxo de dados de mapeamento será executado melhor quando a transformação de Origem itera sobre vários arquivos em vez de looping através da atividade For Each. Recomendamos o uso de curingas ou listas de arquivos na sua transformação de origem. O processo fluxo de dados será executado mais rapidamente, permitindo que o looping ocorra dentro do cluster Spark. Para obter mais informações, consulte [Curinga em Source Transformation](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Por exemplo, se você tiver uma lista de arquivos de dados de julho de 2019 que deseja processar em uma pasta no Blob Storage, abaixo está um curinga que você pode usar na sua transformação de Origem.

```DateFiles/*_201907*.txt```

Usando curinga, seu pipeline conterá apenas uma atividade de Fluxo de Dados. Isso será melhor do que uma pesquisa contra a Blob Store que, em seguida, itera em todos os arquivos combinados usando um ForEach com uma atividade Execute Data Flow dentro.

### <a name="optimizing-for-cosmosdb"></a>Otimização para cosmosDB

A definição de propriedades de throughput e batch nos dissipadores do CosmosDB só tem efeito durante a execução desse fluxo de dados de uma atividade de fluxo de dados do pipeline. As configurações originais de coleta serão honradas pelo CosmosDB após a execução do fluxo de dados.

* Tamanho do lote: Calcule o tamanho da linha aproximada de seus dados e certifique-se de que o tamanho do lote rowSize * seja inferior a dois milhões. Se for, aumente o tamanho do lote para obter melhor throughput
* Throughput: Defina uma configuração de throughput mais alta aqui para permitir que os documentos escrevam mais rápido para o CosmosDB. Por favor, tenha em mente os custos de RU mais altos com base em uma configuração de alta taxa de rendimento.
*   Write Throughput Budget: Use um valor menor do que o total de RUs por minuto. Se você tiver um fluxo de dados com um alto número de partições Spark, definir um throughput de orçamento permitirá mais equilíbrio entre essas partições.

## <a name="join-performance"></a>Participe do desempenho

Gerenciar o desempenho de adesões no seu fluxo de dados é uma operação muito comum que você executará durante todo o ciclo de vida de suas transformações de dados. No ADF, os fluxos de dados não exigem que os dados sejam classificados antes de se juntarem, pois essas operações são realizadas à medida que o hash se junta ao Spark. No entanto, você pode se beneficiar de um desempenho melhor com a otimização do "Broadcast" Join. Isso evitará embaralhar empurrando para baixo o conteúdo de ambos os lados do seu relacionamento de adesão para o nó Spark. Isso funciona bem para tabelas menores que são usadas para busca de referência. Tabelas maiores que podem não caber na memória do nó não são boas candidatas à otimização de transmissão.

Outra otimização do Join é construir suas junções de tal forma que evite a tendência da Spark de implementar cross joins. Por exemplo, quando você inclui valores literais em suas condições de adesão, a Spark pode ver que como um requisito para executar um produto cartesiano completo primeiro, em seguida, filtrar os valores unidos. Mas se você garantir que você tem valores de coluna em ambos os lados da sua condição de adesão, você pode evitar este produto cartesiano induzido por Faísca e melhorar o desempenho de suas entradas e fluxos de dados.

## <a name="next-steps"></a>Próximas etapas

Veja outros artigos de Fluxo de Dados relacionados ao desempenho:

- [Guia otimizar fluxo de dados](concepts-data-flow-overview.md#optimize)
- [Atividade de Fluxo de Dados](control-flow-execute-data-flow-activity.md)
- [Monitore o desempenho do fluxo de dados](concepts-data-flow-monitoring.md)
