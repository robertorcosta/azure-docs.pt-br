---
title: Criar um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Apresenta conceitos e ferramentas de indexação no Pesquisa Cognitiva do Azure, incluindo definições de esquema e a estrutura de dados física.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: aa7c06c3bad59bad11fa288631042cca86109706
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701126"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>Criar um índice de pesquisa básica no Azure Pesquisa Cognitiva

No Azure Pesquisa Cognitiva, um *índice de pesquisa* armazena o conteúdo pesquisável usado para texto completo e consultas filtradas. Um índice é definido por um esquema e salvo no serviço, com a importação de dados a seguir como uma segunda etapa. 

Os índices contêm *documentos*. Conceitualmente, um documento é uma única unidade de dados pesquisáveis no índice. Um varejista pode ter um documento para cada produto, uma organização de notícias pode ter um documento para cada artigo e assim por diante. Mapeando esses conceitos para equivalentes de banco de dados mais conhecidos: um *índice de pesquisa* é igual a uma *tabela* e os *documentos* são aproximadamente equivalentes a *linhas* em uma tabela.

A estrutura física de um índice é determinada pelo esquema, com campos marcados como "pesquisáveis", resultando em um índice invertido criado para esse campo. 

Você pode criar um índice com as seguintes ferramentas e APIs:

* No portal do Azure, use o assistente para **Adicionar índice** ou **importar dados**
* Usando o [criar índice (API REST)](/rest/api/searchservice/create-index)
* Usando o [SDK do .net](./search-get-started-dotnet.md)

É mais fácil aprender com uma ferramenta de Portal. O portal impõe requisitos e regras de esquema para tipos de dados específicos, como a despermissão de recursos de pesquisa de texto completo em campos numéricos. Depois de ter um índice viável, você pode fazer a transição para o código recuperando a definição de JSON do serviço usando [Get index (API REST)](/rest/api/searchservice/get-index) e adicionando-a à sua solução.

## <a name="recommended-workflow"></a>Fluxo de trabalho recomendado

Chegar a um design de índice final é um processo iterativo. É comum começar com o portal para criar o índice inicial e, em seguida, alternar para o código para posicionar o índice sob controle do código-fonte.

1. Determine se você pode usar [**importar dados**](search-import-data-portal.md). O assistente executará a indexação com base no indexador All-in-One se os dados de origem forem de um [tipo de fonte de dados com suporte no Azure](search-indexer-overview.md#supported-data-sources).

1. Se você não pode usar **importar dados**, comece com **Adicionar índice** para definir o esquema.

   ![Comando adicionar índice](media/search-what-is-an-index/add-index.png "Comando adicionar índice")

1. Forneça um nome e uma chave usados para identificar exclusivamente cada documento de pesquisa no índice. A chave é obrigatória e deve ser do tipo EDM. String. Durante a importação, você deve planejar o mapeamento de um campo exclusivo em dados de origem para esse campo. 

   O portal fornece um `id` campo para a chave. Para substituir o padrão `id` , crie um novo campo (por exemplo, uma nova definição de campo chamada `HotelId` ) e, em seguida, selecione-o na **chave**.

   ![Preencher as propriedades necessárias](media/search-what-is-an-index//field-attributes.png "Preencher as propriedades necessárias")

1. Adicione mais campos. O portal mostra quais [atributos de campo](#index-attributes) estão disponíveis para diferentes tipos de dados. Se você for novo no design de índice, isso será útil.

   Se os dados de entrada forem hierárquicos por natureza, atribua o tipo de dados de [tipo complexo](search-howto-complex-data-types.md) para representar as estruturas aninhadas. O conjunto de dados de exemplo interno, hotéis, ilustra os tipos complexos usando um endereço (contém vários subcampos) que têm uma relação um-para-um com cada hotel e uma coleção complexa de salas, em que várias salas são associadas a cada hotel. 

1. Atribua qualquer [analisador](#analyzers) a campos de cadeia de caracteres antes de criar o índice. Faça o mesmo para [sugestores](#suggesters) se você quiser habilitar o preenchimento automático em campos específicos.

1. Clique em **criar** para criar as estruturas físicas em seu serviço de pesquisa.

1. Depois que um índice é criado, use comandos adicionais para revisar definições ou adicionar mais elementos.

   ![Adicionar página de índice mostrando atributos por tipo de dados](media/search-what-is-an-index//field-definitions.png "Adicionar página de índice mostrando atributos por tipo de dados")

1. Baixe o esquema de índice usando [obter índice (API REST)](/rest/api/searchservice/get-index) e uma ferramenta de teste na Web como o [postmaster](search-get-started-rest.md). Agora você tem uma representação JSON do índice que você pode adaptar para o código.

1. [Carregue seu índice com os dados](search-what-is-data-import.md). O Azure Pesquisa Cognitiva aceita documentos JSON. Para carregar seus dados de maneira programática, é possível usar o Postman com documentos JSON no conteúdo da solicitação. Se os dados não estiverem expressados facilmente como JSON, essa etapa será a mais trabalhosa. 

    Depois que um índice é carregado com dados, a maioria das edições nos campos existentes exigirá que você remova e reconstrua um índice.

1. Consulte seu índice, examine resultados e itere o esquema de índice até que você comece a ver os resultados esperados. É possível usar o [**Gerenciador de pesquisa**](search-explorer.md) ou o Postman para consultar seu índice.

Durante o desenvolvimento, planeje recompilações frequentes. Como as estruturas físicas são criadas no serviço, é necessário [descartar e recriar índices](search-howto-reindex.md) para a maioria das modificações em uma definição de campo existente. Considere trabalhar com um subconjunto de seus dados para acelerar as recompilações. 

> [!Tip]
> O código, em vez de uma abordagem de portal, é recomendado para trabalhar em design de índice e importação de dados simultaneamente. Como alternativa, ferramentas como o [postmaster e o Visual Studio Code](search-get-started-rest.md) são úteis para testes de prova de conceito quando os projetos de desenvolvimento ainda estão nas primeiras fases. É possível fazer alterações incrementais em uma definição de índice em um corpo da solicitação e, em seguida, enviar a solicitação para seu serviço a fim de recriar um índice usando um esquema atualizado.

## <a name="index-schema"></a>Esquema de índice

É necessário um índice para ter um nome e um campo de chave designado (Of EDM. String) na coleção Fields. A [*coleção de campos*](#fields-collection) normalmente é a maior parte de um índice, onde cada campo é nomeado, digitado e atribuído com comportamentos permitidos que determinam como é usado. 

Outros elementos incluem [sugestores](#suggesters), [perfis de Pontuação](#scoringprofiles), [analisadores](#analyzers) usados para processar cadeias de caracteres em tokens de acordo com as regras linguísticas ou outras características com suporte do analisador e configurações de [script remoto entre origens (CORS)](#corsoptions) .

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Coleção e atributo de campos

Os campos têm um nome, um tipo que classifica os dados armazenados e atributos que especificam como o campo é usado.

### <a name="data-types"></a>Tipos de dados

| Tipo | Descrição |
|------|-------------|
| Edm.String |O texto que opcionalmente pode ser indexado para a pesquisa de texto completo (separação de palavras, derivação e assim por diante). |
| Collection(Edm.String) |Uma lista de cadeias de caracteres que opcionalmente podem ser indexadas para a pesquisa de texto completo. Não há nenhum limite teórico superior no número de itens em uma coleção, mas o limite superior de 16 MB no tamanho da carga se aplica às coleções. |
| Edm.Boolean |Contém valores true/false. |
| Edm.Int32 |Valores inteiros de 32 bits. |
| Edm.Int64 |Valores inteiros de 64 bits. |
| Edm.Double |Dados numéricos de dupla precisão. |
| Edm.DateTimeOffset |Valores de data e hora representados no formato OData V4 (por exemplo, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| Edm.GeographyPoint |Um ponto que representa uma localização geográfica em todo o mundo. |

Para obter mais informações, consulte [tipos de dados com suporte](/rest/api/searchservice/Supported-data-types).

<a name="index-attributes"></a>

### <a name="attributes"></a>Atributos

Os atributos de campo determinam como um campo é usado, por exemplo, se ele é usado na pesquisa de texto completo, na navegação facetada, nas operações de classificação e assim por diante. 

Campos de cadeia de caracteres geralmente são marcados como "pesquisáveis" e "recuperáveis". Os campos usados para restringir os resultados da pesquisa incluem "classificável", "filtrável" e "facetable".

|Atributo|Descrição|  
|---------------|-----------------|  
|pesquisável |Pesquisável com texto completo, sujeito à análise lexical como separação de palavras durante a indexação. Se você definir um campo pesquisável com um valor como “dia ensolarado”, internamente, ele será dividido nos tokens individuais “dia” e “ensolarado”. Para obter detalhes, consulte [Como funciona a pesquisa de texto completo](search-lucene-query-architecture.md).|  
|filtrável |Referenciado nas consultas $filter. Campos filtráveis dos tipos `Edm.String` ou `Collection(Edm.String)` não são submetidos à separação de palavras. Portanto, as comparações são apenas para correspondências exatas. Por exemplo, se você definir um campo f como “dia ensolarado”, `$filter=f eq 'sunny'` não encontrará correspondências, mas `$filter=f eq 'sunny day'` as encontrará. |  
|classificável |Por padrão, o sistema classifica os resultados pela pontuação, mas você pode configurar a classificação com base nos campos dos documentos. Os campos do tipo `Collection(Edm.String)` não podem ser "classificável". |  
|com faceta |Normalmente, usado em uma apresentação dos resultados da pesquisa que inclui uma contagem de ocorrências por categoria (por exemplo, hotéis em uma cidade específica). Essa opção não pode ser usada com campos do tipo `Edm.GeographyPoint`. Campos de tipo `Edm.String` que são filtráveis, "classificável" ou "facetable" podem ter, no máximo, 32 quilobytes de comprimento. Para obter detalhes, consulte [Criar índice (API REST)](/rest/api/searchservice/create-index).|  
|chaves |Identificador exclusivo para documentos no índice. Exatamente um campo deve ser escolhido como o campo chave e deve ser do tipo `Edm.String`.|  
|recuperáveis |Determina se o campo pode ser retornado em um resultado da pesquisa. Isso é útil quando você deseja usar um campo (por exemplo, *margem de lucro*) como mecanismo de filtro, classificação ou pontuação, mas não deseja que o campo seja visível para o usuário final. Esse atributo deve ser `true` for `key` .|  

Embora seja possível adicionar novos campos a qualquer momento, as definições de campo existentes são bloqueadas durante o tempo de vida do índice. Por esse motivo, os desenvolvedores geralmente usam o portal para criar índices simples, testar ideias ou usar as páginas do portal para pesquisar uma configuração. A iteração frequente em um design de índice será mais eficiente se você seguir uma abordagem baseada em código, de modo que você possa recriar o índice com facilidade.

> [!NOTE]
> As APIs usadas para criar um índice têm comportamentos padrão variados. Para as [APIs REST](/rest/api/searchservice/Create-Index), a maioria dos atributos é habilitada por padrão (por exemplo, "pesquisável" e "recuperável" são verdadeiros para campos de cadeia de caracteres) e, muitas vezes, você só precisa defini-los se desejar desativá-los. Para o SDK do .NET, o oposto é true. Em qualquer propriedade que você não definir explicitamente, o padrão é desabilitar o comportamento de pesquisa correspondente, a menos que você o habilite especificamente.

## `analyzers`

O elemento analisadores define o nome do analisador de linguagem a ser usado para o campo. Para obter mais informações sobre a variedade de analisadores disponíveis para você, consulte [adicionando analisadores a um índice de pesquisa cognitiva do Azure](search-analyzers.md). Os analisadores só podem ser usados com campos pesquisáveis. Depois que o analisador é atribuído a um campo, ele não pode ser alterado, a menos que você recompile o índice.

## `suggesters`

Um sugestor é uma seção do esquema que define quais campos em um índice são usados para dar suporte a consultas de preenchimento automático ou digitação antecipada em pesquisas. Normalmente, as cadeias de caracteres de pesquisa parciais são enviadas para as [sugestões (API REST)](/rest/api/searchservice/suggestions) enquanto o usuário está digitando uma consulta de pesquisa e a API retorna um conjunto de documentos ou frases sugeridos. 

Os campos adicionados a um sugestor são usados para criar termos de pesquisa de digitação antecipada. Todos os termos de pesquisa são criados durante a indexação e armazenados separadamente. Para saber mais sobre como criar uma estrutura de sugestor, confira [Add suggesters](index-add-suggesters.md) (Adicionar sugestores).

## `corsOptions`

O JavaScript do lado do cliente não pode chamar nenhuma API por padrão, pois o navegador impedirá todas as solicitações entre origens. Para permitir ao índice as consultas entre origens, habilite o CORS (Compartilhamento de Recursos entre Origens) definindo o atributo **corsOptions**. Por motivos de segurança, apenas APIs de consulta dão suporte para CORS. 

As seguintes opções podem ser definidas para CORS:

+ **allowedOrigins** (obrigatório): esta é uma lista de origens que terão acesso concedido ao índice. Isso significa que qualquer código JavaScript atendendo a partir dessas origens terá permissão para consultar o índice (supondo que ele forneça a chave de API correta). Cada origem é tipicamente da forma, `protocol://<fully-qualified-domain-name>:<port>` embora `<port>` seja frequentemente omitida. Consulte [Compartilhamento de recursos entre origens (Wikipédia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para obter mais detalhes.

  Se você quiser permitir acesso a todas as origens, inclua `*` como um único item na matriz **allowedOrigins**. *Essa não é uma prática recomendada para serviços de pesquisa de produção*, mas geralmente é útil para desenvolvimento e depuração.

+ **maxAgeInSeconds** (opcional): os navegadores usam esse valor para determinar a duração (em segundos) para respostas de simulação de CORS de cache. Esse deve ser um inteiro não negativo. Quanto maior for esse valor, melhor será o desempenho, porém, mais tempo levará para que as alterações de política CORS entrem em vigor. Se ele não for definido, uma duração padrão de cinco minutos será usada.

## `scoringProfiles`

Um [perfil de Pontuação](index-add-scoring-profiles.md) é uma seção do esquema que define comportamentos de Pontuação personalizados que permitem que você influencie quais itens aparecem mais acima nos resultados da pesquisa. Perfis de pontuação são compostos de funções e pesos de campos. Para usá-las, você pode especificar um perfil por nome na sequência de consulta.

Um padrão de pontuação perfil funciona nos bastidores para calcular uma pontuação para cada item em um conjunto de resultados de pesquisa. Você pode usar o perfil de pontuação interno e sem o nome. Como alternativa, defina **defaultScoringProfile** para usar um perfil personalizado como o padrão, invocado sempre que um perfil personalizado não for especificado na cadeia de caracteres de consulta.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Atributos e tamanho do índice (implicações de armazenamento)

O tamanho de um índice é determinado pelo tamanho dos documentos que você carrega, além da configuração de índice, como se você inclui sugestores e como você define atributos em campos individuais. 

A captura de tela a seguir ilustra padrões de armazenamento de índice resultantes de várias combinações de atributos. O índice é baseado no **índice de exemplo de imóveis**, que você pode criar facilmente usando o assistente de importação de dados. Embora os esquemas de índice não sejam mostrados, é possível inferir os atributos com base no nome do índice. Por exemplo, o índice *realestate-pesquisável* tem o atributo "pesquisável" selecionado e nada mais, o índice *realestate-recuperável* tem o atributo "recuperável" selecionado e nada mais, e assim por diante.

![Tamanho do índice com base na seleção de atributo](./media/search-what-is-an-index/realestate-index-size.png "Tamanho do índice com base na seleção de atributo")

Embora essas variantes de índice sejam artificiais, podemos referenciá-las para obter amplas comparações de como os atributos afetam o armazenamento. Definir "recuperável" aumenta o tamanho do índice? Não. Adicionar campos a um **Sugestor** aumenta o tamanho do índice? Sim.

Os índices que dão suporte ao filtro e à classificação são proporcionalmente maiores do que os índices que dão suporte à pesquisa de texto completo. Isso ocorre porque as operações de filtro e classificação verificam correspondências exatas, exigindo a presença de cadeias de caracteres de texto textual. Por outro lado, os campos pesquisáveis que dão suporte a consultas de texto completo usam índices invertidos, que são preenchidos com termos com token que consomem menos espaço do que documentos inteiros. 

> [!Note]
> A arquitetura de armazenamento é considerada um detalhe de implementação do Azure Pesquisa Cognitiva e pode ser alterada sem aviso prévio. Não há nenhuma garantia de que o comportamento atual persistirá no futuro.

## <a name="next-steps"></a>Próximas etapas

Com o conhecimento adquirido sobre a composição de índice, você poderá continuar no portal para criar seu primeiro índice. É recomendável iniciar com o assistente de **importação de dados** , escolhendo as fontes de dados hospedadas *realestate-US-Sample* ou *Hotéis-Sample* .

> [!div class="nextstepaction"]
> [Assistente de importação de dados (Portal)](search-get-started-portal.md)

Para ambos os conjuntos de dados, o assistente pode inferir um esquema de índice, importar os dados e gerar um índice pesquisável que você possa consultar usando o Search Explorer. Localize essas fontes de dados na página **conectar-se a seus dados** do assistente de **importação de dados** .

   ![Criar um índice de exemplo](media/search-what-is-an-index//import-wizard-sample-data.png "Criar um índice de exemplo")