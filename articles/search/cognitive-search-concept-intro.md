---
title: Introdução ao enriquecimento de IA
titleSuffix: Azure Cognitive Search
description: Extração de conteúdo, processamento de linguagem natural (PNL) e processamento de imagens são usados para criar conteúdo pesquisável em índices de pesquisa cognitiva do Azure com habilidades cognitivas pré-definidas e algoritmos de IA personalizados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: a41dcc9c7ec86f41c64a69ea1aba762b960b2633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283014"
---
# <a name="getting-started-with-ai-enrichment"></a>Começar com o enriquecimento da IA

O enriquecimento de IA é uma capacidade de indexação de Pesquisa Cognitiva do Azure usada para extrair texto de imagens, blobs e outras fontes de dados não estruturadas. Enriquecimento e extração tornam seu conteúdo mais pesquisável em um [índice](search-what-is-an-index.md) ou [armazenamento de conhecimento.](knowledge-store-concept-intro.md) Extração e enriquecimento são implementados utilizando *habilidades cognitivas ligadas* ao pipeline de indexação. As habilidades cognitivas incorporadas ao serviço se enquadram nessas categorias: 

+ As habilidades de **Processamento de linguagem natural** incluem [reconhecimento de entidade](cognitive-search-skill-entity-recognition.md), [detecção de idioma](cognitive-search-skill-language-detection.md), [extração de frases-chave](cognitive-search-skill-keyphrases.md), manipulação de texto, [detecção de sentimento](cognitive-search-skill-sentiment.md) e [detecção de PII](cognitive-search-skill-pii-detection.md). Com essas habilidades, o texto não estruturado é mapeado como campos pesquisáveis e filtrados em um índice.

+ **As** habilidades de processamento de imagens incluem Reconhecimento óptico de [caracteres (OCR)](cognitive-search-skill-ocr.md) e identificação de [recursos visuais,](cognitive-search-skill-image-analysis.md)como detecção facial, interpretação de imagem, reconhecimento de imagem (pessoas famosas e marcos) ou atributos como orientação de imagem. Essas habilidades criam representações de texto do conteúdo de imagem, tornando-o pesquisável usando os recursos de consulta do Azure Cognitive Search.

![Diagrama do pipeline de enriquecimento](./media/cognitive-search-intro/cogsearch-architecture.png "visão geral do pipeline de enriquecimento")

As habilidades cognitivas no Azure Cognitive Search são baseadas em modelos de aprendizagem de máquina pré-treinados em APIs de Serviços [Cognitivos:](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) [Visão computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) e Análise de Texto . 

O processamento de imagens e em idioma natural é aplicada durante a fase de ingestão de dados, com os resultados se tornando parte da composição de um documento em um índice pesquisável na Pesquisa Cognitiva do Azure. Os dados são originados como um conjunto de dados do Azure e, em seguida, enviados por meio de um pipeline de indexação usando o que ocorrer [habilidades internas](cognitive-search-predefined-skills.md) que você precisa. A arquitetura é extensível, portanto, se as habilidades internas não forem suficientes, você pode criar e anexar [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) a integrar o processamento personalizado. Exemplos podem ser um módulo de entidade personalizada ou um classificador de documento direcionado a um domínio específico, como finanças, publicações científicas ou medicina.

## <a name="when-to-use-ai-enrichment"></a>Quando usar o enriquecimento de IA

Você deve considerar o uso de habilidades cognitivas incorporadas se o conteúdo bruto for texto não estruturado, conteúdo de imagem ou conteúdo que precise de detecção e tradução de idioma. A aplicação de IA por meio de habilidades cognitivas incorporadas pode desbloquear esse conteúdo, aumentando seu valor e utilitário em seus aplicativos de pesquisa e ciência de dados. 

Além disso, você pode considerar adicionar uma habilidade personalizada se tiver código de software livre, de terceiros ou de clientes diretos que deseja integrar ao pipeline. Modelos de classificação que identificam características salientes de vários tipos de documentos se enquadram nessa categoria, mas qualquer pacote que agregue valor ao seu conteúdo pode ser usado.

### <a name="more-about-built-in-skills"></a>Mais informações sobre habilidades incorporadas

Um [conjunto de habilidades](cognitive-search-defining-skillset.md) montado usando habilidades incorporadas é adequado para os seguintes cenários de aplicação:

+ Documentos digitalizados (JPEG) que você deseja tornar susceptíveis a pesquisas de texto completo. Você pode anexar uma habilidade de OCR (reconhecimento de caractere óptico) para identificar, extrair e ingerir textos de arquivos JPEG.

+ PDFs com combinações de textos e imagens. Os textos em PDFs podem ser extraídos durante a indexação sem o uso de etapas de enriquecimento, mas a adição do processamento de imagens e em idioma natural pode, muitas vezes, produzir um resultado melhor do que a indexação padrão oferece.

+ Conteúdo multilíngue ao qual você deseja aplicar a detecção de idioma e, possivelmente, a tradução de texto.

+ Documentos não estruturados ou semiestruturados que contêm dados com significado ou contexto inerente, oculto dentro do documento mais amplo. 

  Os BLOBs em particular geralmente contêm um grande corpo de conteúdo empacotado em um "campo" único. Ao anexar habilidades de processamento de imagem e de linguagem natural a um indexador, você pode criar novas informações que estavam contidas no conteúdo bruto, mas não eram destacadas como campos distintos. Algumas habilidades cognitivas internas prontas para uso que podem ajudar: extração de frases-chave, análise de sentimentos e reconhecimento de entidades (pessoas, organizações e locais).

  Além disso, as habilidades internas podem ser usadas para reestruturar o conteúdo por meio de operações de divisão de texto, mesclagem e modelagem.

### <a name="more-about-custom-skills"></a>Saiba mais sobre habilidades incorporadas

As habilidades personalizadas podem dar suporte a cenários mais complexos, como o reconhecimento de formulários ou a detecção de entidades personalizadas usando um modelo que você fornece e encapsula na [interface da Web de habilidades personalizadas](cognitive-search-custom-skill-interface.md). Vários exemplos de habilidades personalizadas incluem o [Reconhecimento de Formulários](/azure/cognitive-services/form-recognizer/overview), a integração da [API de Pesquisa de Entidade do Bing](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example) e o [reconhecimento de entidades personalizadas](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="steps-in-an-enrichment-pipeline"></a>Passos em um gasoduto de enriquecimento

Um gasoduto de enriquecimento é baseado em [*indexadores.*](search-indexer-overview.md) Os indexadores preenchem um índice com base em mapeamentos de campo para campo entre o índice e sua fonte de dados para a quebra de documentos. Habilidades, agora ligadas aos indexadores, interceptam e enriquecem documentos de acordo com as habilidades que você define. Após a indexação, você pode acessar o conteúdo por meio de solicitações de pesquisa em todos os [tipos de consulta compatíveis com a Pesquisa Cognitiva do Azure](search-query-overview.md).  Se você estiver começando a usar indexadores, esta seção o orientará pelas etapas.

### <a name="step-1-connection-and-document-cracking-phase"></a>Passo 1: Fase de conexão e quebra de documentos

No início do pipeline, você tem conteúdo não estruturado de texto ou não-texto (como imagens, documentos digitalizados ou arquivos JPEG). Os dados precisam existir em um serviço de armazenamento de dados do Azure que possa ser acessado por um indexador. Os indexadores podem "desbloquear" documentos de origem para extrair o texto da fonte de dados. A quebra de documentos é o processo de extração ou criação de conteúdo de texto de fontes não-texto durante a indexação.

![Fase de quebra de documento](./media/cognitive-search-intro/document-cracking-phase-blowup.png "quebra de documento")

 As fontes com suporte incluem o Armazenamento de Blobs do Azure, o Armazenamento de Tabelas do Azure, o Banco de Dados SQL do Azure e o Azure Cosmos DB. O conteúdo baseado em texto pode ser extraído dos seguintes tipos de arquivo: arquivos PDF, do Word, do PowerPoint e CSV. Para obter a lista completa, confira [Formatos com suporte](search-howto-indexing-azure-blob-storage.md#supported-document-formats). A indexação é um processo demorado, portanto, comece com um conjunto de dados representativo pequeno e, em seguida, compile incrementalmente à medida que sua solução amadurecer.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Passo 2: Habilidades cognitivas e fase de enriquecimento

O enriquecimento é realizado com *habilidades cognitivas* realizando operações atômicas. Por exemplo, uma vez que você tenha rachado um PDF, você pode aplicar reconhecimento de entidade, detecção de idiomas ou extração de frases-chave para produzir novos campos em seu índice que não estão disponíveis nativamente na fonte. A coleção de habilidades usada em seu pipeline é chamada de *conjunto de habilidades*.  

![Fase de enriquecimento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase de enriquecimento")

Um conjunto de habilidades baseia-se em [habilidades cognitivas internas](cognitive-search-predefined-skills.md) ou em [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) que você fornece e conecta ao conjunto de habilidades. Um conjunto de habilidades pode ser mínima ou altamente complexo e determina não apenas o tipo de processamento, mas também a ordem das operações. Um conjunto de habilidades mais os mapeamentos de campo definidos como parte de um indexador definem a especificação completa do pipeline de enriquecimento. Para obter mais informações sobre como reunir todas essas partes, confira [Define a skillset](cognitive-search-defining-skillset.md) (Definir um conjunto de habilidades).

Internamente, o pipeline gera uma coleção de documentos enriquecidos. Você pode decidir quais partes dos documentos enriquecidos devem ser mapeadas para campos indexáveis no índice de pesquisa. Por exemplo, se você aplicou a extração de frases-chave e as habilidades de reconhecimento da entidade, esses novos campos se tornariam parte do documento enriquecido e podem ser mapeados para campos em seu índice. Consulte [Anotações](cognitive-search-concept-annotations-syntax.md) para saber mais sobre formações de entrada/saída.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Adicionar um elemento knowledgeStore para salvar os enriquecimentos

A [REST de Pesquisa api-version=2019-05-06-Preview](search-api-preview.md) estende os conjuntos de habilidades com uma definição de `knowledgeStore` que fornece uma conexão de armazenamento do Azure e projeções que descrevem como os enriquecimentos são armazenados. Isso é além do seu índice. Em um pipeline de IA padrão, documentos aprimorados são transitórios, usados apenas durante a indexação e descartados em seguida. Com o repositório de conhecimento, os documentos enriquecidos são preservados. Para obter mais informações, confira [Repositório de conhecimento (versão prévia)](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Passo 3: Índice de pesquisa e acesso baseado em consulta

Quando o processamento é concluído, você tem um índice de pesquisa que consiste em documentos enriquecidos, com texto totalmente pesquisável na Pesquisa Cognitiva do Azure. [Consultando o índice](search-query-overview.md) é como os desenvolvedores e usuários acessam conteúdo enriquecido gerado pelo pipeline. 

![Índice com o ícone de pesquisa](./media/cognitive-search-intro/search-phase-blowup.png "Índice com o ícone de pesquisa")

O índice é como qualquer outro item que você possa criar para a Pesquisa Cognitiva do Azure: é possível complementá-lo com analisadores personalizados, invocar consultas de pesquisa difusa, adicionar uma pesquisa filtrada ou fazer experimentos com perfis de pontuação para remodelar os resultados da pesquisa.

Os índice são gerados por meio de um esquema de índice que define os campos, os atributos e outras construções anexadas a um índice específico, como perfis de pontuação e mapas de sinônimo. Depois que um índice é definido e populado, é possível indexá-lo de forma incremental para capturar documentos de origem novos e atualizados. Algumas modificações exigem uma recompilação completa. Você deve usar um conjunto de dados pequeno até que o design de esquema fique estável. Para obter mais informações, confira [Como recompilar um índice](search-howto-reindex.md).

**Lista de verificação: um fluxo de trabalho típico**

1. Subconjunto de dados de origem do Azure em uma amostra representativa. A indexação é um processo demorado, portanto, comece com um conjunto de dados representativo pequeno e, em seguida, compile incrementalmente à medida que sua solução amadurecer.

1. Crie um [objeto de fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) na Pesquisa Cognitiva do Azure para fornecer uma cadeia de conexão para a recuperação de dados.

1. Crie um [conjunto de habilidades](https://docs.microsoft.com/rest/api/searchservice/create-skillset) com etapas de enriquecimento.

1. Defina o [esquema de índice](https://docs.microsoft.com/rest/api/searchservice/create-index). A coleção *Campos* inclui campos da fonte de dados. Você também deve usar stub de campos adicionais para manter os valores gerados para o conteúdo criado durante o enriquecimento.

1. Defina o [indexador](https://docs.microsoft.com/rest/api/searchservice/create-skillset) referenciando a fonte de dados, o conjunto de habilidades e o índice.

1. Dentro do indexador, adicione *outputFieldMappings*. Esta seção mapeia a saída do conjunto de habilidades (na etapa 3) para os campos de entrada no esquema de índice (na etapa 4).

1. Envie a solicitação *Criar Indexador* recém-criada (uma solicitação POST com uma definição de indexador no corpo da solicitação) para expressar o indexador na Pesquisa Cognitiva do Azure. Esta etapa é como você executa o indexador, invocando o pipeline.

1. Execute consultas para avaliar os resultados e modificar o código para atualizar os conjuntos de habilidades, o esquema ou a configuração do indexador.

1. [Reinicie o indexador](search-howto-reindex.md) antes de recompilar o pipeline.

## <a name="next-steps"></a>Próximas etapas

+ [Links de documentação do enriquecimento de IA](cognitive-search-resources-documentation.md)
+ [Exemplo: Criando uma habilidade personalizada para o enriquecimento de IA (C#)](cognitive-search-create-custom-skill-example.md)
+ [Quickstart: Tente o enriquecimento da IA em um walk-through de portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Conheça as APIs de enriquecimento de IA](cognitive-search-tutorial-blob.md)
+ [Repositório de conhecimento (versão prévia)](knowledge-store-concept-intro.md)
+ [Criar um repositório de conhecimento no REST](knowledge-store-create-rest.md)
+ [Dicas de solução de problemas](cognitive-search-concept-troubleshooting.md)
