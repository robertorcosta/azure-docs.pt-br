---
title: Criar um indexador
titleSuffix: Azure Cognitive Search
description: Defina as propriedades em um indexador para determinar a origem dos dados e os destinos. Você pode definir parâmetros para modificar os comportamentos de tempo de execução.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 0483030312493dde9a50ab9000fbe29f19bfaff4
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063757"
---
# <a name="create-a-search-indexer"></a>Criar um indexador de pesquisa

Um indexador de pesquisa fornece um fluxo de trabalho automatizado para transferir documentos e conteúdo de uma fonte de dados externa para um índice de pesquisa em seu serviço de pesquisa. Como originalmente criado, ele extrai texto e metadados de fontes de dados do Azure, serializa documentos em JSON e transmite os documentos resultantes para um mecanismo de pesquisa para indexação. Desde então, foi estendido dar suporte ao [enriquecimento de ia](cognitive-search-concept-intro.md) para processamento profundo de conteúdo. 

O uso de indexadores reduz significativamente a quantidade e a complexidade do código que você precisa escrever. Este artigo se concentra na mecânica e na estrutura de indexadores, colocando uma base em vigor antes de explorar os indexadores específicos de origem e [habilidades](cognitive-search-working-with-skillsets.md).

## <a name="whats-an-indexer-definition"></a>O que é uma definição de indexador?

Os indexadores são usados para indexação baseada em texto que obtém texto de campos de origem para campos de índice ou processamento baseado em ia que analisa texto não diferenciado para estrutura ou analisa imagens de texto e informações. As definições de índice a seguir são típicas do que você pode criar para qualquer um dos cenários.

### <a name="indexers-for-text-content"></a>Indexadores para conteúdo de texto

A finalidade original de um indexador era simplificar o processo complexo de carregar um índice fornecendo um mecanismo para conectar e ler texto e conteúdo numérico de campos em uma fonte de dados, serializar esse conteúdo como documentos JSON e entregar esses documentos ao mecanismo de pesquisa para indexação. Esse ainda é um caso de uso primário e, para essa operação, você precisará criar um indexador com as propriedades definidas nesta seção.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```
As **`name`** **`dataSourceName`** Propriedades,, e **`targetIndexName`**  são necessárias e, dependendo de como você cria o indexador, a fonte de dados e o índice já devem existir antes que você possa executar o indexador. 

A **`parameters`** propriedade informa comportamentos de tempo de execução, como quantos erros aceitar antes de falhar todo o trabalho. Os parâmetros também são como você especifica comportamentos específicos de origem. Por exemplo, se a origem for um armazenamento de BLOBs, você poderá definir um parâmetro que filtra em extensões de arquivo: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` .

A **`field mappings`** propriedade é usada para mapear explicitamente os campos de origem para destino se esses campos forem diferentes por nome ou tipo. Outras propriedades (não mostradas), são usadas para especificar uma agenda, criar o indexador em um estado desabilitado ou especificar uma chave de criptografia para a criptografia complementar dos dados em repouso.

### <a name="indexers-for-ai-indexing"></a>Indexadores para indexação de ia

Como os indexadores são o mecanismo pelo qual um serviço de pesquisa faz solicitações de saída, os indexadores foram estendidos para dar suporte a aprimoramentos de ia, adicionando etapas e objetos necessários para esse caso de uso.

Todas as propriedades e parâmetros acima se aplicam a indexadores que executam enriquecimento de ia, com a adição de três propriedades específicas ao enriquecimento de ia: **`skillSets`** , **`outputFieldMappings`** , **`cache`** (somente visualização e REST). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

O enriquecimento do ia está além do escopo deste artigo. Para obter mais informações, comece com o [habilidades no Azure pesquisa cognitiva](cognitive-search-working-with-skillsets.md) ou [crie o Skills (REST)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Escolher um cliente do indexador e criar o indexador

Quando você estiver pronto para criar um indexador em um serviço de pesquisa remota, será necessário um cliente de pesquisa na forma de uma ferramenta, como portal do Azure ou postmaster, ou código que instancia um cliente do indexador. Recomendamos as APIs portal do Azure ou REST para desenvolvimento antecipado e teste de prova de conceito.

### <a name="permissions"></a>Permissões

Todas as operações relacionadas a indexadores, incluindo solicitações GET para status ou definições, exigem uma [chave de API de administração](search-security-api-keys.md) na solicitação.

### <a name="limits"></a>Limites

Todas as [camadas de serviço limitam](search-limits-quotas-capacity.md#indexer-limits) o número de objetos que você pode criar. Se você estiver experimentando a camada gratuita, só poderá ter três objetos de cada tipo e 2 minutos de processamento do indexador (não incluindo o processamento do Configurador de habilidades).

### <a name="use-azure-portal-to-create-an-indexer"></a>Usar portal do Azure para criar um indexador

O portal fornece duas opções para criar um indexador: [**importar dados**](search-import-data-portal.md) e **novo indexador** que fornece campos para especificar uma definição de indexador. O assistente é exclusivo, pois ele cria todos os elementos necessários. Outras abordagens exigem que você predefina uma fonte de dados e um índice.

A captura de tela a seguir mostra onde você pode encontrar esses recursos no Portal. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="indexador de hotéis" border="true":::

### <a name="use-a-rest-client"></a>Usar um cliente REST

O postmaster e o Visual Studio Code (com uma extensão para o Azure Pesquisa Cognitiva) podem funcionar como um cliente do indexador. Usando qualquer ferramenta, você pode se conectar ao serviço de pesquisa e enviar solicitações que criam indexadores e outros objetos. Há vários tutoriais e exemplos que demonstram clientes REST para a criação de objetos. 

Comece com um destes artigos para saber mais sobre cada cliente:

+ [Criar um índice de pesquisa usando REST e o postmaster](search-get-started-rest.md)
+ [Introdução ao Visual Studio Code e ao Azure Cognitive Search](search-get-started-vs-code.md)

Consulte as [operações do indexador (REST)](/rest/api/searchservice/Indexer-operations) para obter ajuda com as solicitações do indexador formular.

### <a name="use-an-sdk"></a>Usar um SDK

Por Pesquisa Cognitiva, os SDKs do Azure implementam recursos geralmente disponíveis. Dessa forma, você pode usar qualquer um dos SDKs para criar objetos relacionados ao indexador. Todos eles implementam um **SearchIndexerClient** que fornece métodos para criar indexadores e objetos relacionados, incluindo habilidades.

| SDK do Azure | Cliente | Exemplos |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample. java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| JavaScript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [Indexadores](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>Executar o indexador

Um indexador é executado automaticamente quando você cria o indexador no serviço. Esse é o momento de verdade em que você descobrirá se há erros de conexão de fonte de dados, problemas de mapeamento de campo ou problemas de conjunto de qualificações. Uma solicitação HTTP interativa para [criar indexador](/rest/api/searchservice/create-indexer) ou [Atualizar indexador](/rest/api/searchservice/update-indexer) executará um indexador. A execução de um programa que chama os métodos SearchIndexerClient também executará um indexador.

Para evitar executar imediatamente um indexador após a criação, inclua **`disabled=true`** na definição do indexador.

Uma vez que um indexador existe, você pode executá-lo sob demanda usando o [REST (executar indexador)](/rest/api/searchservice/run-indexer) ou um método SDK equivalente. Ou então, coloque o indexador [em um agendamento](search-howto-schedule-indexers.md) para invocar o processamento em intervalos regulares. 

O processamento agendado geralmente coincide com a necessidade de indexação incremental do conteúdo alterado. A lógica de detecção de alteração é uma funcionalidade incorporada às plataformas de origem. As alterações em um contêiner de blob são detectadas automaticamente pelo indexador. Para obter orientação sobre como aproveitar a detecção de alterações em outras fontes de dados, consulte os documentos do indexador para fontes de dados específicas:

+ [Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="know-your-data"></a>Conheça seus dados

Os indexadores esperam um conjunto de linhas tabulares, onde cada linha se torna um documento de pesquisa completo ou parcial no índice. Muitas vezes, há uma correspondência completa entre uma linha e o documento de pesquisa resultante, onde todos os campos se alinham. Mas você pode usar indexadores para gerar apenas parte de um documento, por exemplo, se você estiver usando vários indexadores ou abordagens para criar o índice. 

Para mesclar dados relacionais em um conjunto de linhas, talvez seja necessário criar um modo SQL ou criar uma consulta que retorne os registros pai e filho na mesma linha. Por exemplo, o conjunto de dados interno de exemplos de hotéis é um banco de dados SQL que tem registros 50 (um para cada hotel), vinculados a registros de sala em uma tabela relacionada. A consulta que achata os dados coletivos em um conjunto de linhas incorpora todas as informações de sala em documentos JSON em cada registro de Hotel. As informações de sala inseridas são geradas por uma consulta que usa uma cláusula **for JSON auto** . Você pode aprender mais sobre essa técnica em [definir uma consulta que retorna JSON inserido](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Este é apenas um exemplo; Você pode encontrar outras abordagens que produzirão o mesmo efeito.

Além dos dados simplificados, é importante efetuar pull somente de dados pesquisáveis. Os dados pesquisáveis são alfanuméricos. Pesquisa Cognitiva não pode pesquisar em dados binários em nenhum formato, embora possa extrair e inferir descrições de texto de arquivos de imagem (consulte o [enriquecimento de ai](cognitive-search-concept-intro.md)) para criar conteúdo pesquisável. Da mesma forma, usando o enriquecimento de ia, o texto grande pode ser analisado por modelos de linguagem natural para localizar a estrutura ou informações relevantes, gerando novo conteúdo que você pode adicionar a um documento de pesquisa.

Considerando que os indexadores não corrigem problemas de dados, outras formas de limpeza ou manipulação de dados podem ser necessárias. Para obter mais informações, consulte a documentação do produto do seu [produto de banco de dados do Azure](/azure/?product=databases).

## <a name="know-your-index"></a>Saber seu índice

Lembre-se de que os indexadores passam os documentos de pesquisa para o mecanismo de pesquisa para indexação. Assim como os indexadores têm propriedades que determinam o comportamento da execução, um esquema de índice tem propriedades que afetam de forma prodeterminada como as cadeias de caracteres são indexadas (somente cadeias de caracteres são analisadas e indexadas). Dependendo das atribuições do analisador, as cadeias de caracteres indexadas podem ser diferentes do que você passou. Você pode avaliar os efeitos de analisadores usando o [texto de análise (REST)](/rest/api/searchservice/test-analyzer). Para obter mais informações sobre analisadores, consulte [analisadores para processamento de texto](search-analyzers.md).

Os indexadores só verificam os nomes e tipos de campos. Não há nenhuma etapa de validação que garanta que o conteúdo de entrada esteja correto para o campo de pesquisa correspondente no índice. Como uma etapa de verificação, você pode executar consultas no índice preenchido que retornam documentos inteiros ou campos selecionados. Para obter mais informações sobre como consultar o conteúdo de um índice, consulte [criar uma consulta básica](search-query-create.md).

## <a name="next-steps"></a>Próximas etapas

+ [Indexadores do agendamento](search-howto-schedule-indexers.md)
+ [Definir mapeamentos de campo](search-indexer-field-mappings.md)
+ [Monitorar o status do indexador](search-howto-monitor-indexers.md)
+ [Conectar usando identidades gerenciadas](search-howto-managed-identities-data-sources.md)