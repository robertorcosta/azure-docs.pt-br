---
title: Reconstruir um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Adicione novos elementos, atualize elementos ou documentos existentes ou exclua documentos obsoletos em uma reconstrução completa ou indexação parcial para atualizar um índice de Pesquisa Cognitiva Do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 58b60a0eee8ab407709f33911d3c6b13ffbf301a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498371"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Como reconstruir um índice na Pesquisa Cognitiva do Azure

Este artigo explica como reconstruir um índice de Pesquisa Cognitiva Do Azure, as circunstâncias em que as reconstruções são necessárias e recomendações para mitigar o impacto das reconstruções em solicitações de consulta em andamento.

Um *recompilar* refere-se a remover e recriar as estruturas de dados físicos associadas a um índice, incluindo todos os índices invertidos com base no campo. Na Pesquisa Cognitiva do Azure, você não pode soltar e recriar campos individuais. Para recompilar um índice, todo o armazenamento de campo deve ser excluído, recriado com base em um esquema de índice existente ou revisado e, em seguida, novamente preenchido com os dados enviados por push para o índice ou extraídos de fontes externas. 

É comum recompilar índices durante o desenvolvimento, mas você também precisará recompilar um índice de nível de produção para acomodar alterações estruturais, como a adição de tipos complexos ou de campos a sugestores.

## <a name="rebuild-conditions"></a>Recompilar condições

Solte e recrie um índice se alguma das seguintes condições for verdadeira. 

| Condição | Descrição |
|-----------|-------------|
| Alterar uma definição de campo | A revisão de um nome de campo, de um tipo de dados ou de [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index) específicos (pesquisáveis, filtráveis, classificáveis, com faceta) exige uma recompilação completa. |
| Atribuir um analisador a um campo | Os [analisadores](search-analyzers.md) são definidos em um índice e, em seguida, são atribuídos aos campos. É possível adicionar uma nova definição de analisador a um índice a qualquer momento, mas só é possível *atribuir* um analisador quando o campo é criado. Isso é verdadeiro para as propriedades **analyzer** e **indexAnalyzer**. A propriedade **searchAnalyzer** é uma exceção (é possível atribuir essa propriedade a um campo existente). |
| Atualizar ou excluir uma definição de analisador em um índice | Não é possível excluir nem alterar uma configuração de analisador existente (analisador, gerador de token, filtro de token ou filtro de caracteres) no índice, a menos que você recompile todo o índice. |
| Adicionar um campo a um sugestor | Se um campo já existir e você desejar adicioná-lo a um constructo [Sugestores](index-add-suggesters.md), será necessário recompilar o índice. |
| Excluir um campo | Para remover fisicamente todos os rastreamentos de um campo, você precisa recriar o índice. Quando uma reconstrução imediata não é prática, você pode modificar o código do aplicativo para desativar o acesso ao campo "excluído" ou usar o [parâmetro de consulta $select](search-query-odata-select.md) para escolher quais campos estão representados no conjunto de resultados. Fisicamente, a definição e o conteúdo do campo permanecem no índice até a próxima recompilação, quando você aplica um esquema que omite o campo em questão. |
| Alternar camadas | Se você precisar de mais capacidade, não há atualização no local no portal Azure. Um novo serviço deve ser criado, e os índices devem ser construídos do zero no novo serviço. Para ajudar a automatizar esse processo, você pode usar o código de amostra **de restauração de backup de índice** neste [repo de amostra Azure Cognitive Search .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples). Este aplicativo fará backup do seu índice para uma série de arquivos JSON e, em seguida, recriará o índice em um serviço de pesquisa especificado.|

## <a name="update-conditions"></a>Atualizar condições

Muitas outras modificações podem ser feitas sem impactar as estruturas físicas existentes. Especificamente, as seguintes alterações *não* requerem uma reconstrução do índice. Para essas alterações, você pode [atualizar uma definição de índice](https://docs.microsoft.com/rest/api/searchservice/update-index) com suas alterações.

+ Adicionar um novo campo
+ Definir o atributo **recuperável** em um campo existente
+ Definir **searchAnalyzer** em um campo existente
+ Adicionar uma nova definição de analisador em um índice
+ Adicionar, atualizar ou excluir perfis de pontuação
+ Adicionar, atualizar ou excluir configurações de CORS
+ Adicionar, atualizar ou excluir synonymMaps

Quando você adiciona um novo campo, os documentos indexados existentes recebem um valor nulo para o novo campo. Em uma atualização de dados futura, os valores dos dados de origem externa substituem as nulas adicionadas pelo Azure Cognitive Search. Para obter mais informações sobre como atualizar o conteúdo do índice, consulte [Adicionar, atualizar ou excluir documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Como recompilar um índice

Durante o desenvolvimento, o esquema de índice muda com freqüência. Você pode planejar para ele criando índices que podem ser excluídos, recriados e recarregados rapidamente com um pequeno conjunto de dados representativos.

Para aplicativos já em produção, é recomendável criar um novo índice que é executado lado a lado de um índice existente para evitar a inatividade de consulta. Seu código de aplicativo fornece redirecionamento para o novo índice.

A indexação não é executada em segundo plano e o serviço equilibrará a indexação adicional em relação às consultas em andamento. Durante a indexação, você pode [monitorar solicitações de consulta](search-monitor-queries.md) no portal para garantir que as consultas estejam sendo completadas em tempo hábil.

1. Determine se uma reconstrução é necessária. Se você estiver apenas adicionando campos ou alterando alguma parte do índice que não está relacionada aos campos, você pode simplesmente [atualizar a definição](https://docs.microsoft.com/rest/api/searchservice/update-index) sem excluí-la, recriar e recarregá-la totalmente.

1. [Obtenha uma definição](https://docs.microsoft.com/rest/api/searchservice/get-index) de índice no caso de precisar dele para referência futura.

1. [Solte o índice existente,](https://docs.microsoft.com/rest/api/searchservice/delete-index)assumindo que você não está executando índices novos e antigos lado a lado. 

   Todas as consultas que direcionam esse índice são descartadas imediatamente. Lembre-se que a exclusão de um índice é irreversível, destruindo o armazenamento físico para a coleta de campos e outras construções. Pare para pensar sobre as implicações antes de deixá-lo cair. 

1. [Crie um índice revisado,](https://docs.microsoft.com/rest/api/searchservice/create-index)onde o corpo da solicitação inclua definições de campo alteradas ou modificadas.

1. [Carregue o índice com documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) de uma fonte externa.

Quando você cria o índice, o armazenamento físico é alocado para cada campo no esquema de índice, com um índice invertido criado para cada campo pesquisável. Os campos não pesquisáveis podem ser usados em filtros ou expressões, mas não têm índices invertidos e não são pesquisáveis de texto completo ou de difuso. Em uma recompilação de índice, esses índices invertidos são excluídos e recriados com base no esquema de índice que você fornecer.

Quando você carrega o índice, o índice invertido de cada campo é preenchido com todas as palavras exclusivas, indexadas de cada documento, com um mapa das IDs do documento correspondentes. Por exemplo, durante a indexação de um conjunto de dados de hotéis, um índice invertido criado para um campo de cidade pode conter os termos para Seattle, Portland e assim por diante. Documentos que incluem Seattle ou Portland no campo Cidade teriam sua ID de documento listada juntamente com o termo. Em qualquer operação [Adicionar, atualizar ou excluir](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), os termos e a lista de IDs de documento são atualizadas de acordo.

> [!NOTE]
> Se você tiver rigorosos requisitos de SLA, você pode considerar o provisionamento de um novo serviço especificamente para esse trabalho, com o desenvolvimento e indexação ocorrendo em isolamento completo de um índice de produção. Um serviço separado é executado em seu próprio hardware, eliminando qualquer possibilidade de contenção de recursos. Quando o desenvolvimento estiver concluído, você deixaria o novo índice no lugar, redirecionando consultas para o novo ponto final e índice, ou executaria o código final para publicar um índice revisado no seu serviço original de Pesquisa Cognitiva do Azure. Atualmente, não há nenhum mecanismo para mover um índice pronto para uso para outro serviço.

## <a name="check-for-updates"></a>Verificar atualizações

Você pode começar a consultar um índice, assim que o primeiro documento for carregado. Se você souber a ID de um documento, a [API REST de Procurar documento](https://docs.microsoft.com/rest/api/searchservice/lookup-document) retorna o documento específico. Para testes mais amplos, você deve aguardar até que o índice seja totalmente carregado e, em seguida, usar consultas para verificar o contexto em que você espera ver.

Você pode usar [o Search Explorer](search-explorer.md) ou uma ferramenta de teste da Web como o [Carteiro](search-get-started-postman.md) para verificar se há conteúdo atualizado.

Se você adicionou ou renomeou um campo, use [$select](search-query-odata-select.md) para retornar esse campo:`search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Confira também

+ [Visão geral do indexador](search-indexer-overview.md)
+ [Indexar grandes conjuntos de dados em escala](search-howto-large-index.md)
+ [Indexando no portal](search-import-data-portal.md)
+ [Indexador do Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexador do Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexador de armazenamento Azure Blob](search-howto-indexing-azure-blob-storage.md)
+ [Indexador de armazenamento de tabelas azure](search-howto-indexing-azure-tables.md)
+ [Segurança na Busca Cognitiva do Azure](search-security-overview.md)