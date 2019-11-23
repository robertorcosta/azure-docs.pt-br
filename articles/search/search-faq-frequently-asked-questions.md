---
title: Perguntas frequentes (FAQ)
titleSuffix: Azure Cognitive Search
description: Obtenha respostas para perguntas comuns sobre o Microsoft Azure Pesquisa Cognitiva Service, um serviço de pesquisa hospedado na nuvem no Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fee74cb6ec5acd5fa0f171eab9769a833f04ad66
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792903"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Pesquisa Cognitiva do Azure-perguntas frequentes (FAQ)

 Encontre respostas para perguntas frequentes sobre conceitos, código e cenários relacionados ao Pesquisa Cognitiva do Azure.

## <a name="platform"></a>Plataforma

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Como o Azure Pesquisa Cognitiva diferente da pesquisa de texto completo em meu DBMS?

O Azure Pesquisa Cognitiva dá suporte a várias fontes de dados, [análise linguística para muitas linguagens](https://docs.microsoft.com/rest/api/searchservice/language-support), [análise personalizada para entradas de dados interessantes e incomuns](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), pesquisa de controles de classificação por meio de [perfis de Pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)e recursos de experiência do usuário, como typeahead, realce de visita e navegação facetada. Ele também inclui outros recursos, como sinônimos e sintaxe de consulta avançada, mas esses geralmente não são recursos diferenciados.

### <a name="what-is-the-difference-between-azure-cognitive-search-and-elasticsearch"></a>Qual é a diferença entre o Azure Pesquisa Cognitiva e o Elasticsearch?

Ao comparar as tecnologias de pesquisa, os clientes frequentemente pedem informações específicas sobre como o Azure Pesquisa Cognitiva se compara com o Elasticsearch. Os clientes que escolhem o Azure Pesquisa Cognitiva sobre Elasticsearch para seus projetos de aplicativo de pesquisa normalmente fazem isso porque tornamos uma tarefa fundamental mais fácil ou precisam da integração interna com outras tecnologias da Microsoft:

+ O Azure Pesquisa Cognitiva é um serviço de nuvem totalmente gerenciado com contratos de nível de serviço (SLA) de 99,9% quando provisionado com redundância suficiente (2 réplicas para acesso de leitura, três réplicas para leitura e gravação).
+ Os [Processadores de linguagem natural](https://docs.microsoft.com/rest/api/searchservice/language-support) da Microsoft oferecem análise linguística de ponta.  
+ Os [indexadores do azure pesquisa cognitiva](search-indexer-overview.md) podem rastrear uma variedade de fontes de dados do Azure para indexação inicial e incremental.
+ Se você precisa de uma resposta rápida para flutuações na consulta ou nos volumes de indexação, pode usar [controles deslizantes](search-manage.md#scale-up-or-down) no portal do Azure ou executar um [script do PowerShell](search-manage-powershell.md), ignorando o gerenciamento de fragmento diretamente.  
+ Os [Recursos de pontuação e ajuste](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) fornecem os meios para influenciar a pontuação de classificações de pesquisa além do que o mecanismo de pesquisa sozinho pode fornecer.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Posso pausar o serviço de Pesquisa Cognitiva do Azure e parar a cobrança?

Não é possível pausar o serviço. Recursos de computação e armazenamento são alocados para seu uso exclusivo quando o serviço é criado. Não é possível liberar e recuperar os recursos sob demanda.

## <a name="indexing-operations"></a>Operações de indexação

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Mover, fazer backup e restaurar índices ou instantâneos de índice?

Durante a fase de desenvolvimento, talvez você queira mover o índice entre os serviços de pesquisa. Por exemplo, você pode usar um tipo de preço básico ou gratuito para desenvolver seu índice e, em seguida, desejar movê-lo para a camada Standard ou superior para uso em produção. 

Ou, talvez você queira fazer backup de um instantâneo de índice em arquivos que podem ser usados para restaurá-lo mais tarde. 

Você pode fazer todas essas coisas com o código de exemplo **index-backup-restore** neste [repositório de exemplo do Azure pesquisa cognitiva .net](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

Você também pode [obter uma definição de índice](https://docs.microsoft.com/rest/api/searchservice/get-index) a qualquer momento usando a API REST do Azure pesquisa cognitiva.

No momento, não há nenhum recurso interno de extração de índice, instantâneo ou restauração de backup no portal do Azure. No entanto, estamos pensando em Adicionar a funcionalidade de backup e restauração em uma versão futura. Se você quiser mostrar o suporte para esse recurso, converta um voto na [voz do usuário](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Posso restaurar meu índice ou serviço depois que ele for excluído?

Não, se você excluir um serviço ou índice de Pesquisa Cognitiva do Azure, ele não poderá ser recuperado. Quando você exclui um serviço de Pesquisa Cognitiva do Azure, todos os índices no serviço são excluídos permanentemente. Se você excluir um grupo de recursos do Azure que contém um ou mais serviços de Pesquisa Cognitiva do Azure, todos os serviços serão excluídos permanentemente.  

Recriar recursos como índices, indexadores, fontes de dados e habilidades exige que você os recrie a partir do código. 

Para recriar um índice, você deve reindexar dados de fontes externas. Por esse motivo, é recomendável que você retenha uma cópia mestra ou um backup dos dados originais em outro armazenamento de dados, como o Azure SQL Database ou o Cosmos DB.

Como alternativa, você pode usar o código de exemplo **index-backup-restore** neste [repositório de exemplo do Azure pesquisa cognitiva .net](https://github.com/Azure-Samples/azure-search-dotnet-samples) para fazer backup de uma definição de índice e de um instantâneo de índice para uma série de arquivos JSON. Posteriormente, você pode usar a ferramenta e os arquivos para restaurar o índice, se necessário.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Posso indexar a partir das réplicas de banco de dados SQL? (Aplica-se a [indexadores de Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers).)

Não há nenhuma restrição quanto ao uso de réplicas primárias ou secundárias como fonte de dados na criação de um índice do zero. No entanto, a atualização de um índice com atualizações incrementais (com base em registros modificados) exige a réplica primária. Esse requisito vem do Banco de Dados SQL, que garante o controle de alterações somente em réplicas primárias. Se você tentar usar réplicas secundárias para uma carga de trabalho de atualização do índice, não há nenhuma garantia de que obterá todos os dados.

## <a name="search-operations"></a>Operações de pesquisa

### <a name="can-i-search-across-multiple-indexes"></a>Posso pesquisar em vários índices?

Não, essa operação não tem suporte. A pesquisa sempre está no escopo para um único índice.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Posso restringir o acesso ao índice de pesquisa por identidade do usuário?

Você pode implementar [filtros de segurança](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) com filtro `search.in()`. O filtro integra-se bem com [serviços de gerenciamento de identidade como o Microsoft Azure Active Directory (AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) para selecionar resultados de pesquisa com base em associação de grupos de usuários definidas.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Por que não aparece correspondência em termos que sei que são válidos?

O mais comum é não saber que cada tipo de consulta dá suporte a níveis de análise linguística e a comportamentos de pesquisa diferentes. A pesquisa de texto completo, que é a carga de trabalho predominante, inclui uma fase de análise de linguagem que divide os termos para formulários raiz. Esse aspecto da análise de consulta amplia as possíveis correspondências, pois o termo indexado corresponde a um número maior de variantes.

Consultas difusas, de regex e com caractere curinga, no entanto, não são analisadas como consultas de termos ou frases comuns e podem levar a uma recuperação ruim se a consulta não coincidir com o formulário analisado da palavra no índice de pesquisa. Para obter mais informações sobre análises e análise de consultas, consulte [arquitetura de consulta](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Minhas pesquisas com caractere curinga estão lentas.

A maioria das consultas de pesquisa com caractere curinga, como prefixo, difusa e regex, são reconfiguradas internamente com termos correspondentes no índice de pesquisa. Esse processamento adicional de exame do índice de pesquisa aumenta a latência. Além disso, consultas de pesquisa amplas, como `a*`, por exemplo, que provavelmente serão reconfiguradas com muitos termos, podem ser muito lentas. Para pesquisas com muitos caracteres curingas, considere definir um [analisador personalizado](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Por que a classificação é uma pontuação igual ou constante de 1,0 para cada ocorrência?

Por padrão, os resultados da pesquisa são pontuados com base nas [propriedades estatísticas dos termos de correspondência](search-lucene-query-architecture.md#stage-4-scoring) e classificados do mais alto para o mais baixo do conjunto de resultados. No entanto, alguns tipos de consulta (caractere curinga, prefixo, regex) sempre contribuem com uma pontuação constante para a pontuação total do documento. Este comportamento ocorre por design. O Azure Pesquisa Cognitiva impõe uma pontuação constante para permitir que as correspondências encontradas por meio da expansão de consulta sejam incluídas nos resultados, sem afetar a classificação.

Por exemplo, suponha que uma entrada de "turnê*" em uma pesquisa com curinga produz correspondências em "turim", "turrão" e "turmalina". Dada a natureza desses resultados, não é possível inferir de forma razoável quais termos são mais valiosos do que outros. Por esse motivo, podemos ignorar as frequências dos termos ao pontuar resultados em consultas dos tipos caractere curinga, prefixo e regex. Os resultados da pesquisa com base em uma entrada parcial recebem uma pontuação constante para evitar a tendência de correspondências possivelmente inesperadas.

## <a name="design-patterns"></a>Padrões de design

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Qual é a melhor abordagem para implementar pesquisa localizada?

A maioria dos clientes escolhe campos dedicados em vez de uma coleção quando se trata de dar suporte a idiomas diferentes no mesmo índice. Os campos específicos de idioma possibilitam a atribuição de um analisador adequado. Por exemplo, a atribuição do Analisador de Francês da Microsoft a um campo que contém cadeias de caracteres em francês. Isso também simplifica a filtragem. Se você souber que uma consulta é iniciada em uma página com fr-fr na URL, poderá limitar os resultados da pesquisa para esse campo. Ou criar um [perfil de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) para dar ao campo mais peso relativo. O Azure Pesquisa Cognitiva dá suporte a mais de [50 analisadores de idioma](https://docs.microsoft.com/azure/search/search-language-support) para sua escolha.

## <a name="next-steps"></a>Próximas etapas

A sua pergunta é sobre a falta de um recurso ou funcionalidade? Solicite o recurso no [site do User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Consulte também

 [StackOverflow:  pesquisa cognitiva do Azure](https://stackoverflow.com/questions/tagged/azure-search)  
 [Como funciona a pesquisa de texto completo no Azure Pesquisa Cognitiva](search-lucene-query-architecture.md)  
 [O que é a Pesquisa Cognitiva do Azure?](search-what-is-azure-search.md)
