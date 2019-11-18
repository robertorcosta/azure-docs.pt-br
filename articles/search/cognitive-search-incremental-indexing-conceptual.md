---
title: Indexação incremental (versão prévia)
titleSuffix: Azure Cognitive Search
description: Configure seu pipeline de enriquecimento de ia para direcionar seus dados para a consistência eventual para lidar com quaisquer atualizações de habilidades, habilidades, indexadores ou fontes de dados. Este recurso está atualmente em visualização pública
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 76ab8784f04f3c67e4ea8062505931783048dea1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113592"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>O que é indexação incremental no Pesquisa Cognitiva do Azure?

> [!IMPORTANT] 
> A indexação incremental está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-versão prévia](search-api-preview.md) fornece esse recurso. Não há suporte para Portal ou SDK do .NET no momento.

A indexação incremental é um novo recurso do Azure Pesquisa Cognitiva que adiciona o cache e o estado ao conteúdo aprimorado em um determinado conhecimento, dando a você controle sobre o processamento e reprocessamento de etapas individuais em um pipeline de enriquecimento. Isso não apenas preserva seu investimento monetário em processamento, mas também torna um sistema mais eficiente. Quando estruturas e conteúdo são armazenados em cache, um indexador pode determinar quais habilidades foram alteradas e executar apenas aquelas que foram modificadas, bem como quaisquer habilidades dependentes de downstream. 

Com a indexação incremental, a versão atual do pipeline de enriquecimento faz a menor quantidade de trabalho para garantir a consistência de todos os documentos no índice. Para cenários em que você deseja controle total, você pode usar controles refinados para substituir os comportamentos esperados. Para obter mais informações sobre a configuração, consulte [Configurar a indexação incremental](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Cache do indexador

A indexação incremental adiciona um cache do indexador ao pipeline de enriquecimento. O indexador armazena em cache os resultados da quebra de documento e as saídas de cada habilidade para cada documento. Quando um conjunto de habilidades é atualizado, somente as habilidades alteradas ou downstream são executadas novamente. Os resultados atualizados são gravados no cache e o documento é atualizado no índice e no repositório de conhecimento.

Fisicamente, o cache é uma conta de armazenamento. Todos os índices em um serviço de pesquisa podem compartilhar a mesma conta de armazenamento para o cache do indexador. Cada indexador recebe um identificador de cache exclusivo e imutável.

### <a name="cache-configuration"></a>Configuração do cache

Você precisará definir a propriedade `cache` no indexador para iniciar o beneficiando a partir da indexação incremental. O exemplo a seguir ilustra um indexador com Caching habilitado. Partes específicas dessa configuração são descritas nas seções a seguir.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters":{}
}
```

Definir essa propriedade pela primeira vez em um indexador existente exigirá que você também o redefina, o que fará com que todos os documentos na fonte de dados sejam processados novamente. O objetivo da indexação incremental é tornar os documentos em seu índice consistentes a sua fonte de dados e a versão atual de seu conjunto de habilidades. A redefinição do índice é a primeira etapa rume essa consistência, pois elimina qualquer documento enriquecido pelas versões anteriores do conjunto de habilidades. O indexador precisa ser redefinido para começar com uma linha de base consistente.

### <a name="cache-lifecycle"></a>Ciclo de vida do cache

O ciclo de vida do cache é gerenciado pelo indexador. Se a propriedade `cache` no indexador for definida como NULL ou a cadeia de conexão for alterada, o cache existente será excluído. O ciclo de vida do cache também está vinculado ao ciclo de vida do indexador. Se um indexador for excluído, o cache associado também será.

### <a name="indexer-cache-mode"></a>Modo de cache do indexador

O cache do indexador pode operar no modo em que os dados são gravados apenas no cache ou no modo em que os dados são gravados no cache e usados para enriquecer os documentos novamente.  Você pode suspender temporariamente o enriquecimento incremental definindo a propriedade `enableReprocessing` no cache como `false` e, posteriormente, retomar o enriquecimento incremental e atingir a consistência eventual definindo-a como `true`. Esse controle é particularmente útil quando você deseja priorizar a indexação de novos documentos em vez da garantia da consistência em seu corpus de documentos.

## <a name="change-detection-override"></a>Substituição de detecção de alteração

A indexação incremental oferece controle granular sobre todos os aspectos do pipeline de enriquecimento. Esse controle permite lidar com situações em que uma alteração pode ter consequências indesejadas. Por exemplo, editar um conjunto de habilidades e atualizar a URL de uma habilidade personalizada fará com que o indexador invalide os resultados em cache para essa habilidade. Quando está movendo apenas o ponto de extremidade para uma VM diferente ou reimplantando sua habilidade com uma nova chave de acesso, você não quer que nenhum documento existente seja processado novamente.

Para garantir que o indexador apenas faça os aprimoramentos que você precisa explicitamente, as atualizações para o conjunto de qualificações podem, opcionalmente, definir o parâmetro `disableCacheReprocessingChangeDetection` QueryString como `true`. Quando definido, esse parâmetro garante que apenas atualizações do conjunto de qualificações sejam confirmadas e que a alteração não seja avaliada quanto aos efeitos no corpus existente.

O exemplo a seguir ilustra o uso de QueryString. Faz parte da solicitação, com pares chave-valor separado por &. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>Invalidação de cache

O inverso desse cenário é aquele em que você pode implantar uma nova versão de uma habilidade personalizada, nada dentro do pipeline de enriquecimento é alterado, mas você precisa que uma habilidade específica seja invalidada e todos os documentos afetados sejam reprocessados para refletir os benefícios de um modelo atualizado. Nesses casos, você pode chamar a operação de invalidação de habilidades no conjunto de habilidades. A API de redefinição de habilidades aceita uma solicitação POST com a lista de saídas da habilidade que devem ser invalidadas no cache. Para obter mais informações sobre a API de redefinição de habilidades, consulte [Redefinir indexador (API REST de pesquisa)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="bi-directional-change-detection"></a>Detecção bidirecional de alterações

Os indexadores não apenas avançam e processam novos documentos, mas agora são capazes de voltar e gerar documentos processados anteriormente a fim de manter a consistência. Com essa nova funcionalidade, é importante entender como alterações nos componentes de seu pipeline de enriquecimento resultam no trabalho do indexador. O indexador colocará o trabalho em fila para ser feito quando identificar uma alteração que seja invalidada ou inconsistente em relação ao conteúdo armazenado em cache.

### <a name="invalidating-changes"></a>Alterações que causam invalidações

Alterações que causam invalidação são raras, mas têm um efeito significativo sobre o estado de seu pipeline de enriquecimento. Uma alteração que causa invalidação faz com que o cache inteiro deixe de ser válido. Um exemplo de alteração que causa invalidação é aquela em que a fonte de dados é atualizada. Para cenários em que você sabe que a alteração não deve invalidar o cache, como girar a chave na conta de armazenamento, o parâmetro `ignoreResetRequirement` QueryString deve ser definido como `true` na operação de atualização do recurso específico para garantir que a operação seja Não rejeitado.

Esta é a lista completa de alterações que invalidarão seu cache:

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

### <a name="inconsistent-changes"></a>Alterações divergentes

Um exemplo de alteração divergente é uma atualização de seu conjunto de habilidades que modifica uma habilidade. A modificação pode tornar uma parte do cache divergente. O indexador identificará o trabalho para tornar as coisas consistentes novamente.  

A lista completa de alterações que causam divergência do cache:

* A habilidade no conjunto de habilidades tem um tipo diferente. O tipo odata da habilidade é atualizado
* Atualização de parâmetros específicos da habilidade, por exemplo, a URL, padrões ou outros parâmetros
* Alterações nas saídas da habilidade, a habilidade retorna saídas adicionais ou diferentes
* Atualizações na habilidade que levam a ancestrais diferentes, o encadeamento de habilidades mudou, ou seja, entradas de habilidades
* Qualquer invalidação de habilidade upstream, se uma habilidade que fornece uma entrada para essa habilidade for atualizada
* Atualizações no local de projeção do repositório de conhecimento resultam na reprojeção de documentos
* Alterações nas projeções do repositório de conhecimento resultam na reprojeção de documentos
* Alterações nos mapeamentos do campo de saída de um indexador resultam na reprojeção de documentos para o índice

## <a name="rest-api-reference-for-incremental-indexing"></a>Referência da API REST para indexação incremental

REST `api-version=2019-05-06-Preview` fornece as APIs para indexação incremental, com adições a indexadores, habilidades e fontes de dados. A documentação de referência não inclui atualmente essas adições. A seção a seguir descreve as alterações de API.

### <a name="indexers"></a>Indexadores

[Criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer) e [Atualizar indexador](https://docs.microsoft.com/rest/api/searchservice/update-indexer) agora vão expor novas propriedades relacionadas ao cache:

* `StorageAccountConnectionString`: a cadeia de conexão para a conta de armazenamento que será usada para armazenar em cache os resultados intermediários.

* `CacheId`: o `cacheId` é o identificador do contêiner dentro da conta de armazenamento de `annotationCache` que será usada como o cache para esse indexador. Esse cache será exclusivo para esse indexador e, se o indexador for excluído e recriado com o mesmo nome, o `cacheId` será regenerado. O `cacheId` não pode ser definido, ele sempre é gerado pelo serviço.

* `EnableReprocessing`: definido como `true` por padrão, quando definido como `false`, os documentos continuarão a ser gravados no cache, mas nenhum documento existente será reprocessado com base nos dados do cache.

Alguns indexadores (por meio de [fontes de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)) recuperam dados por meio de consultas. Para consultas que recuperam dados, os indexadores também oferecerão suporte a um novo parâmetro de cadeia de caracteres de consulta: `ignoreResetRequirement` deve ser definido como `true` quando a ação de atualização não deve invalidar o cache.

### <a name="skillsets"></a>Conjuntos de habilidades

Os conjuntos de habilidades não terão suporte para nenhuma nova operação, mas terão suporte para um novo parâmetro de querystring: `disableCacheReprocessingChangeDetection` deve ser definido como `true` quando você não quer atualizações de documentos existentes com base na ação atual.

### <a name="datasources"></a>Fontes de dados

As fontes de dados não dão suporte a nenhuma operação nova, mas dão suporte a um novo parâmetro querystring: `ignoreResetRequirement` deve ser definido como `true` quando a ação de atualização não deve invalidar o cache.

## <a name="best-practices"></a>Práticas recomendadas

A abordagem recomendada para usar a indexação incremental é configurá-la definindo a propriedade de cache em um novo indexador ou redefinir um indexador existente e definir a propriedade de cache.

Use o `ignoreResetRequirement` com moderação, pois isso pode levar à inconsistência indesejada em seus dados que não serão detectados com facilidade.

## <a name="takeaways"></a>Observações

A indexação incremental é um recurso poderoso que estende o controle de alterações da fonte de dados para todos os aspectos do pipeline de enriquecimento, incluindo a fonte de dados, a versão atual do seu conjunto de qualificações e o indexador. À medida que suas habilidades, conjuntos de habilidades ou enriquecimento evoluem, o pipeline de enriquecimento garante que o mínimo de trabalho possível seja feito e, ao mesmo tempo, leva seus documentos à consistência eventual.

## <a name="next-steps"></a>Próximas etapas

Comece a usar a indexação incremental adicionando um cache a um indexador existente ou adicione o cache ao definir um novo indexador.

> [!div class="nextstepaction"]
> [Configurar a indexação incremental](search-howto-incremental-index.md)
