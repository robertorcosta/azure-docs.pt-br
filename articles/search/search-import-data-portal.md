---
title: Importar dados para um índice de pesquisa usando o portal Azure
titleSuffix: Azure Cognitive Search
description: Saiba como usar o Assistente de Importação de Dados no portal do Azure para rastrear dados do Azure do Cosmos DB, do armazenamento de blobs, do armazenamento de tabela, do Banco de Dados SQL e do SQL Server em VMs do Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0ed2bd7f1e03d8d5fa11f7e76010d087605f0fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460708"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Importar assistente de dados para pesquisa cognitiva do Azure

O portal Azure fornece um assistente **de dados de importação** no painel de pesquisa cognitiva do Azure para prototipagem e carregamento de um índice. Este artigo abrange vantagens e limitações do uso do assistente, entradas e saídas e algumas informações de uso. Para obter orientação prática ao passar pelo assistente usando dados de amostra incorporados, consulte o [índice Criar um índice de pesquisa cognitiva do Azure usando o portal Azure](search-get-started-portal.md) quickstart.

As operações que este assistente executa incluem:

1 - Conecte-se a uma fonte de dados Azure suportada.

2 - Criar um esquema de índice, inferido por dados de origem amostral.

3 - Opcionalmente, adicione enriquecimentos de IA para extrair ou gerar conteúdo e estrutura.

4 - Execute o assistente para criar objetos, importar dados, definir um cronograma e outras opções de configuração.

O assistente produz uma série de objetos que são salvos no seu serviço de pesquisa, que você pode acessar programaticamente ou em outras ferramentas.

## <a name="advantages-and-limitations"></a>Vantagens e limitações

Antes de escrever qualquer código, você pode usar o assistente para prototipagem e testes de prova de conceito. O assistente se conecta a fontes de dados externas, coleta os dados para criar um índice inicial e, em seguida, importa os dados como documentos JSON em um índice no Azure Cognitive Search. 

Amostragem é o processo pelo qual um esquema de índice é inferido e tem algumas limitações. Quando a fonte de dados é criada, o assistente escolhe uma amostra de documentos para decidir quais colunas fazem parte da fonte de dados. Nem todos os arquivos são lidos, pois isso pode levar horas para fontes de dados muito grandes. Dada uma seleção de documentos, os metadados de origem, como nome de campo ou tipo, são usados para criar uma coleção de campos em um esquema de índice. Dependendo da complexidade dos dados de origem, você pode precisar editar o esquema inicial para precisão ou ampliá-los para a completitude. Você pode fazer suas alterações em linha na página de definição de índice.

No geral, as vantagens de usar o assistente são claras: desde que os requisitos sejam atendidos, você pode protótipo de um índice queryable em poucos minutos. Algumas das complexidades da indexação, como o fornecimento de dados como documentos JSON, são tratadas pelo assistente.

As limitações conhecidas são resumidas da seguinte forma:

+ O assistente não suporta iteração ou reutilização. Cada passagem pelo assistente cria uma nova configuração de índice, skillset e indexador. Apenas as fontes de dados podem ser persistidas e reutilizadas dentro do assistente. Para editar ou refinar outros objetos, você tem que usar as APIs REST ou o .NET SDK para recuperar e modificar as estruturas.

+ O conteúdo de origem deve residir em uma fonte de dados Azure suportada.

+ A amostragem é sobre um subconjunto de dados de origem. Para grandes fontes de dados, é possível que o assistente perca campos. Você pode precisar estender o esquema ou corrigir os tipos de dados inferidos, se a amostragem for insuficiente.

+ O enriquecimento da IA, como exposto no portal, está limitado a algumas habilidades incorporadas. 

+ Uma [loja de conhecimento](knowledge-store-concept-intro.md), que pode ser criada pelo assistente, está limitada a algumas projeções padrão. Se você quiser salvar documentos enriquecidos criados pelo assistente, o contêiner e as tabelas blob vêm com nomes e estrutura padrão.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Entrada de fonte de dados

O **assistente de dados Import** conecta-se a uma fonte de dados externa usando a lógica interna fornecida pelos indexadores de pesquisa cognitiva do Azure, que estão equipados para amostrar a origem, ler metadados, quebrar documentos para ler conteúdo e estrutura e serializar conteúdo como JSON para importação subsequente para a Pesquisa Cognitiva do Azure.

Você só pode importar a partir de uma única tabela, exibição de banco de dados ou estrutura de dados equivalente, no entanto, a estrutura pode incluir subestruturas hierárquicas ou aninhadas. Para obter mais informações, consulte [Como modelar tipos complexos](search-howto-complex-data-types.md).

Você deve criar esta única tabela ou exibição antes de executar o assistente, e ele deve conter conteúdo. Por razões óbvias, não faz sentido executar o assistente **de dados Importação** em uma fonte de dados vazia.

|  Seleção | Descrição |
| ---------- | ----------- |
| **Fonte de dados existente** |Se você já tiver indexadores definidos em seu serviço de pesquisa, você pode ter uma definição de fonte de dados existente que você pode reutilizar. Na Pesquisa Cognitiva do Azure, os objetos de origem de dados são usados apenas por indexadores. Você pode criar um objeto de origem de dados programáticamente ou através do assistente **de dados Importar** e reutilizá-los conforme necessário.|
| **Amostras**| O Azure Cognitive Search fornece duas fontes de dados de amostra incorporadas que são usadas em tutoriais e rapidez: um banco de dados SQL imobiliário e um banco de dados de Hotéis hospedado no Cosmos DB. Para uma caminhada com base na amostra de Hotéis, consulte o [Create a index no portal Azure](search-get-started-portal.md) quickstart. |
| [**Banco de Dados SQL do Azure**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |É possível especificar o nome do serviço, credenciais para um usuário de banco de dados com permissão de leitura e um nome de banco de dados na página ou por meio de uma cadeia de conexão do ADO.NET. Escolha a opção de cadeia de conexão para exibir ou personalizar as propriedades. <br/><br/>A tabela ou exibição que fornece o conjunto de linhas deve ser especificada na página. Essa opção aparece após o êxito da conexão, fornecendo uma lista suspensa para que você possa fazer uma seleção.|
| **SQL Server na VM do Azure** |Especifique um nome de serviço totalmente qualificado, a ID e a senha de usuário e um banco de dados como uma cadeia de conexão. Para usar esta fonte de dados, você deve ter instalado um certificado no repositório local que criptografa a conexão. Para obter instruções, consulte [a conexão SQL VM com a Pesquisa Cognitiva do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>A tabela ou exibição que fornece o conjunto de linhas deve ser especificada na página. Essa opção aparece após o êxito da conexão, fornecendo uma lista suspensa para que você possa fazer uma seleção. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|Os requisitos incluem a conta, o banco de dados e a coleção. Todos os documentos na coleção serão incluídos no índice. Você pode definir uma consulta para achatar ou filtrar o conjunto de linhas ou deixar a consulta em branco. Não é necessária uma consulta neste assistente.|
| [**Armazenamento azure Blob**](search-howto-indexing-azure-blob-storage.md) |Os requisitos incluem a conta de armazenamento e um contêiner. Como opção, se os nomes de blob seguirem uma convenção de nomenclatura virtual para fins de agrupamento, você poderá especificar a parte do diretório virtual do nome como uma pasta no contêiner. Confira [Indexação do Armazenamento de Blobs](search-howto-indexing-azure-blob-storage.md) para saber mais. |
| [**Armazenamento de mesa azure**](search-howto-indexing-azure-tables.md) |Os requisitos incluem a conta de armazenamento e um nome de tabela. Como opção, você pode especificar uma consulta para recuperar um subconjunto das tabelas. Confira [Indexação do Armazenamento de Tabelas](search-howto-indexing-azure-tables.md) para saber mais. |

## <a name="wizard-output"></a>Saída do assistente

Nos bastidores, o assistente cria, configura e invoca os seguintes objetos. Depois que o assistente é executado, você pode encontrar sua saída nas páginas do portal. A página Visão Geral do seu serviço tem listas de índices, indexadores, fontes de dados e habilidades. As definições de índice podem ser visualizadas em JSON completo no portal. Para outras definições, você pode usar a [API REST](https://docs.microsoft.com/rest/api/searchservice/) para obter objetos específicos.

| Objeto | Descrição | 
|--------|-------------|
| [Fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Persiste informações de conexão para dados de origem, incluindo credenciais. Um objeto de origem de dados é usado exclusivamente com indexadores. | 
| [Índice](https://docs.microsoft.com/rest/api/searchservice/create-index) | Estrutura de dados físicos utilizada para pesquisa completa de texto e outras consultas. | 
| [Conjunto de habilidades](https://docs.microsoft.com/rest/api/searchservice/create-skillset) | Um conjunto completo de instruções para manipular, transformar e moldar conteúdo, incluindo análise e extração de informações de arquivos de imagem. Com exceção de estruturas muito simples e limitadas, inclui uma referência a um recurso de Serviços Cognitivos que proporciona enriquecimento. Opcionalmente, ele também pode conter uma definição de armazenamento de conhecimento.  | 
| [Indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer)  | Um objeto de configuração especificando uma fonte de dados, índice de destino, um conjunto de habilidades opcional, horário opcional e configurações opcionais de configuração para a entrega de erros e codificação base-64. |


## <a name="how-to-start-the-wizard"></a>Como começar o assistente

O assistente de dados Importação é iniciado a partir da barra de comando na página Visão geral do serviço.

1. No [portal do Azure](https://portal.azure.com), abra a página de serviço de pesquisa no painel ou [localize seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na lista de serviços.

2. Na página de visão geral do serviço, na parte superior, clique em **Importação de dados**.

   ![Importar comando de dados no portal](./media/search-import-data-portal/import-data-cmd2.png "Iniciar o assistente Importar dados")

Você também pode lançar dados de **importação** de outros serviços do Azure, incluindo o Azure Cosmos DB, o Azure SQL Database e o armazenamento Azure Blob. Procure **adicionar pesquisa cognitiva do Azure** no painel de navegação à esquerda na página de visão geral do serviço.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Como editar ou terminar um esquema de índice no assistente

O assistente gera um índice incompleto, que será preenchido com documentos obtidos da fonte de dados de entrada. Para um índice funcional, verifique se você tem os seguintes elementos definidos.

1. A lista de campo está completa? Adicione novos campos que a amostragem não tenha sido perdida e remova qualquer um que não agregue valor a uma experiência de pesquisa ou que não seja usado em uma [expressão de filtro](search-query-odata-filter.md) ou perfil de [pontuação](index-add-scoring-profiles.md).

1. O tipo de dados é apropriado para os dados de entrada? A Pesquisa Cognitiva do Azure suporta os [tipos de dados do modelo de dados da entidade (EDM).](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Para dados SQL do Azure, há [um gráfico](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) de mapeamento que estabelece valores equivalentes. Para obter mais informações, consulte [mapeamentos e transformações de campo](search-indexer-field-mappings.md).

1. Você tem um campo que pode servir como *chave?* Este campo deve ser Edm.string e deve identificar exclusivamente um documento. Para dados relacionais, pode ser mapeado para uma chave primária. Para bolhas, pode ser `metadata-storage-path`o . Se os valores de campo incluírem espaços ou traços, defina a opção **Chave de Codificação de Base 64** na etapa **Criar um indexador**, em **Opções avançadas**, para suprimir a verificação de validação para esses caracteres.

1. Defina atributos para determinar como esse campo é usado em um índice. 

   Tome seu tempo com esta etapa porque os atributos determinam a expressão física dos campos no índice. Se você quiser mudar atributos mais tarde, mesmo programáticamente, você quase sempre precisará cair e reconstruir o índice. Atributos principais como **Searchable** e **Recuperável** têm um [impacto insignificante no armazenamento](search-what-is-an-index.md#index-size). Habilitar filtros e usar sugestões aumenta os requisitos de armazenamento. 
   
   + **Pesquisável** permite a pesquisa completa de texto. Todos os campos usados em consultas de forma livre ou em expressões de consulta devem ter esse atributo. Índices invertidos são criados para cada campo que você marcar como **Pesquisável**.

   + **Recuperável** retorna o campo em resultados de pesquisa. Todos os campos que fornecem conteúdo para os resultados da pesquisa devem ter esse atributo. Definir este campo não afetará significativamente o tamanho do índice.

   + **Filtrante** permite que o campo seja referenciado em expressões de filtro. Todos os campos usados em uma expressão **$filter** devem ter esse atributo. As expressões de filtro servem para correspondências exatas. Como as cadeias de caracteres de texto permanecem intactas, é necessário armazenamento adicional para acomodar o conteúdo textual.

   + **O Facetable** permite que o campo seja navegação facetada. Somente os campos que também estão marcados como **Filtrável** podem ser marcados como **Com faceta**.

   + **Sortable** permite que o campo seja usado de uma espécie. Todos os campos usados em uma expressão **$Orderby** devem ter esse atributo.

1. Precisa de [análise léxica?](search-lucene-query-architecture.md#stage-2-lexical-analysis) Para campos Edm.string que são **pesquisáveis,** você pode definir um **Analisador** se quiser indexação e consulta aprimoradas de idioma. 

   O padrão é *Lucene Standard*, mas você pode escolher *English Microsoft* se quiser usar o analisador da Microsoft para processamento léxico avançado, como resolver formas verbais e substantivos irregulares. Apenas analisadores de idiomas podem ser especificados no portal. O uso de um analisador personalizado ou um analisador não-linguístico como Keyword, Pattern, e assim por diante, deve ser feito de forma programática. Para obter mais informações sobre analisadores, consulte [Adicionar analisadores de idiomas](search-language-support.md).

1. Você precisa de funcionalidade de digito à frente na forma de resultados autocompletos ou sugeridos? Selecione o **Suggester** na caixa de seleção para ativar [sugestões de consulta de tipo à frente e complete automaticamente](index-add-suggesters.md) em campos selecionados. Os sugestionadores adicionam ao número de termos tokenizados em seu índice e, portanto, consomem mais armazenamento.


## <a name="next-steps"></a>Próximas etapas

A melhor maneira de entender os benefícios e limitações do assistente é passar por ele. O quickstart a seguir guia você através de cada etapa.

> [!div class="nextstepaction"]
> [Crie um índice de pesquisa cognitiva do Azure usando o portal Azure](search-get-started-portal.md)