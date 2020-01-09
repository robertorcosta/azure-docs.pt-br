---
title: Indexar um conjunto de dados grandes usando indexadores internos
titleSuffix: Azure Cognitive Search
description: Estratégias para indexação de dados grandes ou indexação computacionalmente intensiva por meio do modo de lote, de origem e de técnicas para indexação agendada, paralela e distribuída.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b4b6c57b08de07cae431f015c8d8f53cdf3a50a4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460731"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Como indexar grandes conjuntos de dados no Azure Pesquisa Cognitiva

À medida que os volumes de dados crescem ou as necessidades de processamento mudam, você pode descobrir que as estratégias de indexação simples ou padrão não são mais práticas. Para o Azure Pesquisa Cognitiva, há várias abordagens para acomodar conjuntos de dados maiores, variando de como você estrutura uma solicitação de carregamento de dados, para usar um indexador específico de origem para cargas de trabalho agendadas e distribuídas.

As mesmas técnicas também se aplicam a processos de execução longa. Em particular, as etapas descritas em [indexação paralela](#parallel-indexing) são úteis para indexação computacionalmente intensiva, como análise de imagem ou processamento de linguagem natural em um [pipeline de enriquecimento de ia](cognitive-search-concept-intro.md).

As seções a seguir exploram três técnicas para indexar grandes quantidades de dados.

## <a name="option-1-pass-multiple-documents"></a>Opção 1: passar vários documentos

Um dos mecanismos mais simples para indexação de um conjunto de dados maior é enviar vários documentos ou registros em uma única solicitação. Como a carga inteira é menor que 16 MB, uma solicitação pode manipular até 1000 documentos em uma operação de upload em massa. Esses limites se aplicam se você estiver usando a [API REST de adicionar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ou o [método de índice](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) no SDK do .net. Para qualquer uma das APIs, você deve empacotar 1000 documentos no corpo de cada solicitação.

A indexação de lotes é implementada em solicitações individuais usando REST, .NET ou por meio de indexadores. Alguns indexadores operam sob limites diferentes. Especificamente, a indexação de BLOb do Azure define o tamanho do lote em 10 documentos ao reconhecer o maior tamanho médio do documento. Para indexadores com base em [Criar o indexador do API REST](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer), pode-se definir o `BatchSize` argumento para personalizar essa configuração para corresponder melhor as características de seus dados. 

> [!NOTE]
> Para manter o tamanho do documento inativo, evite adicionar dados não consultáveis a um índice. Imagens e outros dados binários não podem ser diretamente consultados e não devem ser armazenados no índice. Para integrar dados que não podem ser consultados aos resultados da pesquisa, defina um campo não pesquisável que armazene uma referência uma URL para o recurso.

## <a name="option-2-add-resources"></a>Opção 2: adicionar recursos

Serviços que são provisionados em um dos [ tipos de preço Standard](search-sku-tier.md) geralmente têm subutilizado a capacidade para armazenamento e cargas de trabalho (consultas ou indexação), o que [aumenta as contagens de partição e réplica ](search-capacity-planning.md) uma solução óbvia para acomodar grandes conjuntos de dados. Para obter melhores resultados, ambos os recursos são necessários: partições para armazenamento e réplicas para o trabalho de ingestão de dados.

O aumento de réplicas e partições são eventos faturáveis que aumentam seu custo, mas, a menos que você esteja indexando continuamente sob a carga máxima, você pode adicionar escala para a duração do processo de indexação e ajustar os níveis de recursos de volta após a indexação Finaliza.

## <a name="option-3-use-indexers"></a>Opção 3: usar indexadores

[Indexadores](search-indexer-overview.md) são usados para rastrear fontes de dados do Azure com suporte para conteúdo pesquisável. Enquanto não são especificamente destinados a indexação em larga escala, vários recursos do indexador são particularmente úteis para acomodar grandes conjuntos de dados:

+ Agendadores permitem dividir a indexação em intervalos regulares para que se possa distribuí-la ao longo do tempo.
+ A indexação agendada pode retomar no último ponto de interrupção conhecido. Se uma fonte de dados não é rastreada totalmente dentro de uma janela de 24 horas, o indexador retomará a indexação no dia dois e em onde ela parou.
+ A partição de dados em fontes de dados individuais menores habilita o processamento paralelo. Você pode dividir os dados de origem em componentes menores, como em vários contêineres no armazenamento de BLOBs do Azure e, em seguida, criar vários [objetos de fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) correspondentes no Azure pesquisa cognitiva que podem ser indexados em paralelo.

> [!NOTE]
> Os indexadores são específicos da fonte de dados, portanto usar uma abordagem do indexador só é viável para fontes de dados selecionadas no Azure: banco de dados [SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [armazenamento de BLOBs](search-howto-indexing-azure-blob-storage.md), [armazenamento de tabelas](search-howto-indexing-azure-tables.md) [Cosmos DB](search-howto-index-cosmosdb.md).

### <a name="scheduled-indexing"></a>Indexação agendada em

O agendando indexado é um mecanismo importante para processar grandes conjuntos de dados, assim como processos de execução lenta como a análise de imagens em um pipeline de pesquisa cognitiva. O processamento do indexador opera em uma janela de 24 horas. Se o processamento não for concluído em até 24 horas, o agendamento de comportamentos do indexador poderá entrar em ação. 

Por design, a indexação agendada começa em intervalos específicos, normalmente com o trabalho concluído antes da retomada no próximo intervalo agendado. No entanto, se o processamento não for concluído dentro do intervalo, o indexador será interrompido (por esgotar o tempo). No próximo intervalo, o processamento será retomado de onde parou, com o sistema controlando onde isso ocorreu. 

Em termos práticos, para cargas de índice que abrangem vários dias, você pode colocar o indexador em um agendamento de 24 horas. Quando a indexação é retomada para o próximo ciclo de 24 horas, ela é reiniciada no último documento válido conhecido. Dessa forma, um indexador pode percorrer uma lista de pendências de documentos em uma série de dias até que todos os documentos não processados sejam processados. Para obter mais informações sobre essa abordagem, confira [Indexando conjuntos de dados grandes no Armazenamento de Blob do Azure](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets). Para obter mais informações sobre como definir agendas em geral, consulte [criar API REST do indexador](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax) ou veja [como agendar indexadores para o Azure pesquisa cognitiva](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Indexação paralela

Um estratégia de indexação paralela baseia-se na indexação de várias fontes de dados de forma sincronizada, onde cada definição de fonte de dados especifica um subconjunto dos dados. 

Para requisitos de indexação computacionalmente intensivos e não rotineiros - como o OCR em documentos digitalizados em um pipeline de pesquisa cognitiva, análise de imagem ou processamento de linguagem natural - um estratégia de indexação de paralelo geralmente é a abordagem correta para a conclusão de um processo de execução longa no menor tempo. Para eliminar ou reduzir solicitações de consulta, a indexação paralela em um serviço que não esteja tratando simultaneamente de solicitações de consulta pode ser a melhor opção de estratégia para trabalhar em um grande corpo de conteúdo de processamento lento. 

O processamento paralelo tem estes elementos:

+ Subdividir a fonte de dados entre vários contêineres ou várias pastas virtuais dentro do mesmo contêiner. 
+ Mapeie cada conjunto de dados para sua própria [fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source), emparelhado com seu próprio [indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ Para pesquisa cognitiva, referencie o mesmo [conjunto de habilidades](https://docs.microsoft.com/rest/api/searchservice/create-skillset) em cada definição de indexador.
+ Grave no mesmo índice de pesquisa de destino. 
+ Agende todos os indexadores para serem executados ao mesmo tempo.

> [!NOTE]
> No Azure Pesquisa Cognitiva, você não pode atribuir réplicas ou partições individuais para indexação ou processamento de consulta. O sistema determina como os recursos são usados. Para entender o impacto no desempenho da consulta, você pode tentar a indexação paralela em um ambiente de teste antes de distribuí-lo para produção.  

### <a name="how-to-configure-parallel-indexing"></a>Como configurar a indexação paralela

Para os indexadores, a capacidade de processamento geralmente é baseada em um subsistema de indexador para cada SU (unidade de serviço) usada pelo serviço de pesquisa. Vários indexadores simultâneos são possíveis nos serviços de Pesquisa Cognitiva do Azure provisionados nas camadas básica ou Standard com pelo menos duas réplicas. 

1. No [portal do Azure](https://portal.azure.com), na página **Visão geral** do painel do serviço de pesquisa, verifique o **Tipo de preço** para confirmar se ele permite a indexação paralela. Os níveis Básico e Standard oferecem várias réplicas.

2. Em **Configurações** > **Escala**, [aumente as réplicas](search-capacity-planning.md) para o processamento paralelo: uma réplica adicional para cada carga de trabalho do indexador. Deixe um número suficiente para o volume de consulta existente. Não é vantajoso sacrificar as cargas de trabalho de consulta para indexação.

3. Distribua dados em vários contêineres em um nível que os indexadores do Azure Pesquisa Cognitiva podem alcançar. Podem ser várias tabelas no Banco de Dados SQL do Azure, vários contêineres no Armazenamento de Blobs do Azure ou várias coleções. Defina um objeto de fonte de dados para cada tabela ou contêiner.

4. Crie e agende vários indexadores a serem executados em paralelo:

   + Considere um serviço com seis réplicas. Configure seis indexadores, cada um mapeado para uma fonte de dados que contenha um sexto do conjunto de dados para uma divisão de 6 partes de todo o conjunto de dados. 

   + Aponte cada indexador para o mesmo índice. Para cargas de trabalho de pesquisa cognitiva, aponte cada indexador para o mesmo conjunto de habilidades.

   + Dentro de cada definição de indexador, agende o mesmo padrão de execução de tempo de execução. Por exemplo, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` cria uma agenda em 15/05/2018 em todos os indexadores, em execução em intervalos de oito horas.

No horário agendado, todos os indexadores começam a execução, o carregamento de dados, a aplicação de enriquecimentos (se você tiver configurado um pipeline de pesquisa cognitiva) e a gravação no índice. O Azure Pesquisa Cognitiva não bloqueia o índice para atualizações. As gravações simultâneas são gerenciadas, com repetição quando uma gravação específica não é bem-sucedida na primeira tentativa.

> [!Note]
> Ao aumentar réplicas, considere aumentar a contagem de partições se o tamanho do índice for projetado para aumentar significativamente. As partições armazenam fatias de conteúdo indexado. Quanto mais partições houver, menor será a fatia que cada uma precisará armazenar.

## <a name="see-also"></a>Consulte também

+ [Visão geral do indexador](search-indexer-overview.md)
+ [Indexando no portal](search-import-data-portal.md)
+ [Indexador do Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador de Banco de dados do Azure Cosmos](search-howto-index-cosmosdb.md)
+ [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Segurança no Azure Pesquisa Cognitiva](search-security-overview.md)
