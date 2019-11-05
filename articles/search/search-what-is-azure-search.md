---
title: Introdução ao Azure Pesquisa Cognitiva
titleSuffix: Azure Cognitive Search
description: O Azure Pesquisa Cognitiva é um serviço de pesquisa de nuvem hospedado totalmente gerenciado da Microsoft. Leia descrições de recursos, o fluxo de trabalho de desenvolvimento, comparações a outros produtos de pesquisa da Microsoft e como começar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1df8bb293834fca123b2573f02871410754a6bdc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73479651"
---
# <a name="what-is-azure-cognitive-search"></a>O que é o Azure Pesquisa Cognitiva?

O Azure Pesquisa Cognitiva ([anteriormente conhecido como "Azure Search"](whats-new.md#new-service-name)) é uma solução de nuvem de pesquisa como serviço que oferece aos desenvolvedores APIs e ferramentas para adicionar uma experiência de pesquisa avançada sobre conteúdo privado e heterogêneo em aplicativos Web, móveis e empresariais . Seu código ou uma ferramenta invoca a ingestão de dados (indexação) para criar e carregar um índice. Opcionalmente, você pode adicionar habilidades cognitivas para aplicar processos de ia durante a indexação. Isso pode adicionar novas informações e estruturas úteis para pesquisa e outros cenários.

No outro lado do serviço, o código do aplicativo emite solicitações de consulta e manipula as respostas. A experiência de pesquisa é definida em seu cliente usando a funcionalidade do Azure Pesquisa Cognitiva, com a execução da consulta em um índice persistente que você cria, possui e armazena em seu serviço.

![Arquitetura de Pesquisa Cognitiva do Azure](media/search-what-is-azure-search/azure-search-diagram.svg "Arquitetura de Pesquisa Cognitiva do Azure")

A funcionalidade é exposta por meio de uma [API REST](/rest/api/searchservice/) ou um [SDK do .NET](search-howto-dotnet-sdk.md) simples que mascara a complexidade inerente da tecnologia de pesquisa. Além das APIs, o portal do Azure fornece suporte de administração e gerenciamento de conteúdo, com as ferramentas de criação para protótipos e consultas de seus índices. Porque o serviço é executado na nuvem, infraestrutura e disponibilidade são gerenciados pela Microsoft.

## <a name="when-to-use-azure-cognitive-search"></a>Quando usar o Azure Pesquisa Cognitiva

O Pesquisa Cognitiva do Azure é adequado para os seguintes cenários de aplicativo:

+ Consolidação de tipos de conteúdo heterogêneo em um índice privado, único e pesquisável. As consultas são sempre sobre um índice que você cria e carrega com documentos e o índice sempre reside na nuvem no serviço de Pesquisa Cognitiva do Azure. Você pode popular um índice com fluxos de documentos JSON de qualquer fonte ou plataforma. Como alternativa, para o conteúdo originado no Azure, você pode usar um *indexador* para efetuar o pull de dados em um índice. A definição de índice e gerenciamento/propriedade é um motivo importante para usar o Azure Pesquisa Cognitiva.

+ O conteúdo bruto é um grande texto não diferenciado, arquivos de imagem ou arquivos de aplicativo, como tipos de conteúdo do Office em uma fonte de dados do Azure, como o armazenamento de BLOBs do Azure ou o Cosmos DB. Você pode aplicar habilidades cognitivas durante a indexação para adicionar estrutura ou extrair significado de arquivos de imagem e de aplicativo.

+ Implementação fácil de recursos relacionados à pesquisa. As APIs de Pesquisa Cognitiva do Azure simplificam a construção da consulta, a navegação facetada, os filtros (incluindo a pesquisa espacial geográfica), o mapeamento de sinônimos, as consultas typeahead e o ajuste de relevância. Usando recursos internos, você pode satisfazer as expectativas do usuário final para uma experiência de pesquisa semelhante aos mecanismos de pesquisa da Web comercial.

+ Indexação de texto não estruturado ou extração de texto e informações de arquivos de imagem. O recurso de [enriquecimento de ia](cognitive-search-concept-intro.md) do Azure pesquisa cognitiva adiciona o processamento de ia a um pipeline de indexação. Alguns casos de uso comuns incluem OCR sobre documentos digitalizados, reconhecimento de entidade e extração de frases-chave em documentos grandes, detecção de idioma e tradução de texto e análise de sentimentos.

+ Requisitos linguísticos satisfeitos usando os analisadores personalizados e de idioma do Azure Pesquisa Cognitiva. Se você tiver conteúdo diferente do inglês, o Azure Pesquisa Cognitiva dará suporte aos analisadores do Lucene e aos processadores de idioma natural da Microsoft. Você também pode configurar analisadores para obter processamento especializado de conteúdo bruto, como filtragem de marcas diacríticas.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Descrições de recursos

| Pesquisa&nbsp;principal&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Recursos |
|-------------------|----------|
|Pesquisa de texto de forma livre | A [**pesquisa de texto completo**](search-lucene-query-architecture.md) é um caso de uso primário para a maioria dos aplicativos baseados em pesquisa. As consultas podem ser formuladas usando uma sintaxe com suporte. <br/><br/>A [**sintaxe de consulta simples**](query-simple-syntax.md) oferece operadores lógicos, operadores de pesquisa de frase, operadores de sufixo e operadores de precedência.<br/><br/>A [**sintaxe de consulta do Lucene**](query-lucene-syntax.md) inclui todas as operações em sintaxe simples, com extensões para pesquisa difusa, pesquisa por proximidade, aumento de termo e expressões regulares.|
| Relevância | A [**Pontuação simples**](index-add-scoring-profiles.md) é um dos principais benefícios do Azure pesquisa cognitiva. Os perfis de pontuação são usados para modelar a relevância como uma função de valores nos próprios documentos. Por exemplo, talvez você queira que produtos mais recentes ou com desconto apareçam primeiro nos resultados da pesquisa. Você também pode criar perfis de pontuação usando marcas de pontuação personalizadas com base nas preferências de pesquisa do cliente que você controlou e armazenou separadamente. |
| Pesquisa geográfica | O Azure Pesquisa Cognitiva processa, filtra e exibe localizações geográficas. Ele permite aos usuários explorar dados com base na proximidade de um resultado da pesquisa a uma localização física. [Assista a este vídeo](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) ou [examine esta amostra](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) para saber mais. |
| Filtros e facetas | A [**navegação facetada**](search-faceted-navigation.md) é habilitada por meio de um único parâmetro de consulta. O Azure Pesquisa Cognitiva retorna uma estrutura de navegação facetada que você pode usar como o código por trás de uma lista de categorias, para filtragem autodirigida (por exemplo, para filtrar itens de catálogo por faixa de preço ou marca). <br/><br/> Os [**filtros**](query-odata-filter-orderby-syntax.md) podem ser usados para incorporar a navegação facetada na interface do usuário do aplicativo, melhorar a formulação da consulta e filtrar com base em critérios especificados pelo usuário ou pelo desenvolvedor. Crie filtros usando a sintaxe OData. |
| Recursos da experiência do usuário | O [**preenchimento automático**](search-autocomplete-tutorial.md) pode ser habilitado para consultas de digitação antecipada em uma barra de pesquisa. <br/><br/>[**Sugestões de pesquisa**](https://docs.microsoft.com/rest/api/searchservice/suggesters) também funciona em entradas de texto parcial em uma barra de pesquisa, mas os resultados são documentos reais em seu índice em vez de termos de consulta. <br/><br/>[**Sinônimos**](search-synonyms.md) associa termos equivalentes que expandem implicitamente o escopo de uma consulta, sem que o usuário tenha de fornecer os termos alternativos. <br/><br/>O [**realce de ocorrência**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) aplica uma formatação de texto a uma palavra-chave correspondente nos resultados da pesquisa. Você pode escolher quais campos retornam os snippets de código destacados.<br/><br/>A [**classificação**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) é oferecida em vários campos por meio do esquema de índice e, então, alternada no momento da consulta com um único parâmetro de pesquisa.<br/><br/> A [**paginação**](search-pagination-page-layout.md) e a limitação dos resultados da pesquisa são simples com o controle ajustado que o Azure pesquisa cognitiva oferece sobre os resultados da pesquisa.  <br/><br/>|

| Enriquecimento&nbsp;por IA&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Recursos |
|-------------------|----------|
|Documentos enriquecidos por IA | O [**enriquecimento de ia**](cognitive-search-concept-intro.md) para análise de imagem e texto pode ser aplicado a um pipeline de indexação para extrair informações de texto de conteúdo bruto. Alguns exemplos de [habilidades integradas](cognitive-search-predefined-skills.md) incluem reconhecimento óptico de caracteres (fazendo JPEGs digitalizados pesquisáveis), reconhecimento de entidade (identificação de uma organização, o nome ou local) e chave de reconhecimento de frase. Você também pode [codificar qualificações personalizadas](cognitive-search-create-custom-skill-example.md) para anexar ao pipeline. |
| Enriquecimentos armazenados para análise e consumo| O [**Knowledge Store (versão prévia)** ](knowledge-store-concept-intro.md) é uma extensão da indexação baseada em IA. Com o armazenamento do Azure como um back-end, você pode salvar enriquecimentos criados durante a indexação. Esses artefatos podem ser usados para ajudá-lo a criar conjuntos de qualificações enriquecidos ou para criar formas e estruturas a partir de dados ambíguos ou amorfos. Você pode criar projeções dessas estruturas que segmentam cargas de trabalho ou usuários específicos. Você também pode analisar diretamente os dados extraídos ou carregá-los em outros aplicativos.<br/><br/> |

| Importação/indexação&nbsp;de dados | Recursos |
|----------------------------------|----------|
| Fontes de dados | Os índices de Pesquisa Cognitiva do Azure aceitam dados de qualquer fonte, desde que sejam enviados como uma estrutura de dados JSON. <br/><br/> Os [**indexadores**](search-indexer-overview.md) automatizam a ingestão de dados das fontes de dados com suporte do Azure e tratam da serialização JSON. Conecte-se ao [Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), ao [Azure Cosmos DB](search-howto-index-cosmosdb.md) ou ao [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) para extrair conteúdo pesquisável em armazenamentos de dados primários. Os indexadores de Blob do Azure podem executar a *decodificação de documentos* para [extrair texto dos principais formatos de arquivo](search-howto-indexing-azure-blob-storage.md), incluindo documentos do Microsoft Office, PDF e HTML. |
| Estruturas de dados hierárquicas e aninhadas | [**Tipos**](search-howto-complex-data-types.md) e coleções complexos permitem modelar praticamente qualquer tipo de estrutura JSON como um índice de pesquisa cognitiva do Azure. A cardinalidade um-para-muitos e muitos-para-muitos pode ser expressa de maneira nativa por meio de coleções, tipos complexos e coleções de tipos complexos.|
| Análise linguística | Os analisadores são componentes usados para processamento de texto durante as operações de indexação e de pesquisa. Há dois tipos. <br/><br/>Os [**analisadores léxicos personalizados**](index-add-custom-analyzers.md) são usados para consultas de pesquisa complexas usando a correspondência fonética e expressões regulares. <br/><br/>Os [**analisadores de idioma**](index-add-language-analyzers.md) do Lucene ou da Microsoft são usados para tratar com inteligência a linguística específica a um idioma, incluindo tempos verbais, gênero, substantivos plurais irregulares (por exemplo, "mouse" vs. "mice"), decomposição de palavras, separação de palavras (para idiomas sem espaços) e muito mais. <br/><br/>|


| Nível da&nbsp;plataforma&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Recursos |
|-------------------|----------|
| Ferramentas para criação de protótipos e inspeção | No portal, use o [**Assistente para importação de dados**](search-import-data-portal.md) para configurar indexadores, o designer de índices para criar um índice e o [**Search Explorer**](search-explorer.md) para testar as consultas e refinar os perfis de pontuação. Também é possível abrir qualquer índice para exibir seu esquema. |
| Monitoramento e diagnóstico | [**Habilite recursos de monitoramento**](search-monitor-usage.md) para ir além de métricas rápidas que estão sempre visíveis no portal. As métricas nas consultas por segundo, latência e limitação são capturadas e informadas nas páginas do portal sem nenhuma configuração adicional necessária.|
| Criptografia no servidor | A [**criptografia em repouso gerenciada pela Microsoft**](search-security-overview.md#encrypted-transmission-and-storage) está incorporada à camada de armazenamento interno e é irrevogável. Opcionalmente, você pode complementar a criptografia padrão com [**chaves de criptografia gerenciadas pelo cliente (versão prévia)** ](search-security-manage-encryption-keys.md). As chaves que você cria e gerencia no Azure Key Vault são usadas para criptografar índices e mapas de sinônimos no Azure Pesquisa Cognitiva. |
| Infraestrutura | A **plataforma altamente disponível** garante uma experiência de serviço de pesquisa extremamente confiável. Quando dimensionado corretamente, [o Azure pesquisa cognitiva oferece um SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> **Totalmente gerenciado e escalonável** como uma solução de ponta a ponta, o Azure pesquisa cognitiva requer absolutamente nenhum gerenciamento de infraestrutura. Seu serviço pode ser adaptado facilmente às suas necessidades ajustando as duas dimensões para lidar com mais armazenamento de documentos, maiores cargas de consulta ou ambos.<br/><br/>|

## <a name="how-to-use-azure-cognitive-search"></a>Como usar o Azure Pesquisa Cognitiva
### <a name="step-1-provision-service"></a>Etapa 1: Provisionar serviço
Você pode provisionar um serviço de Pesquisa Cognitiva do Azure no [portal do Azure](https://portal.azure.com/) ou por meio da [API de gerenciamento de recursos do Azure](/rest/api/searchmanagement/). É possível escolher o serviço gratuito compartilhado com outros assinantes ou uma [camada paga](https://azure.microsoft.com/pricing/details/search/) que dedica os recursos utilizados apenas pelo seu serviço. Para as camadas pagas, você pode dimensionar um serviço em duas dimensões: 

- Adicionar Réplicas para aumentar a capacidade de manipular cargas de consulta pesadas.   
- Adicionar Partições para aumentar o armazenamento para mais documentos. 

Ao manipular o armazenamento de documentos e a taxa de transferência de consultas separadamente, é possível calibrar a alocação de recursos de acordo com os requisitos de produção.

### <a name="step-2-create-index"></a>Etapa 2: Criar índice
Antes de carregar o conteúdo pesquisável, você deve primeiro definir um índice de Pesquisa Cognitiva do Azure. Um índice é como uma tabela de banco de dados que contém os dados e pode aceitar consultas de pesquisa. Você define o esquema de índice a ser mapeado para refletir a estrutura dos documentos que você deseja pesquisar, semelhante a campos em um banco de dados.

Um esquema pode ser criado no portal do Azure ou de forma programática usando o [SDK do .NET](search-howto-dotnet-sdk.md) ou a [API REST](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Etapa 3: carregar dados
Depois de definir um índice, você estará pronto para carregar o conteúdo. É possível usar um modelo push ou pull.

O modelo pull recupera dados de fontes de dados externas. Há suporte para ele por meio de *indexadores* que simplificam e automatizam aspectos da ingestão de dados, como se conectar a dados, lê-los ou serializá-los. Os [indexadores](/rest/api/searchservice/Indexer-operations) estão disponíveis para o Azure Cosmos DB, Banco de Dados SQL do Azure, Armazenamento de Blobs do Azure e SQL Server hospedado em uma VM do Azure. É possível configurar um indexador para uma atualização de dados sob demanda ou agendada.

O modelo de push é fornecido por meio do SDK ou APIs REST usados para enviar documentos atualizados para um índice. Você pode enviar dados de praticamente qualquer conjunto de dados usando o formato JSON. Confira [Adicionar, atualizar ou excluir Documentos](/rest/api/searchservice/addupdate-or-delete-documents) ou [Como usar o SDK do .NET)](search-howto-dotnet-sdk.md) para obter orientação sobre como carregar dados.

### <a name="step-4-search"></a>Etapa 4: Pesquisar
Depois de preencher um índice, você pode [emitir consultas de pesquisa](search-query-overview.md) para o ponto de extremidade de serviço usando solicitações HTTP simples com a [API REST](/rest/api/searchservice/Search-Documents) ou o [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations).

Percorra as etapas de [Criar seu primeiro aplicativo de pesquisa](tutorial-csharp-create-first-app.md) para criar e, em seguida, estender uma página da Web que coleta a entrada do usuário e manipula os resultados. Use também o [Postman para chamadas REST interativas](search-get-started-postman.md) ou o [Gerenciador de Pesquisa](search-explorer.md) interno no portal do Azure para consultar um índice existente.

## <a name="how-it-compares"></a>Como ele se compara

Os clientes geralmente perguntam como o Azure Pesquisa Cognitiva se compara com outras soluções relacionadas à pesquisa. A tabela a seguir resume as principais diferenças.

| Em comparação com | Principais diferenças |
|-------------|-----------------|
|Bing | A [API de Pesquisa na Web do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) pesquisa os índices no Bing.com para os termos correspondentes que você enviar. Os índices são criados de HTML, XML e outros conteúdos da web em sites públicos. Usando o mesmo conceito, a [Pesquisa Personalizada do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) oferece a mesma tecnologia de rastreador para tipos de conteúdo da Web no escopo para sites individuais.<br/><br/>O Azure Pesquisa Cognitiva pesquisa um índice que você define, populado com dados e documentos que você possui, geralmente de diversas fontes. O Azure Pesquisa Cognitiva tem recursos do rastreador para algumas fontes de dados por meio de [indexadores](search-indexer-overview.md), mas você pode enviar por push qualquer documento JSON que esteja de acordo com o esquema de índice em um único recurso pesquisável consolidado. |
|Pesquisa de banco de dados | Muitas plataformas de banco de dados incluem uma experiência de pesquisa interna. O SQL Server possui uma [pesquisa de texto completa](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). O Cosmos DB e tecnologias semelhantes possuem índices passíveis de consulta. Ao avaliar os produtos que combinam pesquisa e armazenamento, pode ser difícil determinar qual a forma de proceder. Muitas soluções usam ambos: DBMS para armazenamento e Pesquisa Cognitiva do Azure para recursos de pesquisa especializados.<br/><br/>Em comparação com a pesquisa do DBMS, o Azure Pesquisa Cognitiva armazena conteúdo de fontes heterogêneas e oferece recursos de processamento de texto especializados, como processamento de texto com reconhecimento lingüístico (lematização, lematização, formas de palavras) em [56 idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support). Ele também suporta correção automática de ortografia, [sinônimos](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions), [controles de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetas](https://docs.microsoft.com/azure/search/search-filters-facets) e [geração de tokens personalizada](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). O [mecanismo de pesquisa de texto completo](search-lucene-query-architecture.md) no Azure pesquisa cognitiva é criado no Apache Lucene, um padrão do setor na recuperação de informações. Embora o Azure Pesquisa Cognitiva persista os dados na forma de um índice invertido, raramente é uma substituição para o verdadeiro armazenamento de dados. Para saber mais, consulte este [post do fórum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Utilização de recursos é outro ponto de inflexão nesta categoria. Algumas operações de consulta e indexação costumam exigir bastante computação. Descarregar pesquisa do DBMS para uma solução dedicada na nuvem preserva recursos do sistema para processamento de transações. Além disso, ao externalizar a pesquisa, é possível dimensionar facilmente para encontrar uma correspondência ao volume da consulta.|
|Solução de pesquisa dedicada | Supondo que você decidiu usar a pesquisa dedicada com todas as funcionalidades, uma comparação categórica final seria entre soluções locais ou um serviço de nuvem. Muitas tecnologias de pesquisa oferecem controle sobre pipelines de indexação e consulta, acesso à consulta mais avançada e sintaxe de filtragem, controle sobre a classificação e a relevância e recursos para pesquisa autodirecionada e inteligente. <br/><br/>Um serviço de nuvem é a escolha certa se você deseja uma solução completa com sobrecarga e manutenção mínimas e escala ajustável. <br/><br/>No paradigma da nuvem, diversos provedores oferecem recursos de linha de base comparáveis, com a pesquisa de texto completo, pesquisa geográfica e a capacidade de lidar com algum nível de ambiguidade nas entradas de pesquisa. Normalmente, é um [recurso especializado](#feature-drilldown) ou a facilidade e simplicidade geral das APIs, ferramentas e gerenciamento que determinam o melhor ajuste. |

Entre os provedores de nuvem, o Azure Pesquisa Cognitiva é mais forte para cargas de trabalho de pesquisa de texto completo em bancos de dados e repositórios de conteúdo no Azure, para aplicativos que dependem principalmente da pesquisa de recuperação de informações e navegação de conteúdo. 

As principais vantagens incluem:

+ Integração dos dados do Azure (rastreadores) na camada de indexação
+ Portal do Azure para o gerenciamento central
+ Escala do Azure, confiabilidade e disponibilidade superior
+ O processamento de AI de dados brutos para torná-los mais pesquisáveis, incluindo texto de imagens ou localização de padrões em conteúdo não estruturado.
+ Análise linguística e personalizada, com analisadores para a pesquisa sólida de texto completo em 56 idiomas
+ [Principais recursos comuns aos aplicativos centrados em pesquisa](#feature-drilldown): pontuação, facetamento, sugestões, sinônimos, pesquisa geográfica e outros.

> [!Note]
> As fontes de dados não Azure têm suporte completo, mas se baseiam em uma metodologia de push com uso mais intensivo de código, em vez dos indexadores. Usando APIs, você pode canalizar qualquer coleção de documentos JSON para um índice de Pesquisa Cognitiva do Azure.

Entre nossos clientes, eles podem aproveitar a mais ampla variedade de recursos do Azure Pesquisa Cognitiva incluir catálogos online, programas de linha de negócios e aplicativos de descoberta de documentos.

## <a name="rest-api--net-sdk"></a>API REST | SDK do .NET

Embora muitas tarefas possam ser executadas no portal, o Azure Pesquisa Cognitiva destina-se a desenvolvedores que desejam integrar a funcionalidade de pesquisa em aplicativos existentes. As seguintes interfaces de programação estão disponíveis.

|Plataforma |DESCRIÇÃO |
|-----|------------|
|[REST](/rest/api/searchservice/) | Comandos HTTP suportados por qualquer plataforma de programação e linguagem, incluindo Xamarin, Java e JavaScript|
|[SDK .NET](search-howto-dotnet-sdk.md) | O wrapper do .NET para a API REST oferece uma codificação eficiente no C# e outras linguagens de código gerenciado destinadas ao .NET Framework |

## <a name="free-trial"></a>Avaliação gratuita
Os assinantes do Azure podem [provisionar um serviço na camada gratuita](search-create-service-portal.md).

Se você não for um assinante, poderá [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Você obtém créditos para experimentar os serviços pagos do Azure. Depois que eles forem utilizados, você poderá manter a conta e usar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança.

Se preferir, você pode [ativar benefícios para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): todos os meses, sua assinatura do MSDN concede créditos que podem ser usados para serviços pagos do Azure. 

## <a name="how-to-get-started"></a>Como começar

1. Crie um [serviço grátis](search-create-service-portal.md). Todos os tutoriais e inícios rápidos podem ser concluídos no serviço gratuito.

2. Percorra os [tutoriais sobre como usar ferramentas internas para indexação e consultas](search-get-started-portal.md). Aprenda conceitos importantes e se familiarize com as informações fornecidas pelo portal.

3. Avance com o código usando a API REST ou .NET:

   + [Como usar o SDK do .NET](search-howto-dotnet-sdk.md) demonstra o fluxo de trabalho principal no código gerenciado.  
   + [Introdução à API REST](https://github.com/Azure-Samples/search-rest-api-getting-started) mostra as mesmas etapas usando a API REST. Você também pode usar este guia de início rápido para chamar as APIs REST do postmaster ou Fiddler: [explorar as APIs REST do Azure pesquisa cognitiva](search-get-started-postman.md).

## <a name="watch-this-video"></a>Assista a este vídeo

Os mecanismos de pesquisa são os drivers comuns de recuperação de informações nos aplicativos móveis, na Web e nos armazenamentos de dados corporativos. O Azure Pesquisa Cognitiva fornece ferramentas para a criação de uma experiência de pesquisa semelhante àquela em grandes sites comerciais da Web.

Neste vídeo de 9 minutos com o gerente de programa Liam Cavanagh, saiba como a integração de um mecanismo de pesquisa pode trazer vantagens para seu aplicativo. As demonstrações curtas abordam os principais recursos do Azure Pesquisa Cognitiva e a aparência de um fluxo de trabalho típico. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ O vídeo de 0-3 minutos aborda os principais recursos e casos de uso.
+ O vídeo de 3-4 minutos aborda o provisionamento de serviços. 
+ O vídeo de 4-6 minutos aborda o Assistente para Importar Dados usado para criar um índice com o conjunto de dados interno de imóveis.
+ O vídeo de 6 a 9 minutos aborda o Gerenciador de pesquisa e várias consultas.
