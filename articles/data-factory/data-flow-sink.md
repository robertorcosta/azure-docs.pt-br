---
title: Transformação do dissipador no mapeamento do fluxo de dados
description: Saiba como configurar uma transformação de sink no mapeamento do fluxo de dados.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: a728ec6f261b82d563aeb4a8f5c98bbd546f4400
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413223"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Transformação do dissipador no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Depois de transformar seus dados, você pode afundar os dados em um conjunto de dados de destino. Todo fluxo de dados requer pelo menos uma transformação de pia, mas você pode escrever para o máximo de pias necessário para completar seu fluxo de transformação. Para escrever em pias adicionais, crie novos fluxos através de novos ramos e divisões condicionais.

Cada transformação de pia está associada a exatamente um conjunto de dados da Fábrica de Dados. O conjunto de dados define a forma e a localização dos dados para os quais você deseja escrever.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Conectores de pia suportados no mapeamento do fluxo de dados

Atualmente, os seguintes conjuntos de dados podem ser usados em uma transformação de pia:
    
* [Armazenamento Azure Blob](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, Texto, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, Text, Parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, Text, Parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Banco de Dados SQL do Azure](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

As configurações específicas desses conectores estão localizadas na guia **Configurações.** 

A Fábrica de Dados Azure tem acesso a mais de [90 conectores nativos](connector-overview.md). Para gravar dados para essas outras fontes a partir do seu fluxo de dados, use a Atividade de Cópia para carregar esses dados de uma das áreas de preparação suportadas após a conclusão do fluxo de dados.

## <a name="sink-settings"></a>Configurações do dissipador

Depois de adicionar uma pia, configure através da guia **'Afundar'.** Aqui você pode escolher ou criar o conjunto de dados para o que sua pia grava. Abaixo está um vídeo explicando uma série de diferentes opções do Sink para tipos de arquivos delimitados de texto:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

![Configurações do dissipador](media/data-flow/sink-settings.png "Configurações da pia")

**Deriva de esquema:** [Schema Drift](concepts-data-flow-schema-drift.md) é a capacidade da fábrica de dados de lidar nativamente com esquemas flexíveis em seus fluxos de dados sem precisar definir explicitamente as alterações da coluna. Habilitar **Permitir deriva de esquema** para gravar colunas adicionais em cima do que é definido no esquema de dados do dissipador.

**Validar o esquema:** Se o esquema de validação for selecionado, o fluxo de dados falhará se qualquer coluna do esquema de origem recebida não for encontrada na projeção de origem ou se os tipos de dados não corresponderem. Use esta configuração para impor que os dados de origem atendam ao contrato de sua projeção definida. É muito útil em cenários de fonte de banco de dados para sinalizar que nomes de colunas ou tipos mudaram.

## <a name="field-mapping"></a>Mapeamento de campo

Semelhante a uma transformação Select, na guia **Mapeamento** da pia, você pode decidir quais colunas recebidas serão escritas. Por padrão, todas as colunas de entrada, incluindo colunas à deriva, são mapeadas. Isso é conhecido como **Auto-mapping**.

Quando você desativar o mapeamento automático, você terá a opção de adicionar mapeamentos fixos baseados em colunas ou mapeamentos baseados em regras. Os mapeamentos baseados em regras permitem que você escreva expressões com correspondência de padrões, enquanto o mapeamento fixo mapeará nomes de colunas lógicas e físicas. Para obter mais informações sobre mapeamento baseado em regras, consulte [padrões](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)de coluna no fluxo de dados de mapeamento .

## <a name="custom-sink-ordering"></a>Pedidos de pia personalizados

Por padrão, os dados são gravados em vários sinks em uma ordem não determinista. O mecanismo de execução gravará dados em paralelo à medida que a lógica de transformação for concluída e o pedido de pia pode variar a cada execução. Para especificar e solicitar o saque exato, habilite o **pedido de sacção personalizado** na guia geral do fluxo de dados. Quando ativado, as pias serão escritas sequencialmente em ordem crescente.

![Pedidos de pia personalizados](media/data-flow/custom-sink-ordering.png "Pedidos de pia personalizados")

## <a name="data-preview-in-sink"></a>Visualização de dados na pia

Ao buscar uma visualização de dados em um cluster de depuração, nenhum dado será gravado no seu dissipador. Um instantâneo de como os dados se parecem será devolvido, mas nada será escrito para o seu destino. Para testar a escrita de dados em sua pia, execute uma depuração do pipeline da tela do pipeline.

## <a name="next-steps"></a>Próximas etapas
Agora que você criou seu fluxo de dados, adicione uma [atividade de fluxo de dados ao seu pipeline](concepts-data-flow-overview.md).
