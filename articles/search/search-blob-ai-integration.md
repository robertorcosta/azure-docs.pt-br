---
title: Use AI para entender dados de armazenamento Blob
titleSuffix: Azure Cognitive Search
description: Adicione processamento de linguagem semântica, natural e análise de imagem aos blobs do Azure usando um pipeline de enriquecimento de IA na Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496441"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>Use AI para entender dados de armazenamento Blob

Os dados no armazenamento Azure Blob são frequentemente uma variedade de conteúdo não estruturado, como imagens, texto longo, PDFs e documentos do Office. Ao usar os recursos de IA no Azure Cognitive Search, você pode entender e extrair informações valiosas de blobs de várias maneiras. Exemplos de aplicação de IA em conteúdo blob incluem:

+ Extrair texto de imagens usando o OCR (Optical Character Recognition, reconhecimento óptico de caracteres)
+ Produzir uma descrição da cena ou tags de uma foto
+ Detectar idiomas e traduzir texto sumido em diferentes idiomas
+ Texto de processo com reconhecimento de entidade nomeado (NER) para encontrar referências a pessoas, datas, lugares ou organizações 

Embora você possa precisar apenas de uma dessas capacidades de IA, é comum combinar vários deles no mesmo pipeline (por exemplo, extrair texto de uma imagem digitalizada e, em seguida, encontrar todas as datas e locais mencionados nele). 

O enriquecimento da IA cria novas informações, capturadas como texto, armazenadas em campos. Após o enriquecimento, você pode acessar essas informações a partir de um índice de pesquisa através de pesquisa completa de texto ou enviar documentos enriquecidos de volta ao armazenamento Do Zure para alimentar novas experiências de aplicativos que incluem a exploração de dados para cenários de descoberta ou análise. 

Neste artigo, vemos o enriquecimento da IA através de uma lente ampla para que você possa compreender rapidamente todo o processo, desde a transformação de dados brutos em blobs, até informações consultadas em um índice de pesquisa ou em um armazenamento de conhecimento.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>O que significa "enriquecer" dados blob com IA

*O enriquecimento* de IA faz parte da arquitetura de indexação do Azure Cognitive Search que integra a IA incorporada da Microsoft ou a IA personalizada que você fornece. Ele ajuda você a implementar cenários completos onde você precisa processar blobs (tanto os existentes quanto os novos à medida que eles entram ou são atualizados), abrir todos os formatos de arquivo para extrair imagens e texto, extrair as informações desejadas usando vários recursos de IA e indexá-los em um índice de pesquisa para pesquisa rápida, recuperação e exploração. 

As entradas são suas bolhas, em um único recipiente, no armazenamento Azure Blob. Blobs podem ser quase qualquer tipo de texto ou dados de imagem. 

A saída é sempre um índice de pesquisa, usado para pesquisa rápida de texto, recuperação e exploração em aplicativos clientes. Além disso, a produção também pode ser uma *loja de conhecimento* que projeta documentos enriquecidos em blobs do Azure ou tabelas ajusçais para análise a jusante em ferramentas como Power BI ou em cargas de trabalho de ciência de dados.

No meio está a própria arquitetura do gasoduto. O pipeline é baseado no recurso *indexador,* ao qual você pode atribuir um *skillset*, que é composto de uma ou mais *habilidades* que fornecem a IA. O objetivo do pipeline é produzir *documentos enriquecidos* que entram como conteúdo bruto, mas captam estrutura, contexto e informações adicionais enquanto se deslocam pelo pipeline. Documentos enriquecidos são consumidos durante a indexação para criar índices invertidos e outras estruturas usadas em pesquisa de texto completa ou exploração e análise.

## <a name="start-with-services"></a>Comece com serviços

Você precisa de Pesquisa Cognitiva Azure e armazenamento Azure Blob. Dentro do armazenamento Blob, você precisa de um contêiner que forneça conteúdo de origem.

Você pode começar diretamente na página do portal da conta de armazenamento. Na página de navegação à esquerda, em **serviço Blob** clique em **Adicionar pesquisa cognitiva do Azure** para criar um novo serviço ou selecionar um já existente. 

Depois de adicionar o Azure Cognitive Search à sua conta de armazenamento, você pode seguir o processo padrão para enriquecer dados em qualquer fonte de dados do Azure. Recomendamos o **assistente de dados importação** no Azure Cognitive Search para uma introdução inicial fácil ao enriquecimento de IA. Este quickstart orienta você através das etapas: [Crie um pipeline de enriquecimento de IA no portal](cognitive-search-quickstart-blob.md). 

Nas seções a seguir, exploraremos mais componentes e conceitos.

## <a name="use-a-blob-indexer"></a>Use um indexador Blob

O enriquecimento de IA é um complemento a um pipeline de indexação, e na Pesquisa Cognitiva do Azure, esses gasodutos são construídos em cima de um *indexador*. Um indexador é um subserviço de reconhecimento de origem de dados equipado com lógica interna para dados de amostragem, leitura de dados de metadados, recuperação de dados e serialização de dados de formatos nativos em documentos JSON para importação subsequente. Os indexadores são frequentemente usados por si mesmos para importação, separados da IA, mas se você quiser construir um pipeline de enriquecimento de IA, você precisará de um indexador e um skillset para ir com ele. Esta seção destaca o indexador; a próxima seção se concentra em skillsets.

Blobs no Azure Storage são indexados usando o [indexador de armazenamento Azure Cognitive Search Blob](search-howto-indexing-azure-blob-storage.md). Você pode invocar este indexador usando o assistente **de dados Importação,** uma API REST ou o .NET SDK. Em código, você usa este indexador definindo o tipo e fornecendo informações de conexão que incluem uma conta do Azure Storage juntamente com um contêiner blob. Você pode subdefinir suas bolhas criando um diretório virtual, que você pode então passar como um parâmetro, ou filtrando uma extensão de tipo de arquivo.

Um indexador faz o "quebra-documento", abrindo uma bolha para inspecionar o conteúdo. Depois de se conectar à fonte de dados, é o primeiro passo no pipeline. Para dados blob, é aí que PDF, documentos de escritório, imagem e outros tipos de conteúdo são detectados. A quebra de documento com extração de texto não é custo. A quebra de documento com extração de imagem é cobrada a taxas que você pode encontrar na [página de preços](https://azure.microsoft.com/pricing/details/search/).

Embora todos os documentos sejam rachados, o enriquecimento só ocorre se você fornecer explicitamente as habilidades para fazê-lo. Por exemplo, se o seu pipeline consiste exclusivamente na análise de imagens, o texto em seu contêiner ou documentos será ignorado.

O indexador Blob vem com parâmetros de configuração e suporta o rastreamento de alterações se os dados subjacentes fornecerem informações suficientes. Você pode aprender mais sobre a funcionalidade principal no [indexador de armazenamento Azure Cognitive Search Blob](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Adicionar componentes de IA

O enriquecimento da IA refere-se a módulos que procuram padrões ou características e, em seguida, realizam uma operação em conformidade. Reconhecimento facial em fotos, descrições de texto de fotos, detecção de frases-chave em um documento e OCR (ou reconhecendo texto impresso ou escrito à mão em arquivos binários) são exemplos ilustrativos.

Na Pesquisa Cognitiva do Azure, *as habilidades* são os componentes individuais do processamento de IA que você pode usar autônomo, ou em combinação com outras habilidades. 

+ As habilidades incorporadas são apoiadas por Serviços Cognitivos, com análise de imagem baseada na Visão computacional e processamento de linguagem natural com base no Text Analytics. Para obter a lista completa, consulte [habilidades incorporadas para enriquecimento de conteúdo](cognitive-search-predefined-skills.md).

+ Habilidades personalizadas são código personalizado, embrulhadoem em uma [definição de interface](cognitive-search-custom-skill-interface.md) que permite a integração ao pipeline. Em soluções para clientes, é prática comum usar ambos, com habilidades personalizadas fornecendo módulos de IA de código aberto, de terceiros ou de primeira parte.

Um *skillset* é a coleção de habilidades usadas em um pipeline, e é invocada após a fase de quebra de documento disponibilizar conteúdo. Um indexador pode consumir exatamente uma habilidade, mas essa habilidade existe independentemente de um indexador para que você possa reutilizá-lo em outros cenários.

Habilidades personalizadas podem parecer complexas, mas podem ser simples e simples em termos de implementação. Se você tiver pacotes existentes que fornecem modelos de correspondência ou classificação de padrões, o conteúdo extraído das bolhas pode ser passado para esses modelos para processamento. Como o enriquecimento de IA é baseado em Azure, seu modelo também deve estar no Azure. Algumas metodologias comuns de hospedagem incluem o uso de Funções ou [Contêineres](https://github.com/Microsoft/SkillsExtractorCognitiveSearch) [do Azure.](cognitive-search-create-custom-skill-example.md)

Habilidades incorporadas apoiadas por Serviços Cognitivos requerem uma chave de assinatura all-in-one do [Serviço Cognitivo anexada](cognitive-search-attach-cognitive-services.md) que lhe dá acesso ao recurso. Uma chave all-in-one oferece análise de imagens, detecção de idiomas, tradução de texto e análise de texto. Outras habilidades incorporadas são características do Azure Cognitive Search e não requerem nenhum serviço ou chave adicional. Shaper de texto, divisor e fusão são exemplos de habilidades auxiliares que às vezes são necessárias ao projetar o pipeline.

Se você usar apenas habilidades personalizadas e habilidades de utilidade incorporadas, não há dependência ou custos relacionados aos Serviços Cognitivos.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Consumir saída enriquecida com IA em soluções a jusante

A saída do enriquecimento de IA é um índice de pesquisa no Azure Cognitive Search, ou uma [loja de conhecimento](knowledge-store-concept-intro.md) no Azure Storage.

Na Pesquisa Cognitiva do Azure, um índice de pesquisa é usado para exploração interativa usando texto gratuito e consultas filtradas em um aplicativo cliente. Documentos enriquecidos criados por IA são formatados em JSON e indexados da mesma forma que todos os documentos são indexados na Pesquisa Cognitiva do Azure, aproveitando todos os benefícios que um indexador proporciona. Por exemplo, durante a indexação, o indexador blob refere-se a parâmetros de configuração e configurações para utilizar quaisquer mapeamentos de campo ou alterar a lógica de detecção. Essas configurações estão totalmente disponíveis para indexação regular e cargas de trabalho enriquecidas por IA. Pós-indexação, quando o conteúdo é armazenado no Azure Cognitive Search, você pode criar consultas ricas e filtrar expressões para entender seu conteúdo.

No Azure Storage, uma loja de conhecimento tem duas manifestações: um recipiente blob, ou mesas no armazenamento de mesa. 

+ Um recipiente blob captura documentos enriquecidos em sua totalidade, o que é útil se você quiser se alimentar em outros processos. 

+ Em contraste, o armazenamento de tabelas pode acomodar projeções físicas de documentos enriquecidos. Você pode criar fatias ou camadas de documentos enriquecidos que incluem ou excluem peças específicas. Para análise no Power BI, as tabelas de armazenamento da Tabela Azure tornam-se a fonte de dados para visualização e exploração posteriores.

Um documento enriquecido no final do gasoduto difere de sua versão original de entrada pela presença de campos adicionais contendo novas informações que foram extraídas ou geradas durante o enriquecimento. Como tal, você pode trabalhar com uma combinação de conteúdo original e criado, independentemente da estrutura de saída que você usa.

## <a name="next-steps"></a>Próximas etapas

Há muito mais que você pode fazer com o enriquecimento de IA para tirar o máximo de seus dados no Azure Storage, incluindo a combinação de Serviços Cognitivos de diferentes maneiras e a autoria de habilidades personalizadas para casos em que não há serviço cognitivo existente para o cenário. Você pode aprender mais seguindo os links abaixo.

+ [Carregar, baixar e listar bolhas com o portal Azure (armazenamento Azure Blob)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Configure um indexador blob (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [Visão geral do enriquecimento da IA (Pesquisa Cognitiva do Azure)](cognitive-search-concept-intro.md) 
+ [Crie um skillset (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [Mapear os nódulos em uma árvore de anotação (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)
