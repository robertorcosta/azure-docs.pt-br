---
title: Transformação de origem no fluxo de dados de mapeamento
description: Saiba como configurar uma transformação de origem no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/27/2020
ms.openlocfilehash: 22f524ed4f01ad5b3be110008a82cd31c5c476c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827860"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformação de origem no fluxo de dados de mapeamento 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma transformação de origem configura sua fonte de dados para o fluxo de dados. Ao criar fluxos de dados, sua primeira etapa sempre estará configurando uma transformação de origem. Para adicionar uma origem, clique na caixa **Adicionar origem** na tela fluxo de dados.

Cada fluxo de dados requer pelo menos uma transformação de origem, mas você pode adicionar quantas fontes forem necessárias para concluir as transformações de dados. Você pode unir essas fontes junto com uma transformação junção, pesquisa ou União.

Cada transformação de origem é associada a exatamente um conjunto de um DataSet ou serviço vinculado. O DataSet define a forma e o local dos dados que você deseja gravar ou ler. Se estiver usando um conjunto de informações baseado em arquivo, você poderá usar curingas e listas de arquivos em sua fonte para trabalhar com mais de um arquivo por vez.

## <a name="inline-datasets"></a>Conjuntos de valores embutidos

A primeira decisão que você faz ao criar uma transformação de origem é se suas informações de origem são definidas dentro de um objeto de conjunto de dados ou dentro da transformação de origem. A maioria dos formatos só está disponível em um ou outro. Consulte o documento do conector apropriado para saber como usar um conector específico.

Quando há suporte para um formato tanto para dentro quanto para um objeto DataSet, há benefícios para ambos. Objetos DataSet são entidades reutilizáveis que podem ser aproveitadas em outros fluxos de dados e atividades como Copy. Eles são especialmente úteis ao usar um esquema protegido. Os conjuntos de dado não são baseados no Spark e, ocasionalmente, talvez seja necessário substituir certas configurações ou projeção de esquema na transformação de origem.

Os conjuntos de linhas embutidos são recomendados ao usar esquemas flexíveis, instâncias de origem única ou fontes com parâmetros. Se sua fonte estiver muito parametrizada, os conjuntos de linhas embutidos permitem que você não crie um objeto "fictício". Os conjuntos de dados embutidos são baseados no Spark e suas propriedades são nativas para o fluxo.

Para usar um conjunto de linhas embutido, selecione o formato desejado no seletor de **tipo de origem** . Em vez de selecionar um conjunto de fonte de origem, selecione o serviço vinculado ao qual você deseja se conectar.

![Conjunto de linhas embutido](media/data-flow/inline-selector.png "Conjunto de linhas embutido")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> Tipos de origem com suporte

O mapeamento de fluxo de dados segue uma abordagem de extração, carregamento, transformação (ELT) e funciona com conjuntos de dados de *preparo* que estão todos no Azure. Atualmente, os seguintes conjuntos de valores podem ser usados em uma transformação de origem:

| Connector | Formatar | Conjunto de linhas/embutido |
| --------- | ------ | -------------- |
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta (visualização)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Modelo de dados comuns (visualização)](format-common-data-model.md#source-properties)<br>[Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta (visualização)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Banco de dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (API do SQL)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

As configurações específicas para esses conectores estão localizadas na guia **Opções de origem** . Exemplos de script de fluxo de dados e informações nessas configurações estão localizados na documentação do conector. 

O Azure Data Factory tem acesso a aproximadamente [90 conectores nativos](connector-overview.md). Para incluir dados dessas outras fontes em seu fluxo de dados, use a atividade de cópia para carregar esses dados em uma das áreas de preparo com suporte.

## <a name="source-settings"></a>Configurações de origem

Depois de adicionar uma fonte, configure por meio da guia **configurações de origem** . Aqui, você pode escolher ou criar o conjunto de os pontos de origem em. Você também pode selecionar opções de esquema e amostragem para seus dados. 

Os valores de desenvolvimento para parâmetros de conjunto de um podem ser definidos nas [configurações de depuração](concepts-data-flow-debug-mode.md) (requer que o modo de depuração esteja ativado).

![Guia Configurações de origem](media/data-flow/source1.png "Guia Configurações de origem")

**Nome do fluxo de saída:** O nome da transformação de origem.

**Tipo de origem:** Escolha se deseja usar um conjunto de linhas ou um objeto DataSet existente.

**Testar conexão:** Teste se o serviço Spark do fluxo de dados pode ou não se conectar com êxito ao serviço vinculado usado no conjunto de dados de origem. O modo de depuração deve estar ativado para que este recurso seja habilitado.

**Descompasso de esquema:** a [descompasso de esquema](concepts-data-flow-schema-drift.md) é a capacidade do data Factory de lidar nativamente com esquemas flexíveis em seus fluxos de dados sem a necessidade de definir explicitamente as alterações na coluna.

* Marque a caixa **permitir descompasso de esquema** se as colunas de origem forem alteradas com frequência. Essa configuração permite que todos os campos de origem de entrada fluam por meio das transformações para o coletor.

* Escolher **inferir tipos de coluna descompassos** instruirá data Factory a detectar e definir tipos de dados para cada nova coluna descoberta. Com esse recurso desativado, todas as colunas descompassos serão do tipo cadeia de caracteres.

**Validar esquema:** Se a validação de esquema for selecionada, o fluxo de dados não será executado se os dados de origem de entrada não corresponderem ao esquema definido do DataSet.

**Ignorar contagem de linhas:** O campo ignorar contagem de linhas especifica quantas linhas ignorar no início do conjunto de um.

**Amostragem:** Habilite a amostragem para limitar o número de linhas de sua origem. Use essa configuração quando você testar ou criar amostras de dados de sua fonte para fins de depuração.

Para validar se a fonte está configurada corretamente, ative o modo de depuração e busque uma visualização de dados. Para obter mais informações, consulte [modo de depuração](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Quando o modo de depuração estiver ativado, a configuração de limite de linha nas configurações de depuração substituirá a configuração de amostragem na origem durante a visualização de dados.

## <a name="source-options"></a>Opções de origem

A guia Opções de origem contém configurações específicas para o conector e o formato escolhido. Para obter mais informações e exemplos, consulte a [documentação do conector](#supported-sources)relevante.

## <a name="projection"></a>Projeção

Como os esquemas em conjuntos de dados, a projeção em uma fonte define as colunas, os tipos e os formatos dos dados de origem. Para a maioria dos tipos de conjunto de conjuntos, como SQL e parquet, a projeção em uma fonte é fixada para refletir o esquema definido em um conjunto de um DataSet. Quando os arquivos de origem não são fortemente tipados (por exemplo, arquivos CSV simples em vez de arquivos parquet), você pode definir os tipos de dados para cada campo na transformação de origem.

![Configurações na guia projeção](media/data-flow/source3.png "Projeção")

Se o arquivo de texto não tiver um esquema definido, selecione **detectar tipo de dados** para que data Factory obterá amostras e inferirá os tipos de dados. Selecione **definir formato padrão** para detectar automaticamente os formatos de dados padrão.

**Redefinir esquema** redefine a projeção para o que é definido no conjunto de valores referenciado.

Você pode modificar os tipos de dados de coluna em uma transformação de coluna derivada de fluxo inferior. Use uma transformação selecionar para modificar os nomes de coluna.

### <a name="import-schema"></a>Importar esquema

O botão **importar esquema** na guia **projeção** permite que você use um cluster de depuração ativo para criar uma projeção de esquema. Disponível em cada tipo de fonte, a importação do esquema aqui substituirá a projeção definida no conjunto de um. O objeto DataSet não será alterado.

Isso é útil em conjuntos de dados, como Avro e Azure Cosmos DB, que dão suporte a estruturas de dado complexas não exigem que definições de esquema existam no DataSet. Para conjuntos de itens embutidos, essa é a única maneira de fazer referência a metadados de coluna sem descompasso de esquema.

## <a name="optimize-the-source-transformation"></a>Otimizar a transformação de origem

A guia **otimizar** permite a edição de informações de partição em cada etapa de transformação. Na maioria dos casos, o **uso do particionamento atual** otimizará a estrutura de particionamento ideal para uma origem.

Se você estiver lendo de uma fonte de banco de dados SQL do Azure, o particionamento de **origem** personalizado provavelmente lerá os dados de forma mais rápida. O ADF lerá consultas grandes fazendo conexões com seu banco de dados em paralelo. Esse particionamento de origem pode ser feito em uma coluna ou usando uma consulta.

![Configurações de partição de origem](media/data-flow/sourcepart3.png "particionamento")

Para obter mais informações sobre a otimização no fluxo de dados de mapeamento, consulte a [guia otimizar](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Próximas etapas

Comece a criar seu fluxo de dados com uma [transformação de coluna derivada](data-flow-derived-column.md) e uma [transformação Select](data-flow-select.md).
