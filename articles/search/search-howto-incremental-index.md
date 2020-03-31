---
title: Configurar cache e enriquecimento incremental (visualização)
titleSuffix: Azure Cognitive Search
description: Permitir cache e preservar o estado de conteúdo enriquecido para processamento controlado em uma habilidade cognitiva. Esse recurso está atualmente em visualização pública.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 66bac2a063a3257a2101ca2f30e5946264adb9ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989545"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Como configurar o cache para enriquecimento incremental na Pesquisa Cognitiva do Azure

> [!IMPORTANT] 
> O enriquecimento incremental está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-versão prévia](search-api-preview.md) fornece esse recurso. Não há suporte a portal ou .NET SDK no momento.

Este artigo mostra como adicionar cache a um pipeline de enriquecimento para que você possa modificar gradualmente as etapas sem ter que reconstruir todas as vezes. Por padrão, um skillset é apátrida, e alterar qualquer parte de sua composição requer uma reprise completa do indexador. Com o enriquecimento incremental, o indexador pode determinar quais partes da árvore de documentos precisam ser atualizadas com base em alterações detectadas nas definições de skillset ou indexador. A saída processada existente é preservada e reutilizada sempre que possível. 

O conteúdo armazenado em cache é colocado no Azure Storage usando as informações da conta que você fornece. O contêiner, `ms-az-search-indexercache-<alpha-numerc-string>`chamado , é criado quando você executa o indexador. Ele deve ser considerado um componente interno gerenciado pelo seu serviço de pesquisa e não deve ser modificado.

Se você não estiver familiarizado com a configuração de indexadores, comece com [a visão geral do indexador](search-indexer-overview.md) e, em seguida, continue com as [habilidades](cognitive-search-working-with-skillsets.md) para aprender sobre pipelines de enriquecimento. Para obter mais informações sobre conceitos-chave, consulte [o enriquecimento incremental](cognitive-search-incremental-indexing-conceptual.md).

## <a name="enable-caching-on-an-existing-indexer"></a>Habilite o cache em um indexador existente

Se você tiver um indexador existente que já tenha um skillset, siga as etapas nesta seção para adicionar cache. Como uma operação única, você terá que redefinir e reexecutar o indexador na íntegra antes que o processamento incremental possa fazer efeito.

> [!TIP]
> Como prova de conceito, você pode executar através deste [portal quickstart](cognitive-search-quickstart-blob.md) para criar objetos necessários e, em seguida, usar Carteiro ou o portal para fazer suas atualizações. Você pode querer anexar um recurso de Serviços Cognitivos faturados. A execução do indexador várias vezes esgotará a alocação diária gratuita antes de completar todas as etapas.

### <a name="step-1-get-the-indexer-definition"></a>Passo 1: Obtenha a definição do indexador

Comece com um indexador válido e existente que tenha esses componentes: fonte de dados, skillset, índice. Seu indexador deve ser executado. 

Usando um cliente API, construa uma [solicitação get indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer) para obter a configuração atual do indexador. Quando você usa a versão aPI de visualização para o INDEXador GET, uma `cache` propriedade definida como nula é adicionada às definições.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Copie a definição do indexador da resposta.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Passo 2: Modificar a propriedade de cache na definição do indexador

Por padrão, a `cache` propriedade é nula. Use um cliente de API para definir a configuração de cache (o portal não suporta essa atualização de partículas). 

Modifique o objeto de cache para incluir as seguintes propriedades necessárias e opcionais: 

+ O `storageConnectionString` é necessário e deve ser configurado como uma seqüência de conexão de armazenamento Azure. 
+ A `enableReprocessing` propriedade booleana`true` é opcional (por padrão), e indica que o enriquecimento incremental está ativado. Quando necessário, você pode `false` configurá-lo para suspender o processamento incremental enquanto outras operações com uso `true` intensivo de recursos, como indexar novos documentos, estão em andamento e, em seguida, devoli-lo para mais tarde.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>Passo 3: Redefinir o indexador

Uma redefinição do indexador é necessária ao configurar o enriquecimento incremental para os indexadores existentes para garantir que todos os documentos estejam em um estado consistente. Você pode usar o portal ou um cliente de API e a [API Reset Indexer REST](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) para esta tarefa.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Passo 4: Salve a definição atualizada

[Atualize o indexador](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) com uma solicitação PUT, o corpo da solicitação deve conter a definição atualizada do indexador que possui a propriedade cache. Se você conseguir um 400, verifique a definição do indexador para garantir que todos os requisitos sejam atendidos (fonte de dados, skillset, índice).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

Se você emitir agora outra solicitação GET no indexador, `ID` a resposta do serviço incluirá uma propriedade no objeto cache. A seqüência alfanumérica é anexada ao nome do recipiente contendo todos os resultados armazenados em cache e estado intermediário de cada documento processado por este indexador. O ID será usado para nomear exclusivamente o cache no armazenamento Blob.

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>Passo 5: Execute o indexador

Para executar o indexador, você pode usar o portal ou a API. No portal, na lista de indexadores, selecione o indexador e clique **em Executar**. Uma vantagem de usar o portal é que você pode monitorar o status do indexador, observar a duração do trabalho e quantos documentos são processados. As páginas do portal são atualizadas a cada poucos minutos.

Alternativamente, você pode usar REST para [executar o indexador](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Depois que o indexador for executado, você pode encontrar o cache no armazenamento Azure Blob. O nome do contêiner está no seguinte formato:`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Uma reset e uma reprise do indexador resultam em uma reconstrução completa para que o conteúdo possa ser armazenado em cache. Todos os enriquecimentos cognitivos serão reexecutados em todos os documentos.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Passo 6: Modifique um skillset e confirme o enriquecimento incremental

Para modificar um skillset, você pode usar o portal ou a API. Por exemplo, se você estiver usando a tradução `en` `es` de texto, uma simples mudança inline de para ou outro idioma é suficiente para testes de prova de conceito de enriquecimento incremental.

Execute o indexador novamente. Apenas as partes de uma árvore de documentos enriquecida são atualizadas. Se você usou o [portal quickstart](cognitive-search-quickstart-blob.md) como prova de conceito, modificando a habilidade de tradução de texto para 'es', você notará que apenas 8 documentos são atualizados em vez dos 14 originais. Os arquivos de imagem não afetados pelo processo de tradução são reutilizados do cache.

## <a name="enable-caching-on-new-indexers"></a>Habilite o cache em novos indexadores

Para configurar o enriquecimento incremental para um novo indexador, tudo o que você precisa fazer é incluir a `cache` propriedade na carga de definição do indexador ao ligar para O [Indexador de Criação (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer). 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>Verificando a saída armazenada em cache

O cache é criado, usado e gerenciado pelo indexador, e seu conteúdo não é representado em um formato que seja legível em humanos. A melhor maneira de determinar se o cache é usado é executando o indexador e comparar métricas de antes e depois para contagem de tempo de execução e documentos. 

Por exemplo, assuma um skillset que começa com análise de imagem e OCR (Optical Character Recognition, reconhecimento óptico de caracteres) de documentos digitalizados, seguido pela análise a jusante do texto resultante. Se você modificar uma habilidade de texto a jusante, o indexador poderá recuperar todo o conteúdo de imagem e OCR processado anteriormente do cache, atualizando e processando apenas alterações relacionadas ao texto indicadas por suas edições. Você pode esperar ver menos documentos na contagem de documentos (por exemplo 8/8 em vez de 14/14 no prazo original), tempos de execução mais curtos e menos encargos em sua conta.

## <a name="working-with-the-cache"></a>Trabalhando com o cache

Uma vez que o cache esteja operacional, os indexadores verificam o cache sempre que [o Execut Indexador](https://docs.microsoft.com/rest/api/searchservice/run-indexer) é chamado, para ver quais partes da saída existente podem ser usadas. 

A tabela a seguir resume como várias APIs se relacionam com o cache:

| API           | Impacto em cache     |
|---------------|------------------|
| [Criar indexador (2019-05-06-Visualização)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | Cria e executa um indexador no primeiro uso, incluindo a criação de um cache se a definição do indexador o especificar. |
| [Executar indexador](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | Executa um oleoduto de enriquecimento sob demanda. Esta API lê do cache se ele existe ou cria um cache se você adicionou cache a uma definição atualizada de indexador. Quando você executa um indexador que tem o cache ativado, o indexador omite etapas se a saída em cache puder ser usada. Você pode usar a versão aPI geralmente disponível ou visualização desta API.|
| [Redefinir Indexador](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| Limpa o indexador de qualquer informação de indexação incremental. A próxima execução do indexador (sob demanda ou agenda) é o reprocessamento completo do zero, incluindo a reexecução de todas as habilidades e a reconstrução do cache. É funcionalmente equivalente a excluir o indexador e recriá-lo. Você pode usar a versão aPI geralmente disponível ou visualização desta API.|
| [Reset Skills (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | Especifica quais habilidades executar na próxima corrida de indexador, mesmo que você não tenha modificado nenhuma habilidade. O cache é atualizado em conformidade. As saídas, como um armazenamento de conhecimento ou índice de pesquisa, são atualizadas usando dados reutilizáveis do cache mais novos conteúdos pela habilidade atualizada. |

Para obter mais informações sobre o controle do que acontece com o cache, consulte [cache gerenciamento](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Próximas etapas

O enriquecimento incremental é aplicável em indexadores que contêm habilidades. Como próximo passo, visite a documentação do skillset para entender conceitos e composição. 

Além disso, uma vez que você habilitar o cache, você vai querer saber sobre os parâmetros e APIs que levam ao cache, incluindo como substituir ou forçar comportamentos específicos. Para obter mais informações, consulte os links a seguir.

+ [Conceitos e composição de skillset](cognitive-search-working-with-skillsets.md)
+ [Como criar um skillset](cognitive-search-defining-skillset.md)
+ [Introdução ao enriquecimento incremental e cache](cognitive-search-incremental-indexing-conceptual.md)
