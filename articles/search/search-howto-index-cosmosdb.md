---
title: Pesquisar sobre os dados do Azure Cosmos DB
titleSuffix: Azure Cognitive Search
description: Importar dados do Azure Cosmos DB para um índice pesquisável no Azure Cognitive Search. Os indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282998"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Como indexar dados do Cosmos DB usando um indexador no Azure Cognitive Search 

> [!IMPORTANT] 
> A API SQL está geralmente disponível.
> API do MongoDB, API Gremlin e suporte à API de Cassandra estão atualmente em pré-visualização pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Você pode solicitar acesso às visualizações preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de versão prévia. Atualmente, há suporte limitado ao portal e não há suporte para o SDK do .NET.

> [!WARNING]
> Apenas as coleções do Cosmos DB com uma [política de indexação](https://docs.microsoft.com/azure/cosmos-db/index-policy) definida como [Consistente](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) são suportadas pela Azure Cognitive Search. A indexação de coleções com uma política de indexação preguiçosa não é recomendada e pode resultar em dados ausentes. Não são suportadas coleções com indexação desativada.

Este artigo mostra como configurar um [indexador](search-indexer-overview.md) Azure Cosmos DB para extrair conteúdo e torná-lo pesquisável na Pesquisa Cognitiva do Azure. Este fluxo de trabalho cria um índice de Pesquisa Cognitiva Do Azure e o carrega com o texto existente extraído do Azure Cosmos DB. 

Como a terminologia pode ser confusa, vale a pena notar que [a indexação Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) e [a indexação azure Cognitive Search](search-what-is-an-index.md) são operações distintas, exclusivas de cada serviço. Antes de iniciar a indexação da Pesquisa Cognitiva do Azure, o banco de dados Do Azure Cosmos DB já deve existir e conter dados.

O indexador Cosmos DB na Pesquisa Cognitiva Do Azure pode rastrear [itens do Azure Cosmos DB acessados](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) através de diferentes protocolos. 

+ Para [a API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), que geralmente está disponível, você pode usar o [portal](#cosmos-indexer-portal), [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)ou [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) para criar a fonte de dados e indexador.

+ Para [a API (visualização) do MongoDB,](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)você pode usar o [portal](#cosmos-indexer-portal) ou a [versão 2019-05-06-Preview](search-api-preview.md) do REST para criar a fonte de dados e o indexador.

+ Para [API Cassandra (pré-visualização)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction) e [API Gremlin (visualização),](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)você só pode usar a [versão da API REST 2019-05-06-Preview](search-api-preview.md) para criar a fonte de dados e o indexador.


> [!Note]
> Você pode votar na [API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) do User Voice para a Table Se quiser vê-la apoiada na Pesquisa Cognitiva do Azure.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Usar o portal

> [!Note]
> Atualmente, o portal suporta a API SQL e a API MongoDB (preview).

O método mais fácil para indexar itens do Azure Cosmos DB é usar um assistente no [portal Azure](https://portal.azure.com/). Ao amostrar dados e ler metadados no contêiner, o assistente [**de importar dados**](search-import-data-portal.md) no Azure Cognitive Search pode criar um índice padrão, mapear campos de origem para campos de índice de destino e carregar o índice em uma única operação. Dependendo do tamanho e complexidade da fonte de dados, é possível ter um índice de pesquisa de texto completo operando em minutos.

Recomendamos o uso da mesma região ou localização tanto para o Azure Cognitive Search quanto para o Azure Cosmos DB para menor latência e para evitar cargas de largura de banda.

### <a name="1---prepare-source-data"></a>1- Preparar dados de origem

Você deve ter uma conta Cosmos DB, um banco de dados Azure Cosmos DB mapeado para a API SQL, API MongoDB (visualização) ou API Gremlin (visualização) e conteúdo no banco de dados.

Certifique-se de que seu banco de dados Cosmos DB contém dados. O [assistente de dados Importação](search-import-data-portal.md) lê metadados e realiza a amostragem de dados para inferir um esquema de índice, mas também carrega dados do Cosmos DB. Se os dados estão faltando, o assistente pára com esse erro "Esquema de erro de detecção de índice da fonte de dados: não foi possível construir um índice de protótipo porque a 'coleta vazia' da fonte de dados não retornou nenhum dado".

### <a name="2---start-import-data-wizard"></a>2 - Iniciar o Assistente para Importação de Dados 

Você pode [iniciar o assistente a](search-import-data-portal.md) partir da barra de comando na página do serviço de pesquisa cognitiva do Azure, ou se estiver se conectando à API SQL do Cosmos DB, você pode clicar em Adicionar pesquisa cognitiva do **Azure** na seção **Configurações** do painel de navegação esquerdo da sua conta Cosmos DB.

   ![Importar comando de dados no portal](./media/search-import-data-portal/import-data-cmd2.png "Iniciar o assistente Importar dados")

### <a name="3---set-the-data-source"></a>3 - Configurar a fonte de dados

Na página **de origem dos dados,** a fonte deve ser **Cosmos DB,** com as seguintes especificações:

+ **Nome** é o nome do objeto de origem de dados. Uma vez criado, você pode escolhê-lo para outras cargas de trabalho.

+ **A conta Cosmos DB** deve ser a seqüência de `AccountEndpoint` conexões primária ou secundária do Cosmos DB, com um e um `AccountKey`. Para coleções MongoDB, adicione **ApiKind=MongoDb** ao final da seqüência de conexão e separe-a da seqüência de conexão com um ponto e vírgula. Para a API Gremlin e a API Cassandra, use as instruções para a [API REST](#cosmosdb-indexer-rest).

+ **Banco de dados** é um banco de dados existente da conta. 

+ **A coleção** é um contêiner de documentos. Os documentos devem existir para que a importação tenha sucesso. 

+ **A consulta** pode estar em branco se você quiser todos os documentos, caso contrário, você pode inserir uma consulta que seleciona um subconjunto de documentos. **A consulta** só está disponível para a API SQL.

   ![Definição de fonte de dados Do Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Definição de fonte de dados Do Cosmos DB")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Pule a página "Enriquecer conteúdo" no assistente

Adicionar habilidades cognitivas (ou enriquecimento) não é um requisito de importação. A menos que você tenha uma necessidade específica de [adicionar o enriquecimento de IA](cognitive-search-concept-intro.md) ao seu pipeline de indexação, você deve pular esta etapa.

Para pular a etapa, clique nos botões azuis na parte inferior da página para "Next" e "Skip".

### <a name="5---set-index-attributes"></a>5- Definir atributos de índice

Na página **índice**, deve haver uma lista de campos com um tipo de dados e uma série de caixas de seleção para definir os atributos de índice. O assistente pode gerar uma lista de campos com base em metadados e amostrando os dados de origem. 

Você pode selecionar atributos em massa clicando na caixa de seleção na parte superior de uma coluna de atributos. Escolha **Recuperável** e **Pesquisável** para cada campo que deve ser devolvido a um aplicativo cliente e sujeito ao processamento completo de pesquisa de texto. Você notará que os inteiros não são texto completo ou pesquisáveis em delineados (os números são avaliados verbatim e muitas vezes são úteis em filtros).

Revise a descrição dos [atributos](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) de índice e [dos analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support) para obter mais informações. 

Reserve um tempo para revisar suas seleções. Depois de executar o assistente, estruturas de dados físicos são criadas e você não poderá editar esses campos sem descartar e recriar todos os objetos.

   ![Definição do índice Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definição do índice Cosmos DB")

### <a name="6---create-indexer"></a>6 - Criar indexador

Totalmente especificado, o assistente cria três objetos diferentes em seu serviço de pesquisa. Um objeto de origem de dados e objeto de índice são salvos como recursos nomeados no serviço de pesquisa cognitiva do Azure. A última etapa cria um objeto do indexador. Dar um nome ao indexador permite que ele exista como um recurso autônomo, que pode ser agendado e gerenciado independentemente do objeto de fonte de dados e índice criado na mesma sequência do assistente.

Se você não estiver familiarizado com indexadores, um *indexador* é um recurso no Azure Cognitive Search que rastreia uma fonte de dados externa para conteúdo pesquisável. A saída do assistente de **dados Importação** é um indexador que rastreia sua fonte de dados Cosmos DB, extrai conteúdo pesquisável e importa-o para um índice na Pesquisa Cognitiva do Azure.

A captura de tela a seguir mostra a configuração do indexador padrão. Você pode mudar para **Uma vez** se quiser executar o indexador uma vez. Clique **em Enviar** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

   ![Definição do indexador Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Definição do indexador Cosmos DB")

Pode-se monitorar a importação de dados nas páginas do portal. As notificações de progresso indicam o status da indexação e quantos documentos são carregados. 

Quando a indexação estiver concluída, você pode usar o [Search explorer](search-explorer.md) para consultar seu índice.

> [!NOTE]
> Se você não ver os dados que você espera, talvez seja necessário definir mais atributos em mais campos. Exclua o índice e o indexador que você acabou de criar e passe pelo assistente novamente, modificando suas seleções para atributos de índice na etapa 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usar APIs REST

Você pode usar a API REST para indexar dados do Azure Cosmos DB, seguindo um fluxo de trabalho de três partes comum a todos os indexadores no Azure Cognitive Search: crie uma fonte de dados, crie um índice, crie um indexador. A extração de dados do Cosmos DB ocorre quando você envia a solicitação Criar indexador. Depois que essa solicitação for concluída, você terá um índice que pode ser consultado. 

> [!NOTE]
> Para indexar dados da Cosmos DB Gremlin API ou Cosmos DB Cassandra API, você deve primeiro solicitar acesso às visualizações fechadas preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Uma vez que sua solicitação seja processada, você receberá instruções sobre como usar a [versão da API REST 2019-05-06-Preview](search-api-preview.md) para criar a fonte de dados.

No início deste artigo, é mencionado que [a indexação azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) e [a indexação de pesquisa cognitiva do Azure](search-what-is-an-index.md) são operações distintas. Para a indexação Cosmos DB, por padrão todos os documentos são automaticamente indexados, exceto com a API Cassandra. Se você desativar a indexação automática, os documentos só podem ser acessados através de seus links próprios ou por consultas usando o ID do documento. A indexação da Pesquisa Cognitiva do Azure requer que a indexação automática Cosmos DB seja ligada na coleção que será indexada pela Azure Cognitive Search. Ao se inscrever na visualização do indexador API Cosmos DB Cassandra, você receberá instruções sobre como configurar a indexação do Cosmos DB.

> [!WARNING]
> O Azure Cosmos DB é a próxima geração do DocumentDB. Anteriormente com a versão aPI **2017-11-11** você poderia usar a `documentdb` sintaxe. Isso significava que você poderia especificar seu tipo de origem de dados como `cosmosdb` ou `documentdb`. A partir da versão **API 2019-05-06,** tanto as APIs `cosmosdb` de Pesquisa Cognitiva do Azure quanto o Portal só suportam a sintaxe conforme instruído neste artigo. Isso significa que o `cosmosdb` tipo de fonte de dados deve se você quiser se conectar a um ponto final do Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 - Montar entradas para a solicitação

Para cada solicitação, você deve fornecer o nome do serviço e a chave de administração para a Pesquisa Cognitiva do Azure (no cabeçalho POST) e o nome da conta de armazenamento e a chave para armazenamento blob. Você pode usar [o Carteiro](search-get-started-postman.md) para enviar solicitações HTTP para a Pesquisa Cognitiva do Azure.

Copie os quatro valores a seguir no Bloco de Notas para que você possa colá-los em uma solicitação:

+ Nome do serviço de busca cognitiva do Azure
+ Chave de administrador de pesquisa cognitiva do Azure
+ Cadeia de conexão Cosmos DB

Você pode encontrar esses valores no portal:

1. Nas páginas do portal para a Pesquisa Cognitiva do Azure, copie a URL do serviço de pesquisa da página Visão Geral.

2. No painel de navegação à esquerda, clique **em Teclas** e copie a tecla primária ou secundária (elas são equivalentes).

3. Mude para as páginas do portal para sua conta de armazenamento Cosmos. No painel de navegação à esquerda, em **Configurações,** clique em **Teclas**. Esta página fornece um URI, dois conjuntos de strings de conexão e dois conjuntos de teclas. Copie uma das strings de conexão para o bloco de notas.

### <a name="2---create-a-data-source"></a>2 - Criar uma fonte de dados

A **fonte de dados** especifica os dados no índice, nas credenciais e nas políticas para identificação de alterações nos dados (como documentos modificados ou excluídos em sua coleção). A fonte de dados é definida como um recurso independente para que possa ser usada por vários indexadores.

Para criar uma fonte de dados, formule uma solicitação POST:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

O corpo da solicitação contém a definição da fonte de dados, que deve incluir os seguintes campos:

| Campo   | Descrição |
|---------|-------------|
| **name** | Obrigatórios. Escolha qualquer nome para representar seu objeto de origem de dados. |
|**type**| Obrigatórios. Deve ser `cosmosdb`. |
|**Credenciais** | Obrigatórios. Deve ser uma seqüência de conexão Cosmos DB.<br/>Para coleções SQL, as strings de conexão estão neste formato:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/> Para coleções do MongoDB, adicione **ApiKind=MongoDb** à cadeia de conexão: <br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Para gráficos Gremlin e tabelas Cassandra, inscreva-se na visualização do [indexador fechado](https://aka.ms/azure-cognitive-search/indexer-preview) para ter acesso à pré-visualização e informações sobre como formatar as credenciais.<br/><br/>Evite números de porta na url do ponto de extremidade. Se você incluir o número da porta, o Azure Cognitive Search não poderá indexar seu banco de dados Azure Cosmos DB.|
| **Recipiente** | Contém os seguintes elementos: <br/>**nome**: Obrigatório. Especifique o ID da coleção de banco de dados a ser indexado.<br/>**query**: opcional. Você pode especificar uma consulta para achatar um documento JSON arbitrário em um esquema plano que o Azure Cognitive Search pode indexar.<br/>Para a API do MongoDB, a API Gremlin e a API de Cassandra, as consultas não são suportadas. |
| **dataChangeDetectionPolicy** | Recomendável. Consulte a seção [Indexando documentos alterados](#DataChangeDetectionPolicy).|
|**dataDeletionDetectionPolicy** | Opcional. Consulte a seção [Indexando documentos excluídos](#DataDeletionDetectionPolicy).|

### <a name="using-queries-to-shape-indexed-data"></a>Usando consultas para formatar dados indexados
É possível especificar uma consulta do SQL para mesclar propriedades ou matrizes aninhadas, projetar propriedades JSON e filtrar os dados a serem indexados. 

> [!WARNING]
> As consultas personalizadas não são suportadas para **a API MongoDB,** **API Gremlin**e **API Cassandra**: `container.query` o parâmetro deve ser definido como nulo ou omitido. Se você precisar usar uma consulta personalizada, informe em [Voz do Usuário](https://feedback.azure.com/forums/263029-azure-search).

Documento de exemplo:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Filtrar consulta:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Consulta de mesclagem:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Consulta de projeção:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Consulta de mesclagem de matriz:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 - Criar um índice de pesquisa de destino 

[Crie um índice de pesquisa cognitiva azure de destino](/rest/api/searchservice/create-index) se você ainda não tiver um. O exemplo a seguir cria um índice com um campo de ID e descrição:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Verifique se o esquema do índice de destino é compatível com o esquema dos documentos JSON de origem ou com a saída da projeção de consulta personalizada.

> [!NOTE]
> Para coleções particionadas, a chave de documento `_rid` padrão é a propriedade do Azure `rid` Cosmos DB, que o Azure Cognitive Search renomeia automaticamente porque os nomes de campo não podem começar com um caractere sublinhado. Além disso, os valores do Azure Cosmos DB `_rid` contêm caracteres inválidos nas teclas de pesquisa cognitiva do Azure. Por esse motivo, os valores `_rid` são codificados em Base64.
> 
> Para coleções do MongoDB, o Azure `_id` Cognitive `id`Search renomeia automaticamente a propriedade para .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mapeamento entre tipos de dados JSON e tipos de dados de pesquisa cognitiva do Azure
| Tipo de dados JSON | Tipos de campos de índice de destino compatíveis |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Números que se parecem com inteiros |Edm.Int32, Edm.Int64, Edm.String |
| Números que se parecem com pontos flutuantes |Edm.Double, Edm.String |
| String |Edm.String |
| Matrizes de tipos primitivos, por exemplo, [“a”, “b”, “c”] |Collection(Edm.String) |
| Cadeias de caracteres que se parecem com datas |Edm.DateTimeOffset, Edm.String |
| Objetos GeoJSON, por exemplo, { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Outros objetos JSON |N/D |

### <a name="4---configure-and-run-the-indexer"></a>4 - Configurar e executar o indexador

Uma vez que o índice e a fonte de dados forem criados, será possível criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Esse indexador é executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O intervalo mais curto com suporte é de 5 minutos. O agendamento é opcional – se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre como definir os horários dos indexadores, consulte [Como agendar indexadores para a Pesquisa Cognitiva do Azure](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Usar o .NET

O SDK .NET geralmente disponível tem total paridade com a API REST geralmente disponível. É recomendável examinar a seção anterior da API REST para aprender os conceitos, fluxo de trabalho e requisitos. Consulte a seguinte documentação de referência de API do .NET para implementar um indexador JSON em código gerenciado.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexando documentos alterados

A finalidade de uma política de detecção de alteração de dados é identificar de maneira eficaz dados alterados. Atualmente, a única política [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) suportada `_ts` é o uso da propriedade (carimbo de tempo) fornecida pelo Azure Cosmos DB, que é especificada da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

O uso dessa política é altamente recomendável para garantir o bom desempenho do indexador. 

Se estiver usando uma consulta personalizada, garanta que a propriedade `_ts` será projetada pela consulta.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Progresso incremental e consultas personalizadas

O progresso incremental durante a indexação garante que, se a execução do indexador é interrompida por falhas transitórias ou limite de tempo de execução, o indexador pode escolher onde ela parou próxima vez que ele é executado, em vez de toda a coleção de zero de índice novamente. Isso é especialmente importante durante a indexação de coleções grandes. 

Para habilitar o progresso incremental usando uma consulta personalizada, certifique-se de que sua consulta classifica os resultados pelo `_ts` coluna. Isso permite a verificação periódica que o Azure Cognitive Search usa para fornecer progresso incremental na presença de falhas.   

Em alguns casos, mesmo que `ORDER BY [collection alias]._ts` sua consulta contenha uma cláusula, o Azure Cognitive `_ts`Search pode não inferir que a consulta é ordenada pelo . Você pode dizer ao Azure Cognitive Search `assumeOrderByHighWaterMarkColumn` que os resultados são ordenados usando a propriedade de configuração. Para especificar essa dica, crie ou atualize o indexador da seguinte maneira: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexando documentos excluídos

Quando as linhas são excluídas da coleção, normalmente, você também deseja excluí-las do índice de pesquisa. A finalidade de uma política de detecção de exclusão de dados é identificar de maneira eficaz dados excluídos. Atualmente, a única política com suporte é a política `Soft Delete` (a exclusão recebe algum tipo de marcador), que é especificada da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Se estiver usando uma consulta personalizada, garanta que a propriedade referenciada por `softDeleteColumnName` é projetada pela consulta.

O seguinte exemplo cria uma fonte de dados com uma política de exclusão reversível:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="next-steps"></a><a name="NextSteps"></a>Próximas etapas

Parabéns! Você aprendeu a integrar o Azure Cosmos DB com o Azure Cognitive Search usando um indexador.

* Para saber mais sobre o Azure Cosmos DB, consulte a [página de serviço do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Para saber mais sobre a Pesquisa Cognitiva do Azure, consulte a [página do serviço de pesquisa](https://azure.microsoft.com/services/search/).
