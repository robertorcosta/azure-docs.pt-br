---
title: Enriquecimento incremental (visualização)
titleSuffix: Azure Cognitive Search
description: Cache conteúdo intermediário e alterações incrementais do pipeline de enriquecimento de IA no Azure Storage para preservar investimentos em documentos processados existentes. Esse recurso está atualmente em visualização pública.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024136"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Introdução ao enriquecimento incremental e cache na Pesquisa Cognitiva do Azure

> [!IMPORTANT] 
> O enriquecimento incremental está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-versão prévia](search-api-preview.md) fornece esse recurso. Não há suporte a portal ou .NET SDK no momento.

O enriquecimento incremental adiciona cache e statefulness a um pipeline de enriquecimento, preservando seu investimento na produção existente, ao mesmo tempo em que altera apenas os documentos impactados por uma modificação particular. Isso não só preserva seu investimento monetário no processamento (em particular, OCR e processamento de imagem), mas também torna um sistema mais eficiente. Quando estruturas e conteúdo são armazenados em cache, um indexador pode determinar quais habilidades mudaram e executar apenas aqueles que foram modificados, bem como quaisquer habilidades dependentes a jusante. 

## <a name="indexer-cache"></a>Cache do indexador

O enriquecimento incremental adiciona um cache ao gasoduto de enriquecimento. O indexador armazena os resultados da quebra do documento mais as saídas de cada habilidade para cada documento. Quando um conjunto de habilidades é atualizado, somente as habilidades alteradas ou downstream são executadas novamente. Os resultados atualizados são escritos no cache e o documento é atualizado no índice de pesquisa ou no armazenamento de conhecimento.

Fisicamente, o cache é armazenado em um recipiente blob em sua conta de armazenamento Azure. O cache também usa o armazenamento de tabela para um registro interno de atualizações de processamento. Todos os índices em um serviço de pesquisa podem compartilhar a mesma conta de armazenamento para o cache do indexador. Cada indexador é atribuído um identificador de cache único e imutável ao contêiner que está usando.

## <a name="cache-configuration"></a>Configuração do cache

Você precisará definir a `cache` propriedade no indexador para começar a se beneficiar do enriquecimento incremental. O exemplo a seguir ilustra um indexador com cache ativado. Partes específicas desta configuração são descritas em seções a seguir. Para obter mais informações, consulte [Configurar o enriquecimento incremental](search-howto-incremental-index.md).

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

A configuração dessa propriedade em um indexador existente exigirá que você reset e reexecute o indexador, o que resultará em todos os documentos em sua fonte de dados sendo processados novamente. Esta etapa é necessária para eliminar quaisquer documentos enriquecidos por versões anteriores do skillset. 

## <a name="cache-management"></a>Gerenciamento de cache

O ciclo de vida do cache é gerenciado pelo indexador. Se `cache` a propriedade no indexador estiver definida como nula ou a seqüência de conexões for alterada, o cache existente será excluído na próxima execução do indexador. O ciclo de vida do cache também está vinculado ao ciclo de vida do indexador. Se um indexador for excluído, o cache associado também será.

Embora o enriquecimento incremental seja projetado para detectar e responder a alterações sem intervenção de sua parte, existem parâmetros que você pode usar para substituir comportamentos padrão:

+ Priorizar novos documentos
+ Verificações de habilidades de bypass
+ Verificações de origem de dados de bypass
+ Avaliação de skillset de força

### <a name="prioritize-new-documents"></a>Priorizar novos documentos

Defina `enableReprocessing` a propriedade para controlar o processamento sobre documentos de entrada já representados no cache. Quando `true` (padrão), os documentos que já estão no cache são reprocessados quando você executa o indexador, assumindo que sua atualização de habilidade afeta esse doc. 

Quando `false`, os documentos existentes não são reprocessados, priorizando efetivamente conteúdo novo e de entrada sobre o conteúdo existente. Você só `enableReprocessing` deve `false` definir em uma base temporária. Para garantir a consistência `enableReprocessing` em `true` todo o corpus, deve ser a maior parte do tempo, garantindo que todos os documentos, tanto novos quanto existentes, sejam válidos de acordo com a definição atual de skillset.

### <a name="bypass-skillset-evaluation"></a>Avaliação de skillset de bypass

Modificar um skillset e reprocessamento desse skillset normalmente andam lado a lado. No entanto, algumas alterações em um skillset não devem resultar em reprocessamento (por exemplo, implantar uma habilidade personalizada em um novo local ou com uma nova chave de acesso). Provavelmente, são modificações periféricas que não têm impacto genuíno na substância do próprio skillset. 

Se você sabe que uma mudança no skillset é realmente superficial, `disableCacheReprocessingChangeDetection` você `true`deve substituir a avaliação de skillset definindo o parâmetro para :

1. Chamada Update Skillset e modifique a definição de skillset.
1. Anexar o `disableCacheReprocessingChangeDetection=true` parâmetro na solicitação.
1. Envie a mudança.

A definição deste parâmetro garante que apenas as atualizações para a definição de skillset sejam cometidas e que a alteração não seja avaliada para efeitos no corpus existente.

O exemplo a seguir mostra uma solicitação Update Skillset com o parâmetro:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Verificações de validação de fontes de dados de bypass

A maioria das alterações em uma definição de fonte de dados invalidará o cache. No entanto, para cenários em que você sabe que uma alteração não deve invalidar o cache`ignoreResetRequirement` - como alterar uma seqüência de conexões ou girar a chave na conta de armazenamento - anexar o parâmetro na atualização de origem de dados. Definir este parâmetro `true` para permitir que o compromisso passe, sem desencadear uma condição de reset que resultaria em todos os objetos sendo reconstruídos e povoados do zero.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Avaliação de skillset de força

O objetivo do cache é evitar o processamento desnecessário, mas suponha que você faça uma alteração em uma habilidade que o indexador não detecta (por exemplo, mudando algo em código externo, como uma habilidade personalizada).

Neste caso, você pode usar as [Habilidades de Reset](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) para forçar o reprocessamento de uma determinada habilidade, incluindo quaisquer habilidades a jusante que tenham uma dependência da saída dessa habilidade. Esta API aceita uma solicitação POST com uma lista de habilidades que devem ser invalidadas e marcadas para reprocessamento. Depois de Redefinir Skills, execute o indexador para invocar o pipeline.

## <a name="change-detection"></a>Detecção de alteração

Uma vez que você habilita um cache, o indexador avalia alterações na composição do pipeline para determinar qual conteúdo pode ser reutilizado e qual precisa ser reprocessado. Esta seção enumera alterações que invalidam o cache imediatamente, seguidas de alterações que desencadeiam o processamento incremental. 

### <a name="changes-that-invalidate-the-cache"></a>Alterações que invalidam o cache

Uma alteração que causa invalidação faz com que o cache inteiro deixe de ser válido. Um exemplo de alteração que causa invalidação é aquela em que a fonte de dados é atualizada. Esta é a lista completa de alterações que invalidarão seu cache:

* Alteração do tipo de fonte de dados
* Alteração do contêiner de fonte de dados
* Credenciais da fonte de dados
* Política de detecção de alteração da fonte de dados
* Política de detecção de exclusão da fonte de dados
* Mapeamentos de campo do indexador
* Parâmetros do indexador
    * Modo de análise
    * Extensões de nome de arquivo excluídas
    * Extensões de nome de arquivo indexadas
    * Indexar metadados de armazenamento somente para documentos excessivamente grandes
    * Cabeçalhos de texto delimitado
    * Delimitador de texto delimitado
    * Raiz do documento
    * Ação de imagem (alterações de como as imagens são extraídas)

### <a name="changes-that-trigger-incremental-processing"></a>Mudanças que desencadeiam o processamento incremental

O processamento incremental avalia a definição de suas habilidades e determina quais habilidades executar, atualizando seletivamente as partes afetadas da árvore de documentos. Aqui está a lista completa de mudanças que resultam em enriquecimento incremental:

* A habilidade no conjunto de habilidades tem um tipo diferente. O tipo odata da habilidade é atualizado
* Atualização de parâmetros específicos da habilidade, por exemplo, a URL, padrões ou outros parâmetros
* Alterações nas saídas da habilidade, a habilidade retorna saídas adicionais ou diferentes
* Atualizações na habilidade que levam a ancestrais diferentes, o encadeamento de habilidades mudou, ou seja, entradas de habilidades
* Qualquer invalidação de habilidade upstream, se uma habilidade que fornece uma entrada para essa habilidade for atualizada
* Atualizações no local de projeção do repositório de conhecimento resultam na reprojeção de documentos
* Alterações nas projeções do repositório de conhecimento resultam na reprojeção de documentos
* Alterações nos mapeamentos do campo de saída de um indexador resultam na reprojeção de documentos para o índice

## <a name="api-reference"></a>Reference API

A versão `2019-05-06-Preview` da API REST fornece enriquecimento incremental através de propriedades adicionais em indexadores, skillsets e fontes de dados. Além da documentação de referência, consulte [Configurar cache para enriquecimento incremental](search-howto-incremental-index.md) para obter detalhes sobre como chamar as APIs.

+ [Criar indexador (api-versão=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Indexador de atualização (api-versão=2019-05-06-Visualização)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Update Skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (Novo parâmetro URI na solicitação)

+ [Redefinir habilidades (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Indexadores de banco de dados (Azure SQL, Cosmos DB). Alguns indexadores recuperam dados através de consultas. Para consultas que recuperam dados, [a Update Data Source](https://docs.microsoft.com/rest/api/searchservice/update-data-source) suporta um novo parâmetro em uma solicitação **ignoreResetRequirement**, que deve ser definido para `true` quando sua ação de atualização não deve invalidar o cache. 

  Use **ignoreResetRequirement** com moderação, pois pode levar a inconsistências não intencionais em seus dados que não serão detectadas facilmente.

## <a name="next-steps"></a>Próximas etapas

O enriquecimento incremental é um recurso poderoso que estende o rastreamento de alterações a habilidades e enriquecimento de IA. O enriquecimento Incremental permite a reutilização do conteúdo processado existente à medida que você itera sobre o design de skillset.

Como próximo passo, habilite o cache em um indexador existente ou adicione um cache ao definir um novo indexador.

> [!div class="nextstepaction"]
> [Configurar cache para enriquecimento incremental](search-howto-incremental-index.md)
