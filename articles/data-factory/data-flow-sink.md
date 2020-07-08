---
title: Transformação do coletor no fluxo de dados de mapeamento
description: Saiba como configurar uma transformação de coletor no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/03/2020
ms.openlocfilehash: 143c94527b947495709d2e94f107dc578e7f2866
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84610161"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformação do coletor no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Depois de terminar de transformar seus dados, grave-os em um repositório de destino usando a transformação coletor. Cada fluxo de dados requer pelo menos uma transformação de coletor, mas você pode gravar tantos coletores quantos forem necessários para concluir o fluxo de transformação. Para gravar em coletores adicionais, crie novos fluxos por meio de novas ramificações e divisões condicionais.

Cada transformação de coletor é associada a exatamente um objeto de conjunto de Azure Data Factory ou serviço vinculado. A transformação do coletor determina a forma e o local dos dados que você deseja gravar.

## <a name="inline-datasets"></a>Conjuntos de valores embutidos

Ao criar uma transformação do coletor, escolha se as informações do coletor estão definidas dentro de um objeto de conjunto de dados ou na transformação do coletor. A maioria dos formatos só está disponível em um ou outro. Consulte o documento do conector apropriado para saber como usar um conector específico.

Quando há suporte para um formato tanto para dentro quanto para um objeto DataSet, há benefícios para ambos. Objetos DataSet são entidades reutilizáveis que podem ser aproveitadas em outros fluxos de dados e atividades como Copy. Eles são especialmente úteis ao usar um esquema protegido. Os conjuntos de dado não são baseados no Spark e, ocasionalmente, talvez seja necessário substituir certas configurações ou projeção de esquema na transformação do coletor.

Os conjuntos de linhas embutidos são recomendados ao usar esquemas flexíveis, instâncias de coletor one-off ou coletores com parâmetros. Se o seu coletor for altamente parametrizado, conjuntos de linhas embutidos permitem que você não crie um objeto "fictício". Os conjuntos de dados embutidos são baseados no Spark e suas propriedades são nativas para o fluxo.

Para usar um conjunto de linhas embutido, selecione o formato desejado no seletor de **tipo de coletor** . Em vez de selecionar um conjunto de um banco de uma coletor, você seleciona o serviço vinculado ao qual deseja se conectar.

![Conjunto de linhas embutido](media/data-flow/inline-selector.png "Conjunto de linhas embutido")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a>Tipos de coletor com suporte

O mapeamento de fluxo de dados segue uma abordagem de extração, carregamento, transformação (ELT) e funciona com conjuntos de dados de *preparo* que estão todos no Azure. Atualmente, os seguintes conjuntos de valores podem ser usados em uma transformação de origem:

| Conector | Formatar | Conjunto de linhas/embutido |
| --------- | ------ | -------------- |
| [Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties) <br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties) <br> [Parquet](format-parquet.md#mapping-data-flow-properties)  | ✓/- <br> ✓/- <br> ✓/- <br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties) <br> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Modelo de dados comuns (visualização)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Banco de Dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [CosmosDB do Azure (API do SQL)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |

As configurações específicas para esses conectores estão localizadas na guia **configurações** . exemplos de script de fluxo de dados e informações nessas configurações estão localizados na documentação do conector. 

O Azure Data Factory tem acesso a aproximadamente [90 conectores nativos](connector-overview.md). Para gravar dados para essas outras fontes do fluxo de dados, use a atividade de cópia para carregar esses dados de um coletor com suporte.

## <a name="sink-settings"></a>Configurações do coletor

Depois de adicionar um coletor, configure por meio da guia **coletor** . Aqui, você pode escolher ou criar o conjunto de um no qual seu coletor grava. Veja abaixo um vídeo explicando várias opções de coletor diferentes para tipos de arquivo delimitados por texto:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Configurações do coletor](media/data-flow/sink-settings.png "Configurações do coletor")

**Descompasso de esquema:** a [descompasso de esquema](concepts-data-flow-schema-drift.md) é a capacidade do data Factory de lidar nativamente com esquemas flexíveis em seus fluxos de dados sem a necessidade de definir explicitamente as alterações na coluna. Habilite **permitir descompasso de esquemas** para gravar colunas adicionais sobre o que está definido no esquema de dados do coletor.

**Validar esquema:** Se validar esquema for selecionado, o fluxo de dados falhará se qualquer coluna do esquema de origem de entrada não for encontrada na projeção de origem ou se os tipos de dados não corresponderem. Use essa configuração para impor que os dados de origem atendam ao contrato de sua projeção definida. Ele é muito útil em cenários de origem de banco de dados para sinalizar que os nomes ou tipos de coluna foram alterados.

## <a name="field-mapping"></a>Mapeamento de campo

Semelhante a uma transformação selecionar, na guia **mapeamento** do coletor, você pode decidir quais colunas de entrada serão gravadas. Por padrão, todas as colunas de entrada, incluindo colunas descompassos, são mapeadas. Isso é conhecido como **mapeamento automático**.

Ao desativar o mapeamento automático, você terá a opção de adicionar mapeamentos fixos baseados em colunas ou mapeamentos baseados em regras. Os mapeamentos baseados em regras permitem escrever expressões com correspondência de padrões, enquanto o mapeamento fixo mapeará nomes de colunas lógicas e físicas. Para obter mais informações sobre mapeamento baseado em regras, consulte [padrões de coluna no fluxo de dados de mapeamento](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Ordenação de coletor personalizado

Por padrão, os dados são gravados em vários coletores em uma ordem não determinística. O mecanismo de execução gravará dados em paralelo, pois a lógica de transformação é concluída e a ordenação do coletor pode variar cada execução. Para especificar a ordenação exata do coletor, habilite a **ordenação personalizada do coletor** na guia Geral do fluxo de dados. Quando habilitado, os coletores serão gravados em sequência em ordem crescente.

![Ordenação de coletor personalizado](media/data-flow/custom-sink-ordering.png "Ordenação de coletor personalizado")

## <a name="data-preview-in-sink"></a>Visualização de dados no coletor

Ao buscar uma visualização de dados em um cluster de depuração, nenhum dado será gravado no coletor. Um instantâneo da aparência dos dados será retornado, mas nada será gravado no destino. Para testar a gravação de dados em seu coletor, execute uma depuração de pipeline na tela do pipeline.

## <a name="next-steps"></a>Próximas etapas
Agora que você criou o fluxo de dados, adicione uma [atividade de fluxo de dados ao seu pipeline](concepts-data-flow-overview.md).
