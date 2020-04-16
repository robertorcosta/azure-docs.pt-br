---
title: Transformação de origem no mapeamento do fluxo de dados
description: Saiba como configurar uma transformação de origem no mapeamento do fluxo de dados.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: e4f1369aa850a244128da470aee81b2efc2e09be
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413203"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Transformação de origem no mapeamento do fluxo de dados 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Uma transformação de origem configura sua fonte de dados para o fluxo de dados. Ao projetar fluxos de dados, seu primeiro passo será sempre configurar uma transformação de origem. Para adicionar uma fonte, clique na caixa **Adicionar origem** na tela de fluxo de dados.

Todo fluxo de dados requer pelo menos uma transformação de fonte, mas você pode adicionar quantas fontes forem necessárias para concluir suas transformações de dados. Você pode juntar essas fontes com uma união, uma olhada ou uma transformação sindical.

Cada transformação de origem está associada a exatamente um conjunto de dados da Fábrica de Dados. O conjunto de dados define a forma e a localização dos dados para os quais você deseja escrever ou ler. Se usar um conjunto de dados baseado em arquivos, você pode usar curingas e listas de arquivos na sua fonte para trabalhar com mais de um arquivo por vez.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Conectores de origem suportados no mapeamento do fluxo de dados

O Mapeamento do Fluxo de Dados segue uma abordagem de extrato, carga, transformação (ELT) e trabalha com conjuntos de dados *de preparação* que estão todos no Azure. Atualmente, os seguintes conjuntos de dados podem ser usados em uma transformação de origem:
    
* [Armazenamento Azure Blob](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Texto, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Text, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Banco de Dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

As configurações específicas desses conectores estão localizadas na guia **Opções de Origem.** 

A Fábrica de Dados Azure tem acesso a mais de [90 conectores nativos](connector-overview.md). Para incluir os dados dessas outras fontes no seu fluxo de dados, use a Atividade de Cópia para carregar esses dados em uma das áreas de preparação suportadas.

## <a name="source-settings"></a>Configurações de origem

Depois de adicionar uma fonte, configure através da guia **Configurações de** origem. Aqui você pode escolher ou criar o conjunto de dados em que seus pontos de origem. Você também pode selecionar opções de esquema e amostragem para seus dados.

![Guia configurações de origem](media/data-flow/source1.png "Guia configurações de origem")

**Conexão de teste:** Teste se o serviço de faísca do fluxo de dados pode ou não se conectar com sucesso ao serviço vinculado usado no conjunto de dados de origem. O modo de depuração deve estar ativado para que este recurso seja ativado.

**Deriva de esquema:** [Schema Drift](concepts-data-flow-schema-drift.md) é a capacidade da fábrica de dados de lidar nativamente com esquemas flexíveis em seus fluxos de dados sem precisar definir explicitamente as alterações da coluna.

* Verifique a caixa **de deriva do esquema Permitir** se as colunas de origem mudarem com freqüência. Esta configuração permite que todos os campos de origem de entrada fluam através das transformações para a pia.

* A escolha **de tipos de colunas derivadas infer** instruirá a fábrica de dados para detectar e definir tipos de dados para cada nova coluna descoberta. Com este recurso desligado, todas as colunas à deriva serão de cadeia de tipo.

**Validar o esquema:** Se o esquema de validação for selecionado, o fluxo de dados falhará se os dados de origem de entrada não corresponderem ao esquema definido do conjunto de dados.

**Contagem de linhas de sumidria:** O campo de contagem de linhas de salto especifica quantas linhas devem ser ignoradas no início do conjunto de dados.

**Amostragem:** Habilite a amostragem para limitar o número de linhas da sua fonte. Use esta configuração quando testar ou amostrar dados da sua fonte para fins de depuração.

**Linhas multilinhas:** Selecione linhas multilinhas se o arquivo de texto de origem contiver valores de seqüência que abrangem várias linhas, ou seja, novas linhas dentro de um valor. Esta configuração só está disponível em conjuntos de dados DelimitedText.

Para validar sua fonte está configurado corretamente, ative o modo de depuração e busque uma visualização de dados. Para obter mais informações, consulte [o modo Debug](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Quando o modo de depuração estiver ligado, a configuração de limite de linha nas configurações de depuração substituirá a configuração de amostragem na fonte durante a visualização de dados.

## <a name="projection"></a>Projeção

Como esquemas em conjuntos de dados, a projeção em uma fonte define as colunas, tipos e formatos de dados a partir dos dados de origem. Para a maioria dos tipos de conjunto de dados, como SQL e Parquet, a projeção em uma fonte é fixada para refletir o esquema definido em um conjunto de dados. Quando seus arquivos de origem não são fortemente digitados (por exemplo, arquivos csv planos em vez de arquivos Parquet), você pode definir os tipos de dados para cada campo na transformação de origem.

![Configurações na guia Projeção](media/data-flow/source3.png "Projeção")

Se o seu arquivo de texto não tiver um esquema definido, selecione **Detectar o tipo** de dados para que a Fábrica de Dados apareça e infera os tipos de dados. Selecione **Definir formato padrão** para detectar automaticamente os formatos de dados padrão.

**O esquema de redefinição** redefine a projeção para o que é definido no conjunto de dados referenciado.

Você pode modificar os tipos de dados da coluna em uma transformação de coluna derivada no fluxo para baixo. Use uma transformação selecionada para modificar os nomes das colunas.

### <a name="import-schema"></a>Esquema de importação

O botão **Esquema de importação** na guia **Projeção** permite que você use um cluster de depuração ativa para criar uma projeção de esquema. Disponível em todos os tipos de origem, a importação do esquema aqui irá substituir a projeção definida no conjunto de dados. O objeto do conjunto de dados não será alterado.

Isso é útil em conjuntos de dados como Avro e CosmosDB que suportam estruturas de dados complexas que não exigem definições de esquema para existir no conjunto de dados.

## <a name="optimize-the-source-transformation"></a>Otimizar a transformação de origem

Na guia **Otimizar** para a transformação de origem, você pode ver um tipo de partição **de Origem.** Esta opção só está disponível quando sua fonte é o Banco de Dados SQL do Azure. Isso porque a Fábrica de Dados tenta fazer conexões paralelas para executar grandes consultas contra a fonte do Banco de Dados SQL.

![Configurações de partição de origem](media/data-flow/sourcepart3.png "particionamento")

Você não precisa particionar dados na fonte do banco de dados SQL, mas partições são úteis para grandes consultas. Você pode basear sua partição em uma coluna ou em uma consulta.

### <a name="use-a-column-to-partition-data"></a>Use uma coluna para particionar dados

Na sua tabela de origem, selecione uma coluna para partição. Também defina o número de partições.

### <a name="use-a-query-to-partition-data"></a>Use uma consulta para particionar dados

Você pode optar por particionar as conexões com base em uma consulta. Digite o conteúdo de um predicado WHERE. Por exemplo, entrar no ano > 1980.

Para obter mais informações sobre otimização no fluxo de dados de mapeamento, consulte a [guia Otimizar](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Próximas etapas

Comece a construir uma [transformação de coluna derivada](data-flow-derived-column.md) e uma [transformação seleto.](data-flow-select.md)
