---
title: Limites – QnA Maker
description: O QnA Maker tem limites de meta para partes do serviço e da base de dados de conhecimento. É importante manter sua base de dados de conhecimento dentro desses limites para testar e publicar.
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 6375a6c6efc0c7016d9947e04e9479385aa80af5
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273346"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limites e limites de base de dados de conhecimento do QnA Maker

Os limites de QnA Maker fornecidos abaixo são uma combinação dos [limites do tipo de preço do Azure pesquisa cognitiva](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) e os limites do tipo de preço do [QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Você precisa saber os dois conjuntos de limites para entender quantas bases de dados de conhecimento você pode criar por recurso e o tamanho de cada base de dados de conhecimento.

## <a name="knowledge-bases"></a>Bases de dados de conhecimento

O número máximo de bases de dados de conhecimento baseia-se nos [limites da camada de pesquisa cognitiva do Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Camada de Pesquisa Cognitiva do Azure** | **Gratuito** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo de bases de dados de conhecimento publicadas permitidas|2|14|49|199|199|2,999|

 Por exemplo, se a camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicada). O décimo quinto índice, `testkb`, é usado para todas as bases de dados de conhecimento para criação e teste.

## <a name="extraction-limits"></a>Limites de extração

### <a name="file-naming-constraints"></a>Restrições de nomenclatura de arquivo

Os nomes de arquivo não podem incluir os seguintes caracteres:

|Não usar caractere|
|--|
|Aspas simples `'`|
|Aspas duplas `"`|

### <a name="maximum-file-size"></a>Tamanho máximo do arquivo

|Formato|Tamanho máximo do arquivo (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Número máximo de arquivos

O número máximo de arquivos que podem ser extraídos e o tamanho máximo do arquivo baseia-se nos limites do seu **[QnA Maker tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Número máximo de links profundos da URL

O número máximo de links profundos que podem ser rastreados para extração de QnAs de uma página de URL é **20**.

## <a name="metadata-limits"></a>Limites de metadados

Os metadados são apresentados como um par chave com base em texto: valor, como `product:windows 10`. Ele é armazenado e comparado em letras minúsculas.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Pelo Azure Pesquisa Cognitiva tipo de preço

O número máximo de campos de metadados por base de dados de conhecimento baseia-se nos **[limites da camada de pesquisa cognitiva do Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Camada de Pesquisa Cognitiva do Azure** | **Gratuito** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Máximo de campos de metadados por serviço QnA Maker (entre todas as bases de dados de conhecimento)|1\.000|100*|1\.000|1\.000|1\.000|1\.000|

### <a name="by-name-and-value"></a>Por nome e valor

O comprimento e os caracteres aceitáveis para o nome e o valor dos metadados são listados na tabela a seguir.

|Item|Caracteres permitidos|Correspondência de padrão de Regex|Máximo de caracteres|
|--|--|--|--|
|Nome (chave)|Permitem<br>alfanumérico (letras e dígitos)<br>`_` (sublinhado)<br> Não deve conter espaços.|`^[a-zA-Z0-9_]+$`|100|
|{1&gt;Valor&lt;1}|Permite tudo, exceto<br>`:` (dois-pontos)<br>`|` (barra vertical)<br>Apenas um valor é permitido.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limites de conteúdo da Base de Dados de Conhecimento
Limites gerais sobre o conteúdo na base de dados de conhecimento:
* Comprimento do texto de resposta: 25.000
* Comprimento do texto da pergunta: 1.000
* Tamanho do texto de chave/valor dos metadados: 100
* Caracteres com suporte para nome de metadados: alfabetos, dígitos e `_`
* Caracteres com suporte para o valor de metadados: todos, exceto `:` e `|`
* Tamanho do nome do arquivo: 200
* Formatos de arquivo com suporte: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Número máximo de perguntas alternativas: 300
* Número máximo de pares de resposta de pergunta: depende da **[camada de pesquisa cognitiva do Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** escolhida. Um par de perguntas e respostas é mapeado para um documento no índice de Pesquisa Cognitiva do Azure.
* URL/página HTML: 1 milhão caracteres

## <a name="create-knowledge-base-call-limits"></a>Criar limites de chamada da base de dados de conhecimento:
Eles representam os limites de cada ação de criação da base de dados de conhecimento; ou seja, clicar em *Criar KB* ou chamar a API CreateKnowledgeBase.
* Número máximo de perguntas alternativas por resposta: 300
* Número máximo de URLs: 10
* Número máximo de arquivos: 10

## <a name="update-knowledge-base-call-limits"></a>Atualizar limites de chamada da base de dados de conhecimento
Eles representam os limites de cada ação de atualização; ou seja, clique em *Salvar e treinar* ou chame a API UpdateKnowledgeBase.
* Tamanho de cada nome de origem: 300
* Número máximo de perguntas alternativas adicionadas ou excluídas: 300
* Número máximo de campos de metadados adicionados ou excluídos: 10
* Número máximo de URLs que podem ser atualizadas: 5

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Saiba quando e como alterar os [tipos de preço de serviço](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku).
