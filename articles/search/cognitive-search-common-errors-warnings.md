---
title: Erros e avisos do indexador
titleSuffix: Azure Cognitive Search
description: Este artigo fornece informações e soluções para erros e avisos comuns que você pode encontrar durante o enriquecimento de IA na Pesquisa Cognitiva do Azure.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72bf08dce36d857c1fe91bbe9806336dfa185f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671985"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Solução de problemas erros e avisos comuns de indexadores no Azure Cognitive Search

Este artigo fornece informações e soluções para erros e avisos comuns que você pode encontrar durante a indexação e o enriquecimento de IA na Pesquisa Cognitiva do Azure.

A indexação pára quando a contagem de erros excede ['maxFailedItems'](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Se você quiser que os indexadores ignorem esses erros `maxFailedItems` (e pulem "documentos com falha"), considere atualizar o e `maxFailedItemsPerBatch` como descrito [aqui](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Cada documento com falha junto com sua chave de documento (quando disponível) aparecerá como um erro no status de execução do indexador. Você pode utilizar a [api de índice](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para carregar manualmente os documentos em um ponto posterior se você tiver definido o indexador para tolerar falhas.

As informações de erro neste artigo podem ajudá-lo a resolver erros, permitindo que a indexação continue.

Os avisos não param a indexação, mas indicam condições que podem resultar em resultados inesperados. Se você agir ou não depende dos dados e do seu cenário.

Começando com a `2019-05-06`versão da API, os erros e avisos do indexador de nível de item são estruturados para fornecer maior clareza sobre causas e próximos passos. Elas contêm as seguintes propriedades:

| Propriedade | Descrição | Exemplo |
| --- | --- | --- |
| chave | O documento de identificação do documento impactado pelo erro ou aviso. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | O nome da operação descreve onde ocorreu o erro ou aviso. Isso é gerado pela seguinte estrutura: [categoria]. [subcategoria]. [resourceType]. [resourceName] | DocumentExtraction.azureblob.myBlobContainerName Enrique.WebApiSkill.mySkillName Projection.SearchIndex.OutputFieldMapping.myOutputFieldName Projection.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| message | Uma descrição de alto nível do erro ou aviso. | Não foi possível executar a habilidade porque a solicitação de Api da Web falhou. |
| detalhes | Quaisquer detalhes adicionais que possam ser úteis para diagnosticar o problema, como a resposta do WebApi, se a execução de uma habilidade personalizada falhou. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 fonte,`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` Func ... resto de traço de pilha... |
| documentaçãoLink | Um link para documentação relevante com informações detalhadas para depurar e resolver o problema. Este link muitas vezes apontará para uma das seções abaixo nesta página. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Erro: Não foi possível ler o documento

O indexador não conseguiu ler o documento a partir da fonte de dados. Isso pode acontecer devido a:

| Motivo | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Tipos de campo inconsistentes em diferentes documentos | "O tipo de valor tem uma incompatibilidade com o tipo de coluna. Não poderia `'{47.6,-122.1}'` armazenar na coluna de autores.  O tipo esperado é JArray."  "Erro de conversão de dados tipo nvarchar para flutuar."  "A conversão falhou ao converter o valor nvarchar '12 meses' para int do tipo de dados."  "Erro de estouro aritmético ao converter a expressão em dados tipo int". | Certifique-se de que o tipo de cada campo é o mesmo em diferentes documentos. Por exemplo, se `'startTime'` o primeiro campo de documento for um DateTime e no segundo documento for uma seqüência, esse erro será atingido. |
| erros do serviço subjacente da fonte de dados | (da Cosmos DB)`{"Errors":["Request rate is large"]}` | Verifique sua instância de armazenamento para garantir que ela esteja saudável. Talvez seja necessário ajustar o dimensionamento/particionamento. |
| questões transitórias | ocorreu um erro de nível de transporte ao receber os resultados do servidor. (provedor: Provedor TCP, erro: 0 - Uma conexão existente foi fechada à força pelo host remoto | Ocasionalmente, há problemas inesperados de conectividade. Tente executar o documento através de seu indexador novamente mais tarde. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Erro: Não foi possível extrair conteúdo ou metadados do seu documento
O indexador com uma fonte de dados Blob não conseguiu extrair o conteúdo ou os metadados do documento (por exemplo, um arquivo PDF). Isso pode acontecer devido a:

| Motivo | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| bolha é sobre o limite de tamanho | O `'150441598'` documento é bytes, que `'134217728'` excede o tamanho máximo de bytes para extração de documento para o seu nível de serviço atual. | [erros de indexação de bolhas](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob tem tipo de conteúdo sem suporte | Documento tem tipo de conteúdo não suportado`'image/png'` | [erros de indexação de bolhas](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob é criptografado | O documento não pôde ser processado - ele pode ser criptografado ou protegido por senha. | Você pode pular a bolha com [as configurações de bolha](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed). |
| questões transitórias | "Erro de processamento de blob: O pedido foi abortado: O pedido foi cancelado." "Documento esgotado durante o processamento." | Ocasionalmente, há problemas inesperados de conectividade. Tente executar o documento através de seu indexador novamente mais tarde. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Erro: Não foi possível analisar o documento
O indexador leu o documento a partir da fonte de dados, mas houve um problema na conversão do conteúdo do documento no esquema de mapeamento de campo especificado. Isso pode acontecer devido a:

| Motivo | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| A chave do documento está faltando | A chave do documento não pode estar faltando ou vazia | Certifique-se de que todos os documentos tenham chaves de documento válidas |
| A chave do documento é inválida | A chave de documento não pode ser maior que 1024 caracteres | Modifique a chave do documento para atender aos requisitos de validação. |
| Não foi possível aplicar o mapeamento de campo em um campo | Não foi possível `'functionName'` aplicar `'fieldName'`a função de mapeamento no campo . A matriz não pode ser nula. Nome do parâmetro: bytes | Verifique duas vezes os [mapeamentos de campo definidos](search-indexer-field-mappings.md) no indexador e compare com os dados do campo especificado do documento com falha. Pode ser necessário modificar os mapeamentos de campo ou os dados do documento. |
| Não foi possível ler o valor do campo | Não foi possível ler `'fieldName'` o `'fieldIndex'`valor da coluna no índice . ocorreu um erro de nível de transporte ao receber os resultados do servidor. (provedor: Provedor TCP, erro: 0 – Uma conexão existente foi fechada forçadamente pelo host remoto.) | Esses erros são normalmente devido a problemas inesperados de conectividade com o serviço subjacente da fonte de dados. Tente executar o documento através de seu indexador novamente mais tarde. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Erro: Não foi possível executar habilidade
O indexador não era capaz de executar uma habilidade no skillset.

| Motivo | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Problemas transitórios de conectividade | Ocorreu um erro transitório. Tente novamente mais tarde. | Ocasionalmente, há problemas inesperados de conectividade. Tente executar o documento através de seu indexador novamente mais tarde. |
| Bug potencial do produto | Erro inesperado. | Isso indica uma classe desconhecida de falha e pode significar que há um bug do produto. Por favor, registre um [bilhete de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para obter ajuda. |
| Uma habilidade encontrou um erro durante a execução | (Da Habilidade de Mesclagem) Um ou mais valores de compensação eram inválidos e não podiam ser analisados. Os itens foram inseridos no final do texto | Use as informações na mensagem de erro para corrigir o problema. Esse tipo de falha exigirá ação para ser resolvida. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Erro: Não foi possível executar a habilidade porque a solicitação de API da Web falhou
A execução de habilidades falhou porque a chamada para a API da Web falhou. Normalmente, essa classe de falha ocorre quando as habilidades personalizadas são usadas, nesse caso você precisará depurar seu código personalizado para resolver o problema. Se, em vez disso, a falha for de uma habilidade incorporada, consulte a mensagem de erro para obter ajuda na correção do problema.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Erro: Não foi possível executar a habilidade porque a resposta de habilidade da API da Web é inválida
A execução de habilidades falhou porque a chamada para a API da Web retornou uma resposta inválida. Normalmente, essa classe de falha ocorre quando as habilidades personalizadas são usadas, nesse caso você precisará depurar seu código personalizado para resolver o problema. Se, em vez disso, a falha for de uma habilidade incorporada, por favor, registre um [bilhete de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para obter assistência.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Erro: A habilidade não foi executada dentro do prazo
Existem dois casos em que você pode encontrar esta mensagem de erro, cada um dos quais deve ser tratado de forma diferente. Por favor, siga as instruções abaixo, dependendo de qual habilidade devolveu este erro para você.

### <a name="built-in-cognitive-service-skills"></a>Habilidades incorporadas de Serviço Cognitivo
Muitas das habilidades cognitivas incorporadas, como detecção de linguagem, reconhecimento de entidades ou OCR, são apoiadas por um ponto final da API do Serviço Cognitivo. Às vezes, há problemas transitórios com esses pontos finais e uma solicitação vai acabar. Para questões transitórias, não há remédio a não ser esperar e tentar novamente. Como atenuação, considere configurar seu indexador para [executar em um cronograma](search-howto-schedule-indexers.md). A indexação programada retoma de onde parou. Supondo que os problemas transitórios sejam resolvidos, a indexação e o processamento de habilidades cognitivas devem ser capazes de continuar na próxima execução programada.

Se você continuar a ver esse erro no mesmo documento para uma habilidade cognitiva incorporada, por favor, registre um [bilhete de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para obter assistência, pois isso não é esperado.

### <a name="custom-skills"></a>Habilidades personalizadas
Se você encontrar um erro de tempo com uma habilidade personalizada que você criou, há algumas coisas que você pode tentar. Primeiro, revise sua habilidade personalizada e certifique-se de que ela não está ficando presa em um loop infinito e que está retornando um resultado de forma consistente. Uma vez confirmado que é esse o caso, determine qual é o tempo de execução de sua habilidade. Se você não definiu explicitamente `timeout` um valor na sua definição `timeout` de habilidade personalizada, então o padrão é de 30 segundos. Se 30 segundos não forem longos o suficiente `timeout` para sua habilidade ser executada, você pode especificar um valor mais alto na sua definição de habilidade personalizada. Aqui está um exemplo de uma definição de habilidade personalizada onde o tempo está definido para 90 segundos:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

O valor máximo que você `timeout` pode definir para o parâmetro é de 230 segundos.  Se sua habilidade personalizada não for capaz de executar consistentemente `batchSize` dentro de 230 segundos, você pode considerar reduzir a sua habilidade personalizada para que ela tenha menos documentos para processar dentro de uma única execução.  Se você já `batchSize` definiu o seu para 1, você precisará reescrever a habilidade para ser capaz de executar em menos de 230 segundos ou dividi-lo em múltiplas habilidades personalizadas para que o tempo de execução para qualquer habilidade personalizada seja de no máximo 230 segundos. Revise a [documentação de habilidade suél para](cognitive-search-custom-skill-web-api.md) obter mais informações.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Erro: Não`MergeOrUpload`poderia ' | '`Delete`' documento para o índice de pesquisa

O documento foi lido e processado, mas o indexador não pôde adicioná-lo ao índice de pesquisa. Isso pode acontecer devido a:

| Motivo | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Um campo contém um termo que é muito grande | Um termo em seu documento é maior que o [limite de 32 KB](search-limits-quotas-capacity.md#api-request-limits) | Você pode evitar essa restrição garantindo que o campo não esteja configurado como filtrante, facetable ou classificável.
| O documento é muito grande para ser indexado | Um documento é maior do que o [tamanho máximo da solicitação de API](search-limits-quotas-capacity.md#api-request-limits) | [Como indexar grandes conjuntos de dados](search-howto-large-index.md)
| Documento contém muitos objetos na coleção | Uma coleção em seu documento excede os [elementos máximos em todos os limites de coleções complexas](search-limits-quotas-capacity.md#index-limits) "O documento com chave `'1000052'` tem `'4303'` objetos em coleções (matrizes JSON). Na `'3000'` maioria dos objetos são permitidos estar em coleções em todo o documento. Por favor, remova objetos das coleções e tente indexar o documento novamente." | Recomendamos reduzir o tamanho da coleção complexa no documento para abaixo do limite e evitar alta utilização de armazenamento.
| Problemas de conexão ao índice de destino (que persiste após tentativas) porque o serviço está outra carga, como consulta ou indexação. | Falha ao estabelecer conexão para atualizar índice. O serviço de busca está carga pesada. | [Dimensione seu serviço de pesquisa](search-capacity-planning.md)
| O serviço de pesquisa está sendo corrigido para atualização de serviço, ou está no meio de uma reconfiguração de topologia. | Falha ao estabelecer conexão para atualizar índice. O serviço de pesquisa está atualmente em baixa/O serviço de pesquisa está passando por uma transição. | Configure o serviço com pelo menos 3 réplicas para 99,9% de disponibilidade por [documentação SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Falha no recurso de computação/rede subjacente (raro) | Falha ao estabelecer conexão para atualizar índice. Ocorreu uma falha desconhecida. | Configure indexadores para [executar em um cronograma](search-howto-schedule-indexers.md) para pegar de um estado com falha.
| Uma solicitação de indexação feita ao índice-alvo não foi reconhecida dentro de um período de tempo devido a problemas de rede. | Não foi possível estabelecer conexão com o índice de pesquisa em tempo hábil. | Configure indexadores para [executar em um cronograma](search-howto-schedule-indexers.md) para pegar de um estado com falha. Além disso, tente reduzir o tamanho do [lote](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) do indexador se essa condição de erro persistir.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Erro: Não foi possível indexar o documento porque alguns dos dados do documento não eram válidos

O documento foi lido e processado pelo indexador, mas devido a uma incompatibilidade na configuração dos campos de índice e dos dados extraídos e processados pelo indexador, não pôde ser adicionado ao índice de pesquisa. Isso pode acontecer devido a:

| Motivo | Detalhes/Exemplo
| --- | ---
| O tipo de dados dos campos extraídos pelo indexador é incompatível com o modelo de dados do campo de índice alvo correspondente. | O campo de dados '_dados_' no documento com a chave '888' tem um valor inválido 'do tipo 'Edm.String''. O tipo esperado era 'Collection(Edm.String)'. |
| Falha ao extrair nenhuma entidade JSON de um valor de string. | Não foi possível analisar o valor 'do tipo 'Edm.String'' de_dados_de campo ' como um objeto JSON. Erro:'Depois de analisar um valor, um personagem inesperado foi encontrado: ''. Caminho '_caminho_', linha 1, posição 3162.' |
| Falhou em extrair uma coleção de entidades JSON de um valor de string.  | Não foi possível analisar o valor 'do tipo 'Edm.String'' de_dados_de campo ' como uma matriz JSON. Erro:'Depois de analisar um valor, um personagem inesperado foi encontrado: ''. Caminho '[0]', linha 1, posição 27.' |
| Um tipo desconhecido foi descoberto no documento de origem. | Tipo desconhecido '_desconhecido_' não pode ser indexado |
| Uma notação incompatível para pontos de geografia foi utilizada no documento de origem. | Os literais de seqüência WKT POINT não são suportados. Por favor, use geojson ponto literals em vez |

Em todos esses casos, consulte [os tipos de dados suportados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) e o mapa do tipo de dados para os [indexadores](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) para garantir que você construa o esquema de índice corretamente e configure mapeamentos de [campo indexadores apropriados](search-indexer-field-mappings.md). A mensagem de erro incluirá detalhes que podem ajudar a rastrear a fonte da incompatibilidade.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Erro: A política integrada de rastreamento de alterações não pode ser usada porque a tabela tem uma chave primária composta

Isso se aplica a tabelas SQL, e geralmente acontece quando a chave é definida como uma chave composta ou, quando a tabela definiu um índice único de cluster (como em um índice SQL, não em um índice de Pesquisa Azure). A principal razão é que o atributo-chave é modificado para ser uma chave primária composta no caso de um [índice único agrupado](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15). Nesse caso, certifique-se de que sua tabela SQL não tenha um índice agrupado único ou que você mapeie o campo-chave para um campo que é garantido não ter valores duplicados.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Erro: Não foi possível processar o documento dentro do tempo de execução do indexador max

Esse erro ocorre quando o indexador não consegue concluir o processamento de um único documento da fonte de dados dentro do tempo de execução permitido. [O tempo máximo](search-limits-quotas-capacity.md#indexer-limits) de execução é menor quando as habilidades são usadas. Quando esse erro ocorrer, se você tiver maxFailedItems definidos para um valor diferente de 0, o indexador ignora o documento em corridas futuras para que a indexação possa progredir. Se você não puder pular qualquer documento, ou se estiver vendo esse erro de forma consistente, considere quebrar documentos em documentos menores para que o progresso parcial possa ser feito dentro de uma única execução do indexador.

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>Erro: Não foi possível projetar documento

Esse erro ocorre quando o indexador está tentando [projetar dados em um armazenamento de conhecimento](knowledge-store-projection-overview.md) e houve uma falha em nossa tentativa de fazê-lo.  Esta falha pode ser consistente e consertável ou pode ser uma falha passageira com a pia de saída de projeção que você pode precisar esperar e tentar novamente para resolver.  Aqui estão um conjunto de estados de falha conhecidos e possíveis resoluções.

| Motivo | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Não foi possível `'blobUri'` atualizar bolha de projeção no recipiente`'containerName'` |O contêiner especificado não existe. | O indexador verificará se o contêiner especificado foi criado anteriormente e o criará se necessário, mas só realizará essa verificação uma vez por execução do indexador. Este erro significa que algo excluiu o contêiner após esta etapa.  Para resolver esse erro, tente isso: deixe as informações da sua conta de armazenamento em paz, espere o indexador terminar e, em seguida, execute novamente o indexador. |
| Não foi possível `'blobUri'` atualizar bolha de projeção no recipiente`'containerName'` |Não é possível gravar dados na conexão de transporte: uma conexão existente foi fechada à força pelo host remoto. | Espera-se que isso seja uma falha passageira com o Armazenamento Azure e, portanto, deve ser resolvido reexecutando o indexador. Se você encontrar esse erro de forma consistente, por favor, registre um bilhete de [suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para que ele possa ser investigado mais adiante.  |
| Não foi `'projectionRow'` possível atualizar a linha na tabela`'tableName'` | O servidor está ocupado. | Espera-se que isso seja uma falha passageira com o Armazenamento Azure e, portanto, deve ser resolvido reexecutando o indexador. Se você encontrar esse erro de forma consistente, por favor, registre um bilhete de [suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para que ele possa ser investigado mais adiante.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>Aviso: A entrada de habilidade foi inválida
Faltava uma entrada para a habilidade, do tipo errado ou inválida. A mensagem de aviso indicará o impacto:
1) Não foi possível executar habilidade
2) Habilidade executada, mas pode ter resultados inesperados

As habilidades cognitivas têm requisitado insumos e entradas opcionais. Por exemplo, [a habilidade de extração de frases-chave](cognitive-search-skill-keyphrases.md) tem duas entradas necessárias `text` `languageCode`, e nenhuma entrada opcional. Entradas de habilidade personalizadas são todas consideradas entradas opcionais.

Se alguma entrada necessária estiver faltando ou se alguma entrada não for do tipo certo, a habilidade será ignorada e gera um aviso. As habilidades ignoradas não geram saídas, portanto, se outras habilidades usarem saídas da habilidade ignorada, elas podem gerar avisos adicionais.

Se faltar uma entrada opcional, a habilidade ainda será executada, mas poderá produzir uma saída inesperada devido à entrada ausente.

Em ambos os casos, este aviso pode ser esperado devido à forma de seus dados. Por exemplo, se você tiver um documento contendo `firstName` `middleName`informações `lastName`sobre pessoas com os campos, e `middleName`, você pode ter alguns documentos que não têm uma entrada para . Se você `middleName` passar como uma entrada para uma habilidade no pipeline, então espera-se que essa entrada de habilidade pode estar faltando parte do tempo. Você precisará avaliar seus dados e cenários para determinar se qualquer ação é necessária ou não como resultado deste aviso.

Se você quiser fornecer um valor padrão em caso de entrada ausente, você pode usar a [habilidade Condicional](cognitive-search-skill-conditional.md) para gerar um valor padrão e, em seguida, usar a saída da [habilidade Condicional](cognitive-search-skill-conditional.md) como a entrada de habilidade.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Motivo | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Entrada de habilidade é o tipo errado | "A entrada de habilidade necessária `String`não era do tipo esperado. Nome: `text`, `/document/merged_content`Fonte: ."  "A entrada de habilidade necessária não era do formato esperado. Nome: `text`, `/document/merged_content`Fonte: ."  "Não pode iterar `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`sobre não-matriz ."  "Incapaz de `0` selecionar em `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`não-matriz" | Certas habilidades esperam entradas de determinados `text` tipos, por exemplo, a habilidade de [sentimento](cognitive-search-skill-sentiment.md) espera ser uma string. Se a entrada especificar um valor não-string, a habilidade não será executada e não gerará saídas. Certifique-se de que seu conjunto de dados tenha valores de entrada uniformes no tipo ou use uma [habilidade de API da Web personalizada](cognitive-search-custom-skill-web-api.md) para pré-processar a entrada. Se você está iterando a habilidade sobre uma matriz, `*` verifique o contexto de habilidade e a entrada tem nas posições corretas. Normalmente, tanto o contexto `*` quanto a fonte de entrada devem terminar com para arrays. |
| A entrada de habilidade está faltando | "Falta a entrada de habilidade necessária. Nome: `text`, `/document/merged_content`Fonte: " `/document/normalized_images/0/imageTags`"Valor faltante."  "Não é `0` possível `/document/pages` selecionar `0`na matriz de comprimento." | Se todos os seus documentos receberem esse aviso, provavelmente há um erro de digitação `*` nos caminhos de entrada e você deve verificar duas vezes o invólucro do nome da propriedade, extra ou ausente no caminho, e certifique-se de que os documentos da fonte de dados forneçam as entradas necessárias. |
| A entrada de código de linguagem de habilidade é inválida | A `languageCode` entrada de habilidades `X,Y,Z`tem os seguintes códigos de idioma, pelo menos um dos quais é inválido. | Veja mais detalhes [abaixo](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Aviso: A entrada de habilidade 'languageCode' tem os seguintes códigos de idioma 'X,Y,Z', pelo menos um dos quais é inválido.
Um ou mais dos valores `languageCode` passados para a entrada opcional de uma habilidade a jusante não são suportados. Isso pode ocorrer se você estiver passando a saída do [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) para habilidades subseqüentes, e a saída consiste em mais idiomas do que são suportados nessas habilidades a jusante.

Se você sabe que seu conjunto de dados está tudo em `languageCode` um idioma, `defaultLanguageCode` você deve remover a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e a entrada de habilidadee usar o parâmetro de habilidade para essa habilidade, assumindo que a linguagem seja suportada para essa habilidade.

Se você sabe que seu conjunto de dados contém vários `languageCode` idiomas e, portanto, você precisa da Habilidade e entrada [de Detecção de Idiomas,](cognitive-search-skill-language-detection.md) considere adicionar uma [Habilidade Condicional](cognitive-search-skill-conditional.md) para filtrar o texto com idiomas que não são suportados antes de passar o texto para a habilidade downstream.  Aqui está um exemplo de como isso pode parecer para a EntityRecognitionSkill:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Aqui estão algumas referências para os idiomas suportados atualmente para cada uma das habilidades que podem produzir esta mensagem de erro:
* [Linguagem suportada por análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (para a [keyphraseextractionskill,](cognitive-search-skill-keyphrases.md) [entityrecognitionskill,](cognitive-search-skill-entity-recognition.md) [sentimentskill](cognitive-search-skill-sentiment.md)e [PIIDetectionSkill)](cognitive-search-skill-pii-detection.md)
* [Idiomas suportados por tradutor](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (para a [habilidade de tradução de texto)](cognitive-search-skill-text-translation.md)
* [Habilidade de divisão de texto](cognitive-search-skill-textsplit.md) Idiomas suportados:`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Aviso: A entrada de habilidade foi truncada
As habilidades cognitivas têm limites para o comprimento do texto que podem ser analisados de uma só vez. Se a entrada de texto dessas habilidades estiver acima desse limite, truncaremos o texto para atingir o limite e, em seguida, executaremos o enriquecimento nesse texto truncado. Isso significa que a habilidade é executada, mas não sobre todos os seus dados.

No exemplo LanguageDetectionSkill abaixo, o `'text'` campo de entrada pode disparar este aviso se estiver acima do limite de caracteres. Você pode encontrar os limites de entrada de habilidades na documentação de [habilidades.](cognitive-search-predefined-skills.md)

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Se você quiser garantir que todo o texto seja analisado, considere usar a [habilidade Split](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Aviso: A resposta de habilidade da API da Web contém avisos
O indexador foi capaz de executar uma habilidade no skillset, mas a resposta da solicitação de API da Web indicou que havia avisos durante a execução. Revise os avisos para entender como seus dados são afetados e se a ação é necessária ou não.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Aviso: A configuração atual do indexador não suporta progresso incremental

Este aviso só ocorre para fontes de dados cosmos DB.

O progresso incremental durante a indexação garante que, se a execução do indexador é interrompida por falhas transitórias ou limite de tempo de execução, o indexador pode escolher onde ela parou próxima vez que ele é executado, em vez de toda a coleção de zero de índice novamente. Isso é especialmente importante durante a indexação de coleções grandes.

A capacidade de retomar um trabalho de indexação inacabado baseia-se em ter documentos encomendados pela `_ts` coluna. O indexador usa o carimbo de tempo para determinar qual documento pegar a seguir. Se `_ts` a coluna estiver faltando ou se o indexador não puder determinar se uma consulta personalizada é encomendada por ela, o indexador começa no início e você verá este aviso.

É possível anular esse comportamento, permitindo o progresso incremental e `assumeOrderByHighWatermarkColumn` suprimindo esse aviso usando a propriedade de configuração.

Para obter mais informações, consulte [progresso incremental e consultas personalizadas](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Aviso: Alguns dados foram perdidos durante a projeção. A linha 'X' na tabela 'Y' tem a propriedade de string 'Z' que era muito longa.

O [serviço de armazenamento de tabelas](https://azure.microsoft.com/services/storage/tables) tem limites sobre como as grandes propriedades da [entidade](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) podem ser. Strings podem ter 32.000 caracteres ou menos. Se uma linha com uma propriedade de string suster mais de 32.000 caracteres estiver sendo projetada, apenas os primeiros 32.000 caracteres serão preservados. Para contornar esse problema, evite projetar linhas com propriedades de string com mais de 32.000 caracteres.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Aviso: Texto extraído truncado para caracteres X
Os indexadores limitam a quantidade de texto que pode ser extraído de qualquer documento. Esse limite depende do nível de preços: 32.000 caracteres para nível Livre, 64.000 para basic, 4 milhões para Standard, 8 milhões para Standard S2 e 16 milhões para Standard S3. O texto truncado não será indexado. Para evitar esse aviso, tente separar documentos com grandes quantidades de texto em documentos menores. 

Para obter mais informações, consulte [os limites do Indexador](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Aviso: Não foi possível mapear o campo de saída 'X' para o índice de pesquisa
Mapeamentos de campo de saída que fazem referência a dados inexistentes/nulos produzirão avisos para cada documento e resultarão em um campo de índice vazio. Para contornar esse problema, verifique duas vezes os caminhos de origem de mapeamento de campo de saída para possíveis erros de digitação ou defina um valor padrão usando a [habilidade Condicional](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Aviso: A política de detecção de alteração de dados está configurada para usar a coluna de tecla 'X'
[As políticas de detecção de alterações de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) têm requisitos específicos para as colunas que usam para detectar alterações. Um desses requisitos é que esta coluna seja atualizada toda vez que o item de origem for alterado. Outro requisito é que o novo valor para esta coluna seja maior que o valor anterior. As colunas-chave não preenchem esse requisito porque não mudam em todas as atualizações. Para contornar esse problema, selecione uma coluna diferente para a política de detecção de alterações.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Aviso: O texto do documento parece estar codificado em UTF-16, mas está faltando uma marca de ordem de byte

Os [modos de análise do indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) precisam saber como o texto é codificado antes de analisá-lo. As duas formas mais comuns de codificação de texto são UTF-16 e UTF-8. UTF-8 é uma codificação de comprimento variável onde cada caractere tem entre 1 byte e 4 bytes de comprimento. UTF-16 é uma codificação de comprimento fixo onde cada caractere tem 2 bytes de comprimento. UTF-16 tem duas variantes diferentes, "grande endian" e "pequeno endian". A codificação de texto é determinada por uma "marca de ordem de byte", uma série de bytes antes do texto.

| Codificação | Marca de ordem de byte |
| --- | --- |
| UTF-16 Grande Endian | 0xFE 0xFF |
| UTF-16 Pequeno Endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Se não houver nenhuma marca de ordem de byte, o texto deverá ser codificado como UTF-8.

Para contornar este aviso, determine qual é a codificação de texto para esta bolha e adicione a marca de ordem de byte apropriada.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Aviso: A coleção 'X' da coleção Cosmos DB tem uma política de indexação preguiçosa. Alguns dados podem ser perdidos

Coleções com políticas de indexação [preguiçosas](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) não podem ser consultadas de forma consistente, resultando na falta de dados do indexador. Para contornar esse aviso, mude sua política de indexação para Consistente.
