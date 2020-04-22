---
title: Aceleração da consulta de armazenamento do Lago de Dados Do Azure (visualização)
description: A aceleração de consultas (preview) é um novo recurso para o Azure Data Lake Storage que permite que aplicativos e frameworks de análise otimizem drasticamente o processamento de dados, recuperando apenas os dados necessários para uma operação de processamento.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 191a3280075403c8c5b57c5ffca1c7707d1ddb11
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771814"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Aceleração da consulta de armazenamento do Lago de Dados Do Azure (visualização)

A aceleração de consultas (preview) é um novo recurso para o Azure Data Lake Storage que permite que aplicativos e frameworks de análise otimizem drasticamente o processamento de dados, recuperando apenas os dados necessários para realizar uma determinada operação. Isso reduz o tempo e o poder de processamento necessários para obter insights críticos sobre os dados armazenados.

> [!NOTE]
> O recurso de aceleração de consultas está em visualização pública e está disponível nas regiões Central e Central do Canadá. Para rever limitações, consulte o artigo [Questões Conhecidas.](data-lake-storage-known-issues.md) Para se inscrever na pré-visualização, consulte [este formulário](https://aka.ms/adls/qa-preview-signup).  

## <a name="overview"></a>Visão geral

A aceleração da consulta aceita *predicados filtrantes* e projeções de *colunas* que permitem que os aplicativos filtrelinhas e colunas no momento em que os dados são lidos a partir do disco. Apenas os dados que atendem às condições de um predicado são transferidos pela rede para o aplicativo. Isso reduz a latência da rede e o custo de computação.  

Você pode usar o SQL para especificar os predicados do filtro de linha e as projeções da coluna em uma solicitação de aceleração de consulta. Uma solicitação processa apenas um arquivo. Portanto, características relacionais avançadas do SQL, como a adesão e o grupo por agregados, não são suportadas. A aceleração de consulta suporta dados formatados CSV e JSON como entrada para cada solicitação.

O recurso de aceleração de consulta não se limita ao Data Lake Storage (contas de armazenamento que têm o namespace hierárquico ativado neles). A aceleração de consulta é completamente compatível com as bolhas em contas de armazenamento que **não** têm um namespace hierárquico ativado neles. Isso significa que você pode obter a mesma redução na latência da rede e calcular custos quando você processa dados que você já armazenou como blobs em contas de armazenamento.

Para obter um exemplo de como usar a aceleração de consultas em um aplicativo cliente, consulte Filter data data usando a aceleração de [consulta do Azure Data Lake Storage](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Fluxo de dados

O diagrama a seguir ilustra como um aplicativo típico usa aceleração de consulta para processar dados.

> [!div class="mx-imgBorder"]
> ![Visão geral da aceleração da consulta](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. O aplicativo cliente solicita dados de arquivo especificando predicados e projeções de coluna.

2. A aceleração da consulta analisa a consulta SQL especificada e distribui trabalho para analisar e filtrar dados.

3. Os processadores lêem os dados do disco, analisam os dados usando o formato apropriado e, em seguida, filtram os dados aplicando os predicados e projeções de coluna especificados.

4. A aceleração de consulta combina os fragmentos de resposta para transmitir de volta ao aplicativo do cliente.

5. O aplicativo do cliente recebe e analisa a resposta transmitida. O aplicativo não precisa filtrar dados adicionais e pode aplicar o cálculo ou transformação desejado diretamente.

## <a name="better-performance-at-a-lower-cost"></a>Melhor desempenho a um custo menor

A aceleração de consulta otimiza o desempenho reduzindo a quantidade de dados que são transferidos e processados pelo seu aplicativo.

Para calcular um valor agregado, os aplicativos geralmente recuperam **todos** os dados de um arquivo e, em seguida, processam e filtram os dados localmente. Uma análise dos padrões de entrada/saída para cargas de trabalho de análise revela que os aplicativos normalmente requerem apenas 20% dos dados que lêem para realizar qualquer cálculo. Essa estatística é verdadeira mesmo após a aplicação de técnicas como [poda de partição.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning) Isso significa que 80% desses dados são transferidos desnecessariamente pela rede, analisados e filtrados por aplicativos. Este padrão, essencialmente projetado para remover dados desnecessários, incorre em um custo significativo de computação.  

Embora o Azure tenha uma rede líder do setor, em termos de throughput e latência, a transferência dedados desnecessariamente de dados em essa rede ainda é cara para o desempenho do aplicativo. Ao filtrar os dados indesejados durante a solicitação de armazenamento, a aceleração da consulta elimina esse custo.

Além disso, a carga da CPU necessária para analisar e filtrar dados desnecessários requer que seu aplicativo prover um número maior e VMs maiores, a fim de fazer seu trabalho. Ao transferir essa carga de computação para a aceleração da consulta, os aplicativos podem realizar uma economia significativa de custos.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Aplicativos que podem se beneficiar da aceleração de consultas

A aceleração de consultas foi projetada para estruturas de análise distribuída e aplicativos de processamento de dados. 

Estruturas de análise distribuída, como Apache Spark e Apache Hive, incluem uma camada de abstração de armazenamento dentro da estrutura. Esses mecanismos também incluem otimizadores de consulta que podem incorporar o conhecimento dos recursos do serviço de I/O subjacente ao determinar um plano de consulta ideal para consultas de usuários. Esses frameworks estão começando a integrar a aceleração da consulta. Como resultado, os usuários desses frameworks verão uma latência de consulta melhorada e um custo total de propriedade menor sem ter que fazer nenhuma alteração nas consultas. 

A aceleração de consultas também foi projetada para aplicativos de processamento de dados. Esses tipos de aplicativos normalmente realizam transformações de dados em larga escala que podem não levar diretamente a insights de análise, de modo que nem sempre usam frameworks de análise distribuída estabelecidos. Esses aplicativos geralmente têm uma relação mais direta com o serviço de armazenamento subjacente para que possam se beneficiar diretamente de recursos como aceleração de consultas. 

Para um exemplo de como um aplicativo pode integrar a aceleração de consultas, consulte [Filter data data usando a aceleração da consulta do Azure Data Lake Storage](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Preços

Devido ao aumento da carga de computação dentro do serviço Azure Data Lake Storage, o modelo de preços para usar a aceleração de consultas difere do modelo normal de transação Do Lago de Dados do Azure. A aceleração da consulta cobra um custo pela quantidade de dados digitalizados, bem como um custo pela quantidade de dados devolvidos ao chamador.

Apesar da mudança no modelo de faturamento, o modelo de preços da aceleração da Consulta foi projetado para reduzir o custo total de propriedade para uma carga de trabalho, dada a redução dos custos muito mais caros da VM.

## <a name="next-steps"></a>Próximas etapas

- [Formulário de inscrição para aceleração de consulta](https://aka.ms/adls/qa-preview-signup)    
- [Filtrar dados usando a aceleração da consulta de armazenamento do Lago de Dados do Azure](data-lake-storage-query-acceleration-how-to.md)
- [Referência de linguagem SQL de aceleração de consulta (visualização)](query-acceleration-sql-reference.md)
- Referência de API de aceleração de consulta REST



