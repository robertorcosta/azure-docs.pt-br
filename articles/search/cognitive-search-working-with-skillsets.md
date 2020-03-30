---
title: Conceitos de skillset e fluxo de trabalho
titleSuffix: Azure Cognitive Search
description: Skillsets é onde você cria um pipeline de enriquecimento de IA na Pesquisa Cognitiva do Azure. Aprenda conceitos e detalhes importantes sobre a composição de skillsets.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8b45840215092281c7fbc8d499e26b095b374dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191038"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Conceitos e composição de skillset em Azure Cognitive Search

Este artigo é para desenvolvedores que precisam de uma compreensão mais profunda de como o pipeline de enriquecimento funciona e assumem que você tem uma compreensão conceitual do processo de enriquecimento da IA. Se você é novo neste conceito, comece com:
+ [Enriquecimento de IA na Pesquisa Cognitiva do Azure](cognitive-search-concept-intro.md)
+ [Repositório de conhecimento (versão prévia)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Especificar o Skillset
Um skillset é um recurso reutilizável no Azure Cognitive Search que especifica uma coleção de habilidades cognitivas usadas para analisar, transformar e enriquecer o conteúdo de texto ou imagem durante a indexação. A criação de um skillset permite anexar enriquecimentos de texto e imagem na fase de ingestão de dados, extraindo e criando novas informações e estruturas a partir de conteúdo bruto.

Um skillset tem três propriedades:

+   ```skills```, uma coleção não ordenada de habilidades para as quais a plataforma determina a seqüência de execução com base nas entradas necessárias para cada habilidade
+   ```cognitiveServices```, a chave dos serviços cognitivos necessários para faturar as habilidades cognitivas invocadas
+   ```knowledgeStore```, a conta de armazenamento onde seus documentos enriquecidos serão projetados



Skillsets são de autoria em JSON. Você pode construir habilidades complexas com looping e [ramificação](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) usando a [linguagem de expressão](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional). A linguagem de expressão usa a notação de caminho [JSON Pointer](https://tools.ietf.org/html/rfc6901) com algumas modificações para identificar nódulos na árvore de enriquecimento. Um ```"/"``` atravessa um nível mais ```"*"``` baixo na árvore e age como um operador para cada operador no contexto. Esses conceitos são melhor descritos com um exemplo. Para ilustrar alguns dos conceitos e capacidades, vamos percorrer as [avaliações](knowledge-store-connect-powerbi.md) do hotel para provar habilidades. Para visualizar o skillset depois de seguir o fluxo de trabalho de dados de importação, você precisará usar um cliente de API REST para [obter o skillset](https://docs.microsoft.com/rest/api/searchservice/get-skillset).

### <a name="enrichment-tree"></a>Árvore de enriquecimento

Para imaginar como um skillset enriquece progressivamente seu documento, vamos começar com como o documento se parece antes de qualquer enriquecimento. A saída da quebra do documento depende da fonte de dados e do modo de análise específico selecionado. Este também é o estado do documento que os [mapeamentos](search-indexer-field-mappings.md) de campo podem obter conteúdo ao adicionar dados ao índice de pesquisa.
![Diagrama do repositório de conhecimento no diagrama](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Diagrama do repositório de conhecimento no diagrama")

Uma vez que um documento está no pipeline de enriquecimento, ele é representado como uma árvore de conteúdo e enriquecimentos associados. Esta árvore é instanciada como a saída de quebra de documento. O formato de árvore de enriquecimento permite que o pipeline de enriquecimento conecte metadados até mesmo a tipos de dados primitivos, não é um objeto JSON válido, mas pode ser projetado em um formato JSON válido. A tabela a seguir mostra o estado de um documento que entra no gasoduto de enriquecimento:

|Fonte de dados\Modo de análise|Padrão|JSON, JSON Lines & CSV|
|---|---|---|
|Armazenamento de Blobs|/documento/conteúdo<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{coluna2}<br>…|N/D |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/D|

 À medida que as habilidades são executadas, eles adicionam novos nódulos à árvore de enriquecimento. Esses novos nós podem então ser usados como entradas para habilidades a jusante, projetando para o armazenamento de conhecimento ou mapeando para campos de índice. Os enriquecimentos não são mutáveis: uma vez criados, os nós não podem ser editados. À medida que suas habilidades ficam mais complexas, sua árvore de enriquecimento também, mas nem todos os nós na árvore de enriquecimento precisam chegar ao índice ou à loja de conhecimento. 

Você pode persistir seletivamente apenas um subconjunto dos enriquecimentos para o índice ou para o armazenamento de conhecimento.
Para o resto deste documento, assumiremos que estamos trabalhando com [o exemplo de revisões de hotéis,](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)mas os mesmos conceitos se aplicam a documentos enriquecedores de todas as outras fontes de dados.

### <a name="context"></a>Contexto
Cada habilidade requer um contexto. Um contexto determina:
+   O número de vezes que a habilidade é executada, com base nos nós selecionados. Para valores de contexto ```/*``` da coleta de tipo, adicionar um no final resultará na invocação da habilidade uma vez para cada instância na coleção. 
+   Onde na árvore de enriquecimento as saídas de habilidade são adicionadas. As saídas são sempre adicionadas à árvore como filhos do nó de contexto. 
+   Forma das entradas. Para coleções de vários níveis, definir o contexto para a coleção dos pais afetará a forma da entrada para a habilidade. Por exemplo, se você tem uma árvore de enriquecimento com uma lista de países, cada um enriqueceu com uma lista de estados contendo uma lista de CEP.

|Contexto|Entrada|Forma de entrada|Invocação de Habilidades|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Uma lista de todos os CEP do país |Uma vez por país |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Uma lista de CEP-POSTAL no estado | Uma vez por combinação de país e estado|

### <a name="sourcecontext"></a>SourceContext

O `sourceContext` é usado apenas em insumos de habilidades e [projeções.](knowledge-store-projection-overview.md) É usado para construir objetos aninhados em vários níveis. Você pode precisar criar um novo objeto para passá-lo como uma entrada para uma habilidade ou projetar na loja de conhecimento. Como os nódulos de enriquecimento podem não ser um objeto JSON válido na árvore de enriquecimento e referenciar um nó na árvore só retorna esse estado do nó quando ele foi criado, usar os enriquecimentos como entradas de habilidade ou projeções exige que você crie um objeto JSON bem formado. O `sourceContext` permite que você construa um objeto hierárquico e anônimo, o que exigiria múltiplas habilidades se você estivesse usando apenas o contexto. O `sourceContext` uso é mostrado na próxima seção. Veja a saída de habilidade que gerou um enriquecimento para determinar se é um objeto JSON válido e não um tipo primitivo.

### <a name="projections"></a>Projeções

Projeção é o processo de seleção dos nódulos da árvore de enriquecimento a ser salvo na loja de conhecimento. Projeções são formas personalizadas do documento (conteúdo e enriquecimentos) que podem ser saídas como projeções de tabela ou objeto. Para saber mais sobre como trabalhar com projeções, consulte [o trabalho com projeções.](knowledge-store-projection-overview.md)

![Opções de mapeamento de campo](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opções de mapeamento de campo para gasoduto de enriquecimento")

O diagrama acima descreve o seletor com o qual você trabalha com base em onde você está no pipeline de enriquecimento.

## <a name="generate-enriched-data"></a>Gerar dados enriquecidos 

Vamos agora passar pelas habilidades de avaliações do hotel, você pode seguir o [tutorial](knowledge-store-connect-powerbi.md) para criar o skillset ou [visualizar](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) as habilidades. Nós vamos olhar para:

* como a árvore de enriquecimento evolui com a execução de cada habilidade 
* como o contexto e as entradas funcionam para determinar quantas vezes uma habilidade executa 
* qual a forma da entrada é baseada no contexto. 

Uma vez que estamos usando o modo de análise de texto delimitado para o indexador, um documento dentro do processo de enriquecimento representa uma única linha dentro do arquivo CSV.

### <a name="skill-1-split-skill"></a>Habilidade #1: Habilidade dividida 

![árvore de enriquecimento após quebra de documento](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Árvore de enriquecimento após quebra de documento e antes da execução de habilidades")

Com o contexto ```"/document/reviews_text"```de habilidade de , `reviews_text`esta habilidade será executada uma vez para o . A saída de habilidade `reviews_text` é uma lista onde o é dividido em segmentos de caracteres de 5000. A saída da habilidade `pages` dividida é nomeada e adicionada à árvore de enriquecimento. O `targetName` recurso permite que você renomeie uma saída de habilidade antes de ser adicionado à árvore de enriquecimento.

A árvore de enriquecimento agora tem um novo nó colocado o contexto da habilidade. Este nó está disponível para qualquer mapeamento de habilidade, projeção ou campo de saída.


O nó raiz para todos `"/document"`os enriquecimentos é . Ao trabalhar com indexadores `"/document"` blob, o nó `"/document/content"` terá `"/document/normalized_images"`nódulos infantis de e . Ao trabalhar com dados CSV, como estamos neste exemplo, os nomes `"/document"`das colunas serão mapeados para nós abaixo . Para acessar qualquer um dos enriquecimentos adicionados a um nó por uma habilidade, o caminho completo para o enriquecimento é necessário. Por exemplo, se você quiser usar ```pages``` o texto do nó como uma entrada para ```"/document/reviews_text/pages/*"```outra habilidade, você precisará especiá-lo como .
 
 ![árvore de enriquecimento após habilidade #1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Árvore de enriquecimento após habilidade #1 executa")

### <a name="skill-2-language-detection"></a>Habilidade #2 detecção de linguagem
 Embora a habilidade de detecção de linguagem seja a terceira (habilidade #3) definida no skillset, é a próxima habilidade a ser executada. Uma vez que não está bloqueado por exigir quaisquer entradas, ele será executado em paralelo com a habilidade anterior. Como a habilidade de divisão que a precedeu, a habilidade de detecção de linguagem também é invocada uma vez para cada documento. A árvore de enriquecimento agora tem um novo nó para a linguagem.
 ![árvore de enriquecimento após #2 de habilidade](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Árvore de enriquecimento após habilidade #2 executa")
 
 ### <a name="skill-3-key-phrases-skill"></a>Habilidade #3: Habilidade de frases-chave 

Dado o ```/document/reviews_text/pages/*``` contexto das frases-chave, a habilidade será invocada `pages` uma vez para cada um dos itens da coleção. A saída da habilidade será um nó o elemento de página associada. 

 Agora você deve ser capaz de olhar para o resto das habilidades no skillset e visualizar como a árvore de enriquecimentos continuará a crescer com a execução de cada habilidade. Algumas habilidades, como a habilidade de mesclagem e a habilidade do shaper, também criam novos nós, mas só usam dados de nós existentes e não criam novos enriquecimentos líquidos.

![árvore de enriquecimento depois de todas as habilidades](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Árvore de enriquecimento depois de todas as habilidades")

As cores dos conectores na árvore acima indicam que os enriquecimentos foram criados por diferentes habilidades e os nós precisarão ser abordados individualmente e não farão parte do objeto retornado ao selecionar o nó pai.

## <a name="save-enrichments-in-a-knowledge-store"></a>Economize enriquecimentos em uma loja de conhecimento 

Os skillsets também definem uma loja de conhecimento onde seus documentos enriquecidos podem ser projetados como tabelas ou objetos. Para salvar seus dados enriquecidos no armazenamento de conhecimento, você define um conjunto de projeções para o seu documento enriquecido. Para saber mais sobre a loja de conhecimento, veja [a visão geral do armazém de conhecimento](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Projeções de fatiamento

Ao definir um grupo de projeção de tabela, um único nó na árvore de enriquecimento pode ser cortado em várias tabelas relacionadas. Se você adicionar uma tabela com um caminho de origem que seja filho de uma projeção de tabela existente, o nó infantil resultante não será uma criança da projeção de tabela existente, mas será projetada na nova tabela, relacionada. Esta técnica de corte permite definir um único nó em uma habilidade de shaper que pode ser a fonte para todas as suas projeções de tabela. 

### <a name="shaping-projections"></a>Moldando projeções

Há duas maneiras de definir uma projeção. Você poderia usar uma habilidade de shaper para criar um novo nó que é o nó raiz para todos os enriquecimentos que você está projetando. Então, em suas projeções, você só referenciaria a saída da habilidade do modelador. Você também pode moldar uma projeção dentro da própria definição de projeção.

A abordagem do shaper é mais verbosa do que a modelagem inline, mas garante que todas as mutações da árvore de enriquecimento estão contidas dentro das habilidades e que a saída é um objeto que pode ser reutilizado. A modelagem inline permite que você crie a forma que você precisa, mas é um objeto anônimo e só está disponível para a projeção para a qual é definido. As abordagens podem ser usadas em conjunto ou separadamente. O skillset criado para você no fluxo de trabalho do portal contém ambos. Ele usa uma habilidade de shaper para as projeções de tabela, mas também usa modelagem inline para projetar a tabela de frases-chave.

Para estender o exemplo, você pode optar por remover a modelagem inline e usar uma habilidade modeladora para criar um novo nó para as frases-chave. Para criar uma forma projetada em `hotelReviewsDocument`três `hotelReviewsPages`tabelas, ou seja, , e `hotelReviewsKeyPhrases`, as duas opções são descritas nas seções a seguir.


#### <a name="shaper-skill-and-projection"></a>Habilidade e projeção do shaper 

> [!Note]
> Algumas das colunas da tabela de documentos foram removidas deste exemplo para a brevidade.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

Com `tableprojection` o nó definido `outputs` na seção acima, agora podemos usar o `tableprojection` recurso de corte para projetar partes do nó em diferentes tabelas:

> [!Note]
> Este é apenas um trecho da projeção dentro da configuração do armazenamento de conhecimento.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Projeções de modelagem em linha

A abordagem de modelagem em linha não requer uma habilidade de modelador, pois todas as formas necessárias para as projeções são criadas no momento em que são necessárias. Para projetar os mesmos dados do exemplo anterior, a opção de projeção inline ficaria assim:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Uma observação de ambas as `"Keyphrases"` abordagens é `"sourceContext"`como os valores são projetados usando o . O `"Keyphrases"` nó, que contém uma coleção de strings, é em si uma criança do texto da página. No entanto, como as projeções requerem um objeto JSON e a página é primitiva (string), a `"sourceContext"` é usada para envolver a frase-chave em um objeto com uma propriedade nomeada. Esta técnica permite que até mesmo primitivos sejam projetados independentemente.

## <a name="next-steps"></a>Próximas etapas

Como próximo passo, crie seu primeiro skillset com habilidades cognitivas.

> [!div class="nextstepaction"]
> [Crie suas primeiras habilidades.](cognitive-search-defining-skillset.md)
