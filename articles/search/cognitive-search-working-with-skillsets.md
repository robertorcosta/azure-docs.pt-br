---
title: Conceitos e fluxo de trabalho de conjuntos de habilidades
titleSuffix: Azure Cognitive Search
description: Os conjuntos de habilidades são onde você cria um pipeline de enriquecimento de IA no Azure Cognitive Search. Conheça conceitos e detalhes importantes sobre a composição de conjuntos de habilidades.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8e263d29bc71ac76c374eeda78e5250a0af2095
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744798"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Conceitos e composição de conjuntos de habilidades no Azure Cognitive Search

Este artigo destina-se a desenvolvedores que precisam de uma compreensão mais profunda de como o pipeline de enriquecimento funciona e pressupõe que você tenha um entendimento conceitual do processo de enriquecimento de IA. Se não estiver familiarizado com esse conceito, comece com:
+ [Enriquecimento de IA no Azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Repositório de conhecimento (versão prévia)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Especificar o conjunto de habilidades
Um conjunto de habilidades é um recurso reutilizável no Azure Cognitive Search que especifica uma coleção de habilidades cognitivas usadas para analisar, transformar e enriquecer o conteúdo de texto ou imagem durante a indexação. A criação de um conjunto de habilidades permite anexar enriquecimentos de texto e imagem na fase de ingestão de dados, extraindo e criando novas informações e estruturas a partir do conteúdo bruto.

Um conjunto de habilidades apresenta três propriedades:

+    ```skills```, uma coleção não ordenada de habilidades para as quais a plataforma determina a sequência de execução com base nas entradas necessárias para cada habilidade
+    ```cognitiveServices```, a chave de serviços cognitivos necessária para cobrar as habilidades cognitivas invocadas
+    ```knowledgeStore```, a conta de armazenamento em que seus documentos enriquecidos serão projetados



Os conjuntos de habilidades são criados em JSON. Você pode criar conjuntos de habilidades complexos com looping e [ramificação](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) usando a [linguagem de expressão](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional). A linguagem de expressão usa a notação de caminho [ponteiro JSON](https://tools.ietf.org/html/rfc6901) com algumas modificações para identificar nós na árvore de enriquecimento. Um ```"/"``` percorre um nível mais baixo na árvore e ```"*"``` atua como um operador for-each no contexto. Esses conceitos são melhor descritos com um exemplo. Para ilustrar alguns dos conceitos e funcionalidades, veremos o conjunto de habilidades de [exemplo de críticas de hotéis](knowledge-store-connect-powerbi.md). Para exibir o conjunto de habilidades depois de seguir o fluxo de trabalho Importar Dados, você precisará usar um cliente da API REST para [obter o conjunto de habilidades](https://docs.microsoft.com/rest/api/searchservice/get-skillset).

### <a name="enrichment-tree"></a>Árvore de enriquecimento

Para prever como um conjunto de habilidades enriquece o seu documento progressivamente, vamos começar com a aparência do documento antes de qualquer enriquecimento. A saída da quebra de documento depende da fonte de dados e do modo de análise específico selecionado. Esse também é o estado do documento que pode originar o conteúdo dos [mapeamentos de campo](search-indexer-field-mappings.md) ao adicionar dados ao índice de pesquisa.
![Diagrama do repositório de conhecimento no diagrama](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Diagrama do repositório de conhecimento no diagrama")

Quando um documento está no pipeline de enriquecimento, ele é representado como uma árvore de conteúdo e enriquecimentos associados. Essa árvore é instanciada como a saída da quebra de documento. O formato da árvore de enriquecimento permite ao pipeline de enriquecimento anexar metadados até mesmo aos tipos de dados primitivos. Ele não é um objeto JSON válido, mas pode ser projetado em um formato JSON válido. A tabela a seguir mostra o estado de um documento em sua entrada no pipeline de enriquecimento:

|Fonte de dados\Modo de análise|Padrão|JSON, linhas JSON & CSV|
|---|---|---|
|Armazenamento de Blobs|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|N/D |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/D|

 À medida que as habilidades são executadas, novos nós são adicionados à árvore de enriquecimento. Esses novos nós podem ser usados como entradas para habilidades de downstream, projeção para o repositório de conhecimento ou mapeamento para campos de índice. Os enriquecimentos não são mutáveis: uma vez criados, os nós não podem ser editados. À medida que seus conjuntos de habilidades se tornam mais complexos, a árvore de enriquecimento também aumenta, mas nem todos os nós da árvore de enriquecimento precisam chegar ao índice ou ao repositório de conhecimento. 

Você pode manter seletivamente apenas um subconjunto dos enriquecimentos no índice ou no repositório de conhecimento.
No restante deste documento, vamos pressupor que estamos trabalhando com o [exemplo de críticas de hotéis](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi), mas os mesmos conceitos se aplicam ao enriquecimento de documentos de todas as outras fontes de dados.

### <a name="context"></a>Contexto
Cada habilidade requer um contexto. Um contexto determina:
+    O número de vezes que a habilidade é executada, com base nos nós selecionados. Para valores de contexto da coleção de tipos, adicionar um ```/*``` no final resultará na invocação da habilidade uma vez para cada instância da coleção. 
+    Onde são adicionadas as saídas de habilidades na árvore de enriquecimento. As saídas são sempre adicionadas à árvore como filhos do nó de contexto. 
+    A forma das entradas. Para coleções de vários níveis, a configuração do contexto para a coleção pai afetará a forma da entrada da habilidade. Por exemplo, se você possui uma árvore de enriquecimento com uma lista de países/regiões, cada um enriquecido com uma lista de estados que contém uma lista de códigos postais.

|Contexto|Entrada|Forma da entrada|Invocação de habilidades|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Uma lista de todos os códigos postais no país/região |Uma vez por país/região |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Uma lista de códigos postais no estado | Uma vez por combinação de país/região e estado|

### <a name="sourcecontext"></a>SourceContext

O `sourceContext` só é usado em entradas de habilidades e [projeções](knowledge-store-projection-overview.md). Ele é usado para construir objetos aninhados de vários níveis. Talvez seja necessário criar um novo objeto para transmiti-lo como uma entrada para uma habilidade ou projeto no repositório de conhecimento. Como os nós de enriquecimento podem não ser um objeto JSON válido na árvore de enriquecimento e a referência a um nó na árvore só retorna esse estado do nó quando ele foi criado, usar os enriquecimentos como entradas ou projeções de habilidades requer que você crie um objeto JSON bem formado. O `sourceContext` permite construir um objeto de tipo anônimo e hierárquico, que exigiria várias habilidades se você estivesse usando apenas o contexto. O uso de `sourceContext` é mostrado na próxima seção. Observe a saída de habilidade que gerou um enriquecimento para determinar se é um objeto JSON válido e não um tipo primitivo.

### <a name="projections"></a>Projeções

Projeção é o processo de selecionar os nós da árvore de enriquecimento a serem salvos no repositório de conhecimento. Projeções são formas personalizadas do documento (conteúdo e enriquecimentos) que podem ser exibidas como projeções de tabela ou objeto. Para saber mais sobre como trabalhar com projeções, confira [Trabalhar com projeções](knowledge-store-projection-overview.md).

![Opções de mapeamento de campos](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opções de mapeamento de campos para pipeline de enriquecimento")

O diagrama acima descreve o seletor com o qual você trabalha, com base em onde você está no pipeline de enriquecimento.

## <a name="generate-enriched-data"></a>Gerar dados enriquecidos 

Agora, vamos percorrer o conjunto de habilidades de críticas de hotéis, você pode seguir o [tutorial](knowledge-store-connect-powerbi.md) para criar o conjunto de habilidades ou [exibir](https://github.com/Azure-Samples/azure-search-postman-samples/) o conjunto de habilidades. Vamos ver o seguinte:

* como a árvore de enriquecimento evolui com a execução de cada habilidade 
* como o contexto e as entradas funcionam para determinar quantas vezes uma habilidade é executada 
* qual é a forma da entrada, com base no contexto. 

Como estamos usando o modo de análise de texto delimitado para o indexador, um documento no processo de enriquecimento representa uma única linha no arquivo CSV.

### <a name="skill-1-split-skill"></a>Habilidade nº 1: divisão da habilidade 

![árvore de enriquecimento após a quebra do documento](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Árvore de enriquecimento após a quebra do documento e antes da execução da habilidade")

Com o contexto de habilidade de ```"/document/reviews_text"```, essa habilidade será executada uma vez para `reviews_text`. A saída de habilidade é uma lista em que `reviews_text` é dividido em segmentos de 5000 caracteres. A saída da habilidade de divisão é chamada `pages` e adicionada à árvore de enriquecimento. O recurso `targetName` permite renomear uma saída de habilidade antes que ela seja adicionada à árvore de enriquecimento.

A árvore de enriquecimento agora tem um novo nó colocado no contexto da habilidade. Este nó está disponível para qualquer habilidade, projeção ou mapeamento de campo de saída.


O nó raiz de todos os enriquecimentos é `"/document"`. Ao trabalhar com indexadores de blob, o nó `"/document"` terá nós filhos de `"/document/content"` e `"/document/normalized_images"`. Ao trabalhar com dados CSV, como neste exemplo, os nomes das colunas serão mapeados para os nós abaixo de `"/document"`. Para acessar qualquer um dos enriquecimentos adicionados a um nó por uma habilidade, é necessário o caminho completo para o enriquecimento. Por exemplo, se você deseja usar o texto do nó ```pages``` como uma entrada para outra habilidade, precisará especificá-lo como ```"/document/reviews_text/pages/*"```.
 
 ![árvore de enriquecimento após a habilidade nº 1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Árvore de enriquecimento após a execução da habilidade nº 1")

### <a name="skill-2-language-detection"></a>Habilidade nº 2: detecção de idioma
 Embora a habilidade de detecção de idioma seja a terceira (habilidade nº 3) definida no conjunto de habilidades, é a próxima habilidade a ser executada. Como ela não é bloqueada pela exigência de qualquer entrada, ela será executada em paralelo com a habilidade anterior. Como a habilidade de divisão que a precedeu, a habilidade de detecção de idioma também é invocada uma vez para cada documento. A árvore de enriquecimento agora tem um novo nó para o idioma.
 ![árvore de enriquecimento após a habilidade nº 2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Árvore de enriquecimento após a execução da habilidade nº 2")
 
 ### <a name="skill-3-key-phrases-skill"></a>Habilidade nº 3: habilidade de frases-chave 

Dado o contexto de ```/document/reviews_text/pages/*```, a habilidade das frases-chave será invocada uma vez para cada um dos itens na coleção `pages`. A saída da habilidade será um nó no elemento de página associado. 

 Agora você deve poder examinar o restante das habilidades no conjunto de habilidades e visualizar como a árvore de enriquecimentos continuará a crescer com a execução de cada habilidade. Algumas habilidades, como a de mesclagem e a de formatação, também criam novos nós, mas apenas usam dados de nós existentes e não criam novos enriquecimentos.

![árvore de enriquecimento após todas as habilidades](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Árvore de enriquecimento após todas as habilidades")

As cores dos conectores na árvore acima indicam que os enriquecimentos foram criados por diferentes habilidades e os nós precisarão ser endereçados individualmente e não farão parte do objeto retornado ao selecionar o nó pai.

## <a name="save-enrichments-in-a-knowledge-store"></a>Salvar enriquecimentos em um repositório de conhecimento 

Os conjuntos de habilidades também definem um repositório de conhecimento onde seus documentos enriquecidos podem ser projetados como tabelas ou objetos. Para salvar seus dados enriquecidos no repositório de conhecimento, defina um conjunto de projeções para o documento enriquecido. Para saber mais sobre o repositório de conhecimento, confira [Visão Geral do Repositório de Conhecimento](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Fracionar projeções

Ao definir um grupo de projeção de tabela, um único nó na árvore de enriquecimento pode ser fracionado em várias tabelas relacionadas. Se você adicionar uma tabela com um caminho de origem que seja filho de uma projeção de tabela existente, o nó filho resultante não será um filho da projeção de tabela existente, mas, em vez disso, será projetado para a nova tabela relacionada. Essa técnica de fracionamento permite definir um único nó em uma habilidade de formatação que pode ser a origem de todas as suas projeções de tabela. 

### <a name="shaping-projections"></a>Formatar projeções

Há duas maneiras de definir uma projeção. Você pode usar uma habilidade de formatação para criar um novo nó que é o nó raiz de todos os enriquecimentos que você está projetando. Então, em suas projeções, você faria referência apenas à saída da habilidade de formatação. Você também poderia embutir a forma em uma projeção dentro da própria definição de projeção.

A abordagem de formatação é mais detalhada do que a formatação embutida, mas garante que todas as mutações da árvore de enriquecimento estejam contidas nas habilidades e que a saída seja um objeto que possa ser reutilizado. A formatação embutida permite criar a forma que você precisa, mas é um objeto anônimo e está disponível apenas para a projeção para a qual está definida. As abordagens podem ser usadas juntas ou separadamente. O conjunto de habilidades criado para você no fluxo de trabalho do portal contém ambas. Ele usa uma habilidade de formatação para as projeções de tabela, mas também usa a formatação embutida para projetar a tabela de frases-chave.

Para estender o exemplo, você pode optar por remover a formatação embutida e usar uma habilidade de formatação para criar um novo nó para as frases-chave. Para criar uma forma projetada em três tabelas, isto é, `hotelReviewsDocument`, `hotelReviewsPages` e `hotelReviewsKeyPhrases`, as duas opções são descritas nas seções a seguir.


#### <a name="shaper-skill-and-projection"></a>Habilidade de formatação e projeção 

> [!Note]
> Para sermos breves, algumas das colunas da tabela de documentos foram removidas deste exemplo.
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

Com o nó `tableprojection` definido na seção `outputs` acima, agora podemos usar o recurso de fracionamento para projetar partes do nó `tableprojection` em diferentes tabelas:

> [!Note]
> Este é apenas um trecho da projeção na configuração do repositório de conhecimento.
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

#### <a name="inline-shaping-projections"></a>Projeção de formatação embutida

A abordagem de formatação embutida não requer uma habilidade de formatação, pois todas as formas necessárias para as projeções são criadas no momento em que são necessárias. Para projetar os mesmos dados do exemplo anterior, a opção de projeção embutida seria assim:

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
  
Uma observação de ambas as abordagens é como os valores de `"Keyphrases"` são projetados usando o `"sourceContext"`. O nó `"Keyphrases"`, que contém uma coleção de cadeias, é filho do texto da página. No entanto, como as projeções exigem um objeto JSON e a página é do tipo primitivo (cadeia de caracteres), `"sourceContext"` é usado para encapsular a frase-chave em um objeto com uma propriedade nomeada. Essa técnica permite que mesmo tipos primitivos sejam projetados independentemente.

## <a name="next-steps"></a>Próximas etapas

Como próximo passo, crie seu primeiro conjunto de habilidades com habilidades cognitivas.

> [!div class="nextstepaction"]
> [Crie seu primeiro conjunto de habilidades](cognitive-search-defining-skillset.md).
