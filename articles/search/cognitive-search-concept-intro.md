---
title: Introdução ao enriquecimento de IA
titleSuffix: Azure Cognitive Search
description: Extração de conteúdo, NLP (processamento em idioma natural) e processamento de imagens para criar um conteúdo pesquisável na indexação da Pesquisa Cognitiva do Azure usando habilidades cognitivas e algoritmos de IA.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/04/2019
ms.openlocfilehash: e6ee75f4a7e00e8c21079e1336756db20221750f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837996"
---
# <a name="introduction-to-ai-in-azure-cognitive-search"></a>Introdução à IA na Pesquisa Cognitiva do Azure

O enriquecimento de IA é uma funcionalidade da indexação da Pesquisa Cognitiva do Azure, usada para extrair texto de imagens, blobs e outras fontes de dados não estruturadas – enriquecendo o conteúdo, a fim de torná-lo mais pesquisável em um índice ou um repositório de conhecimento. A extração e o enriquecimento são implementados por meio de *habilidades cognitivas* anexadas a um pipeline de indexação. As habilidades cognitivas incorporadas ao serviço se enquadram nessas categorias: 

+ As habilidades de **Processamento de linguagem natural** incluem [reconhecimento de entidade](cognitive-search-skill-entity-recognition.md), [detecção de idioma](cognitive-search-skill-language-detection.md), [extração de frases-chave](cognitive-search-skill-keyphrases.md), manipulação de texto, [detecção de sentimento](cognitive-search-skill-sentiment.md) e [detecção de PII](cognitive-search-skill-pii-detection.md). Com essas habilidades, o texto não estruturado pode assumir novas formas e ser mapeado como campos pesquisáveis e filtráveis em um índice.

+ As habilidades de **Processamento de imagens** incluem [OCR (Reconhecimento Ótico de Caracteres)](cognitive-search-skill-ocr.md) e identificação de [recursos visuais](cognitive-search-skill-image-analysis.md), como detecção facial, interpretação de imagem, reconhecimento da imagem (pessoas famosas e pontos de referência) ou atributos como cores ou orientação da imagem. Você pode criar representações de texto de conteúdo de imagem, pesquisáveis com o uso de todas as funcionalidades de consulta da Pesquisa Cognitiva do Azure.

![Diagrama do pipeline de enriquecimento](./media/cognitive-search-intro/cogsearch-architecture.png "visão geral do pipeline de enriquecimento")

As habilidades cognitivas na Pesquisa Cognitiva do Azure são baseadas nos modelos de machine learning pré-treinados na API de Serviços Cognitivos: [Pesquisa Visual Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) e [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

O processamento de imagens e em idioma natural é aplicada durante a fase de ingestão de dados, com os resultados se tornando parte da composição de um documento em um índice pesquisável na Pesquisa Cognitiva do Azure. Os dados são originados como um conjunto de dados do Azure e, em seguida, enviados por meio de um pipeline de indexação usando o que ocorrer [habilidades internas](cognitive-search-predefined-skills.md) que você precisa. A arquitetura é extensível, portanto, se as habilidades internas não forem suficientes, você pode criar e anexar [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) a integrar o processamento personalizado. Exemplos podem ser um módulo de entidade personalizada ou um classificador de documento direcionado a um domínio específico, como finanças, publicações científicas ou medicina.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem, como parte do estágio de quebra de documento na Pesquisa Cognitiva do Azure. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço da extração de imagem é descrito na [página de preços da Pesquisa Cognitiva do Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="when-to-use-cognitive-skills"></a>Quando usar as habilidades cognitivas

Você deve considerar o uso de habilidades cognitivas incorporadas se o conteúdo bruto for texto não estruturado, conteúdo de imagem ou conteúdo que precise de detecção e tradução de idioma. A aplicação de IA por meio de habilidades cognitivas incorporadas pode desbloquear esse conteúdo, aumentando seu valor e utilitário em seus aplicativos de pesquisa e ciência de dados. 

Além disso, você pode considerar adicionar uma habilidade personalizada se tiver código de software livre, de terceiros ou de clientes diretos que deseja integrar ao pipeline. Os modelos de classificação que identificam as características de destaque de vários tipos de documento se enquadram nessa categoria, mas qualquer pacote que adiciona valor ao seu conteúdo também pode ser usado.

### <a name="more-about-built-in-skills"></a>Mais informações sobre habilidades incorporadas

Um conjunto de habilidades que é montado usando habilidades internas é adequado para os seguintes cenários de aplicativo:

+ Documentos digitalizados (JPEG) que você deseja tornar susceptíveis a pesquisas de texto completo. Você pode anexar uma habilidade de OCR (reconhecimento de caractere óptico) para identificar, extrair e ingerir textos de arquivos JPEG.

+ PDFs com combinações de textos e imagens. Os textos em PDFs podem ser extraídos durante a indexação sem o uso de etapas de enriquecimento, mas a adição do processamento de imagens e em idioma natural pode, muitas vezes, produzir um resultado melhor do que a indexação padrão oferece.

+ Conteúdo multilíngue ao qual você deseja aplicar a detecção de idioma e, possivelmente, a tradução de texto.

+ Documentos não estruturados ou semiestruturados que contêm dados com significado ou contexto inerente, oculto dentro do documento mais amplo. 

  Os BLOBs em particular geralmente contêm um grande corpo de conteúdo empacotado em um "campo" único. Ao anexar habilidades de processamento de imagem e de linguagem natural a um indexador, você pode criar novas informações que estavam contidas no conteúdo bruto, mas não eram destacadas como campos distintos. Algumas habilidades cognitivas internas prontas para uso que podem ajudar: extração de frases-chave, análise de sentimentos e reconhecimento de entidades (pessoas, organizações e locais).

  Além disso, as habilidades internas podem ser usadas para reestruturar o conteúdo por meio de operações de divisão de texto, mesclagem e modelagem.

### <a name="more-about-custom-skills"></a>Saiba mais sobre habilidades incorporadas

As habilidades personalizadas podem dar suporte a cenários mais complexos, como o reconhecimento de formulários ou a detecção de entidades personalizadas usando um modelo que você fornece e encapsula na [interface da Web de habilidades personalizadas](cognitive-search-custom-skill-interface.md). Vários exemplos de habilidades personalizadas incluem o [Reconhecimento de Formulários](/azure/cognitive-services/form-recognizer/overview), a integração da [API de Pesquisa de Entidade do Bing](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example) e o [reconhecimento de entidades personalizadas](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="components-of-an-enrichment-pipeline"></a>Componentes de um pipeline de enriquecimento

Um pipeline de enriquecimento baseia-se em [*indexadores*](search-indexer-overview.md) que rastreiam fontes de dados e fornecem processamento de índice de ponta a ponta. Agora, as habilidades são anexadas aos indexadores, interceptando e enriquecendo documentos de acordo com o conjunto de habilidades que você define. Após a indexação, você pode acessar o conteúdo por meio de solicitações de pesquisa em todos os [tipos de consulta compatíveis com a Pesquisa Cognitiva do Azure](search-query-overview.md).  Se você estiver começando a usar indexadores, esta seção o orientará pelas etapas.

### <a name="step-1-connection-and-document-cracking-phase"></a>Etapa 1: Fase de conexão e desbloqueio de documento

No início do pipeline, há texto não estruturado ou conteúdo que não é de texto (como imagens e arquivos JPEG de documentos digitalizados). Os dados precisam existir em um serviço de armazenamento de dados do Azure que possa ser acessado por um indexador. Os indexadores podem "desbloquear" documentos de origem para extrair o texto da fonte de dados.

![Fase de quebra de documento](./media/cognitive-search-intro/document-cracking-phase-blowup.png "quebra de documento")

 As fontes com suporte incluem o Armazenamento de Blobs do Azure, o Armazenamento de Tabelas do Azure, o Banco de Dados SQL do Azure e o Azure Cosmos DB. O conteúdo baseado em texto pode ser extraído dos seguintes tipos de arquivo: arquivos PDFs, Word, PowerPoint e CSV. Para obter a lista completa, confira [Formatos com suporte](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Etapa 2: Fase de enriquecimento e habilidades cognitivas

O enriquecimento ocorre por meio de *habilidades cognitivas* que executam operações atômicas. Por exemplo, quando há conteúdo de texto de um PDF, você pode aplicar a Detecção de Idioma de reconhecimento de entidade ou a extração de frase-chave para produzir novos campos no índice que não estão disponíveis nativamente na fonte. A coleção de habilidades usada em seu pipeline é chamada de *conjunto de habilidades*.  

![Fase de enriquecimento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase de enriquecimento")

Um conjunto de habilidades baseia-se em [habilidades cognitivas internas](cognitive-search-predefined-skills.md) ou em [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) que você fornece e conecta ao conjunto de habilidades. Um conjunto de habilidades pode ser mínima ou altamente complexo e determina não apenas o tipo de processamento, mas também a ordem das operações. Um conjunto de habilidades mais os mapeamentos de campo definidos como parte de um indexador definem a especificação completa do pipeline de enriquecimento. Para obter mais informações sobre como reunir todas essas partes, confira [Define a skillset](cognitive-search-defining-skillset.md) (Definir um conjunto de habilidades).

Internamente, o pipeline gera uma coleção de documentos enriquecidos. Você pode decidir quais partes dos documentos enriquecidos devem ser mapeadas para campos indexáveis no índice de pesquisa. Por exemplo, se você aplicou a extração de frases-chave e as habilidades de reconhecimento de entidade, os novos campos se tornarão parte do documento enriquecido e poderão ser mapeados para os campos no índice. Consulte [Anotações](cognitive-search-concept-annotations-syntax.md) para saber mais sobre formações de entrada/saída.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Adicionar um elemento knowledgeStore para salvar os enriquecimentos

A [REST de Pesquisa api-version=2019-05-06-Preview](search-api-preview.md) estende os conjuntos de habilidades com uma definição de `knowledgeStore` que fornece uma conexão de armazenamento do Azure e projeções que descrevem como os enriquecimentos são armazenados. 

Adicionar um repositório de dados de conhecimento a uma habilidade fornece a capacidade de projetar uma representação de seus enriquecimentos para cenários diferentes da pesquisa de texto completo. Para obter mais informações, confira [Repositório de conhecimento (versão prévia)](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Etapa 3: Índice de pesquisa e acesso baseado em consulta

Quando o processamento é concluído, você tem um índice de pesquisa que consiste em documentos enriquecidos, com texto totalmente pesquisável na Pesquisa Cognitiva do Azure. [Consultando o índice](search-query-overview.md) é como os desenvolvedores e usuários acessam conteúdo enriquecido gerado pelo pipeline. 

![Índice com o ícone de pesquisa](./media/cognitive-search-intro/search-phase-blowup.png "Índice com o ícone de pesquisa")

O índice é como qualquer outro item que você possa criar para a Pesquisa Cognitiva do Azure: é possível complementá-lo com analisadores personalizados, invocar consultas de pesquisa difusa, adicionar uma pesquisa filtrada ou fazer experimentos com perfis de pontuação para remodelar os resultados da pesquisa.

Os índice são gerados por meio de um esquema de índice que define os campos, os atributos e outras construções anexadas a um índice específico, como perfis de pontuação e mapas de sinônimo. Depois que um índice é definido e populado, é possível indexá-lo de forma incremental para capturar documentos de origem novos e atualizados. Algumas modificações exigem uma recompilação completa. Você deve usar um conjunto de dados pequeno até que o design de esquema fique estável. Para obter mais informações, confira [Como recompilar um índice](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Principais recursos e conceitos

| Conceito | Descrição| Links |
|---------|------------|-------|
| Conjunto de habilidades | Um recurso nomeado de nível superior que contém uma coleção de habilidades. Um conjunto de habilidades é o pipeline de enriquecimento. Ele é invocado durante a indexação por um indexador. | Confira [Definir um conjunto de habilidades](cognitive-search-defining-skillset.md) |
| Habilidades cognitivas | Uma transformação atômica em um pipeline de enriquecimento. Geralmente, é um componente que extrai ou infere a estrutura e, portanto, aumenta o seu entendimento sobre os dados de entrada. Quase sempre, a saída é baseada em texto e o processamento é de idioma natural ou de imagem que extrai ou gera o texto usando entradas de imagem. A saída de uma habilidade pode ser mapeada para um campo em um índice ou usada como entrada para um enriquecimento de downstream. Uma habilidade é predefinida e fornecida pela Microsoft ou personalizada, ou seja, criada e implantada por você. | [Habilidades cognitivas internas](cognitive-search-predefined-skills.md) |
| Extração de dados | Abrange uma ampla gama de processamento, mas em relação ao enriquecimento de IA, a habilidade de reconhecimento de entidade é mais comumente usada para extrair dados (uma entidade) de uma fonte que não fornece informações nativamente. | Confira [Habilidades de reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) e [Habilidades de extração de documentos (versão prévia)](cognitive-search-skill-document-extraction.md)| 
| Processamento de imagem | Infere o texto de uma imagem, como a capacidade de reconhecer um marco, ou extrai o texto de uma imagem. Exemplos comuns incluem o OCR para levantar caracteres de um arquivo de documento (JPEG) ou reconhecer o nome da rua em uma fotografia que contém uma placa de rua. | Confira [Habilidade de Análise de Imagens](cognitive-search-skill-image-analysis.md) ou [Habilidade de OCR](cognitive-search-skill-ocr.md)
| Processamento de idioma natural | Processamento de texto para gerar insight e informações sobre entradas de texto. A Detecção de Idioma, a análise de sentimento e a extração de frases-chave são as habilidades que se enquadram no processamento de idioma natural.  | Confira [Habilidade de Extração de Frases-chave](cognitive-search-skill-keyphrases.md), [Habilidade de Detecção de Idioma](cognitive-search-skill-language-detection.md), [Habilidade de Tradução de Texto](cognitive-search-skill-text-translation.md), [Habilidade de Análise de Sentimento](cognitive-search-skill-sentiment.md), [Habilidade de Detecção de PII (versão prévia)](cognitive-search-skill-pii-detection.md) |
| Desbloqueio de documento | O processo de extração ou criação de conteúdo de texto de fontes que não são de texto durante a indexação. O OCR (reconhecimento óptico de caracteres) é um exemplo, mas geralmente se refere à funcionalidade principal do indexador, pois o indexador extrai o conteúdo de arquivos de aplicativo. A fonte de dados que fornece o local do arquivo de origem e a definição do indexador que fornece os mapeamentos de campo são os dois fatores-chave no desbloqueio de documento. | Confira [Visão geral dos indexadores](search-indexer-overview.md) |
| Formatação | Consolidar os fragmentos de texto em uma estrutura maior ou, ao contrário, dividir partes de texto maiores em um tamanho gerenciável para processamento de downstream posterior. | Confira [Habilidade de Modelagem](cognitive-search-skill-shaper.md), [Habilidade de Mesclagem de Texto](cognitive-search-skill-textmerger.md) e [Habilidade de Divisão de Texto](cognitive-search-skill-textsplit.md) |
| Documentos enriquecidos | Uma estrutura interna transitória, gerada durante o processamento, com a saída final refletida em um índice de pesquisa. Um conjunto de qualificações que determina quais aprimoramentos são executados. Os mapeamentos de campo determinam quais elementos de dados são adicionados ao índice. Opcionalmente, você pode criar um repositório de conhecimento para persistir e explorar documentos enriquecidos usando ferramentas como o Gerenciador de Armazenamento, o Power BI ou qualquer outra ferramenta que se conecte ao armazenamento de Blobs do Azure. | Confira [Repositório de conhecimento (versão prévia)](knowledge-store-concept-intro.md) |
| Indexador |  Um rastreador que extrai dados e metadados pesquisáveis de uma fonte de dados externa e popula um índice com base nos mapeamentos de campo a campo entre o índice e a fonte de dados para desbloqueio de documento. Para enriquecimentos de IA, o indexador invoca um conjunto de habilidades e contém os mapeamentos de campo que associam a saída do enriquecimento aos campos de destino no índice. A definição do indexador contém todas as instruções e as referências para as operações do pipeline e o pipeline é invocado quando você executa o indexador. Com a configuração adicional, você poderá reutilizar o conteúdo processado existente e executar somente as etapas e as habilidades que foram alteradas. | Confira [Indexadores](search-indexer-overview.md) e [Enriquecimento incremental (versão prévia)](cognitive-search-incremental-indexing-conceptual.md). |
| fonte de dados  | Um objeto usado por um indexador para se conectar a uma fonte de dados externa dos tipos com suporte no Azure. | Confira [Visão geral dos indexadores](search-indexer-overview.md) |
| Índice | Um índice de pesquisa persistente na Pesquisa Cognitiva do Azure, criado com base em um esquema de índice que define a estrutura e o uso do campo. | Confira [Criar um índice básico](search-what-is-an-index.md) | 
| Repositório de conhecimento | Uma conta de armazenamento em que os documentos aprimorados podem ser moldados e projetados juntamente com o índice de pesquisa | Confira [Introdução ao repositório de conhecimento](knowledge-store-concept-intro.md) | 
| Cache | Uma conta de armazenamento que contém a saída armazenada em cache criada por um pipeline de enriquecimento. A habilitação do cache preserva a saída existente que não é afetada por alterações em um conjunto de habilidades ou em outros componentes do pipeline de enriquecimento. | Confira [Enriquecimento incremental](cognitive-search-incremental-indexing-conceptual.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Por onde começo?

**Etapa 1: [Criar um recurso da Pesquisa Cognitiva do Azure](search-create-service-portal.md)** 

**Etapa 2: Tente alguns guias de início rápido e exemplos para experiência prática**

+ [Início Rápido (portal)](cognitive-search-quickstart-blob.md)
+ [Tutorial (solicitações HTTP)](cognitive-search-tutorial-blob.md)
+ [Exemplo: Como criar uma habilidade personalizada para o enriquecimento de IA (C#)](cognitive-search-create-custom-skill-example.md)

Recomendamos o serviço Gratuito para fins de aprendizado. Contudo, o número de transações gratuitas é limitado a 20 documentos por dia. Para executar o início rápido e o tutorial em um só dia, use um conjunto de arquivos menor (10 documentos) para encaixar ambos os exercícios ou exclua o indexador usado no início rápido para redefinir o contador para zero.

**Etapa 3: Analisar a API**

Você pode usar a `api-version=2019-05-06` REST ou o SDK .NET. Se estiver explorando o repositório de conhecimento, use a API REST de versão prévia (`api-version=2019-05-06-Preview`).

Esta etapa usa as APIs REST para criar uma solução de enriquecimento de IA. Apenas duas APIs são adicionadas ou estendidas para o enriquecimento de IA. As outras APIs têm a mesma sintaxe que as versões disponíveis ao público.

| API REST | Descrição |
|-----|-------------|
| [Criar Fonte de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Um recurso que identifica uma fonte de dados externa, fornecendo os dados de origem usados para criar documentos enriquecidos.  |
| [Criar Conjunto de Qualificações (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Essa API é específica do enriquecimento de IA. Trata-se de um recurso que coordena o uso de [habilidades internas](cognitive-search-predefined-skills.md) e [habilidades cognitivas personalizadas](cognitive-search-custom-skill-interface.md) em um pipeline de enriquecimento durante a indexação. |
| [Criar o índice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Um esquema que expressa um índice da Pesquisa Cognitiva do Azure. Os campos no índice são mapeados para os campos na fonte de dados ou para os campos fabricados durante a fase de enriquecimento (por exemplo, um campo para os nomes de organização criados pelo reconhecimento de entidade). |
| [Criar Indexador (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que define os componentes usados durante a indexação: incluindo uma fonte de dados, um conjunto de habilidades, associações de campo de origem e estruturas de dados intermediárias para o índice de destino e o próprio índice. Executar o indexador é o gatilho para a ingestão de dados e o enriquecimento. A saída é um índice de pesquisa com base no esquema de índice, preenchido com os dados de origem, enriquecidos por conjuntos de qualificações. Essa API existente é estendida para cenários de pesquisa cognitiva com a inclusão de uma propriedade de conjunto de habilidades. |

**Lista de verificação: Um fluxo de trabalho típico**

1. Subconjunto de dados de origem do Azure em uma amostra representativa. A indexação é um processo demorado, portanto, comece com um conjunto de dados representativo pequeno e, em seguida, compile incrementalmente à medida que sua solução amadurecer.

1. Crie um [objeto de fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) na Pesquisa Cognitiva do Azure para fornecer uma cadeia de conexão para a recuperação de dados.

1. Crie um [conjunto de habilidades](https://docs.microsoft.com/rest/api/searchservice/create-skillset) com etapas de enriquecimento.

1. Defina o [esquema de índice](https://docs.microsoft.com/rest/api/searchservice/create-index). A coleção *Campos* inclui campos da fonte de dados. Você também deve usar stub de campos adicionais para manter os valores gerados para o conteúdo criado durante o enriquecimento.

1. Defina o [indexador](https://docs.microsoft.com/rest/api/searchservice/create-skillset) referenciando a fonte de dados, o conjunto de habilidades e o índice.

1. Dentro do indexador, adicione *outputFieldMappings*. Esta seção mapeia a saída do conjunto de habilidades (na etapa 3) para os campos de entrada no esquema de índice (na etapa 4).

1. Envie a solicitação *Criar Indexador* recém-criada (uma solicitação POST com uma definição de indexador no corpo da solicitação) para expressar o indexador na Pesquisa Cognitiva do Azure. Esta etapa é como você executa o indexador, invocando o pipeline.

1. Execute consultas para avaliar os resultados e modificar o código para atualizar os conjuntos de habilidades, o esquema ou a configuração do indexador.

1. [Reinicie o indexador](search-howto-reindex.md) antes de recompilar o pipeline.

Para obter mais informações sobre problemas ou dúvidas específicas, confira [Troubleshooting tips](cognitive-search-concept-troubleshooting.md) (Dicas de solução de problemas).

## <a name="next-steps"></a>Próximas etapas

+ [Links de documentação do enriquecimento de IA](cognitive-search-resources-documentation.md)
+ [Início Rápido: Experimentar o enriquecimento de IA em um passo a passo no portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Aprender a usar as APIs de enriquecimento de IA](cognitive-search-tutorial-blob.md)
+ [Repositório de conhecimento (versão prévia)](knowledge-store-concept-intro.md)
+ [Criar um repositório de conhecimento no REST](knowledge-store-create-rest.md)
