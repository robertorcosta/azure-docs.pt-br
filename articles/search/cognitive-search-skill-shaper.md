---
title: Habilidades cognitivas do Shaper
titleSuffix: Azure Cognitive Search
description: Extrair metadados e informações estruturadas a partir de dados não estruturados e moldá-los como um tipo complexo em um pipeline de enriquecimento de IA na Pesquisa Cognitiva do Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754124"
---
# <a name="shaper-cognitive-skill"></a>Habilidades cognitivas do Shaper

A habilidade **Shaper** consolida várias entradas em um [tipo complexo](search-howto-complex-data-types.md) que pode ser referenciado mais tarde no pipeline de enriquecimento. A habilidade **shaper** permite essencialmente criar uma estrutura, definir o nome dos membros dessa estrutura e atribuir valores a cada membro. Exemplos de campos consolidados úteis em cenários de pesquisa incluem a combinação de um nome e sobrenome em uma única estrutura, cidade e estado em uma única estrutura, ou nome e data de nascimento em uma única estrutura para estabelecer uma identidade única.

Além disso, a habilidade **shaper** ilustrada no [cenário 3](#nested-complex-types) adiciona uma propriedade *opcional sourceContext* à entrada. As propriedades *source* e *sourceContext* são mutuamente exclusivas. Se a entrada estiver no contexto da habilidade, basta usar a *fonte*. Se a entrada estiver em um contexto *diferente* do contexto de habilidade, use o *sourceContext*. O *sourceContext* exige que você defina uma entrada aninhada com o elemento específico sendo abordado como a fonte. 

O nome da saída é sempre "saída". Internamente, o pipeline pode mapear um nome diferente, como "analyzedText" como mostrado nos exemplos abaixo, mas a própria habilidade **shaper** retorna "saída" na resposta. Isso pode ser importante se você estiver depurando documentos enriquecidos e observar a discrepância de nomenclatura, ou se você criar uma habilidade personalizada e estruturação de resposta por conta própria.

> [!NOTE]
> A habilidade **shaper** não está vinculada a uma API de Serviços Cognitivos e você não é cobrado por usá-la. No entanto, você ainda deverá [anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) para substituir a opção de recurso **Gratuito** que limita você a um pequeno número de enriquecimentos por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Cenário 1: tipos complexos

Considere um cenário onde você deseja criar uma estrutura chamada *analyzedText* que tem dois membros: *texto* e *sentimento*, respectivamente. Em um índice, um campo pesquisável em várias partes é chamado de *tipo complexo* e é frequentemente criado quando os dados de origem têm uma estrutura complexa correspondente que mapeia para ele.

No entanto, outra abordagem para criar tipos complexos é através da habilidade **Shaper.** Ao incluir essa habilidade em um skillset, as operações na memória durante o processamento de skillset podem produzir formas de dados com estruturas aninhadas, que podem então ser mapeadas para um tipo complexo em seu índice. 

O exemplo a seguir de definição de habilidade fornece os nomes dos membros como a entrada. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Índice amostral

Um skillset é invocado por um indexador, e um indexador requer um índice. Uma representação de campo complexa em seu índice pode parecer o seguinte exemplo. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Entrada de habilidades

Um documento JSON de entrada que fornece entrada utilizável para esta habilidade **shaper** pode ser:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Saída de habilidade

A habilidade **shaper** gera um novo elemento chamado *analyzedText* com os elementos combinados de *texto* e *sentimento*. Esta saída está em conformidade com o esquema de índice. Ele será importado e indexado em um índice de Pesquisa Cognitiva Azure.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Cenário 2: consolidação de entradas

Em outro exemplo, imagine que em diferentes estágios do processamento de pipeline, você extraiu o título de um livro e títulos de capítulo em diferentes páginas do livro. Agora você pode criar uma única estrutura composta por essas várias entradas.

A definição de habilidade **do Shaper** para este cenário pode parecer o seguinte exemplo:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Saída de habilidade
Neste caso, o **Shaper** achata todos os títulos de capítulo para criar uma única matriz. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Cenário 3: consolidação de entrada a partir de contextos aninhados

Imagine que você tem o título, capítulos e conteúdo de um livro e executou o reconhecimento de entidades e frases-chave sobre o conteúdo e agora precisa agregar resultados das diferentes habilidades em uma única forma com o nome do capítulo, entidades e frases-chave.

A definição de habilidade **do Shaper** para este cenário pode parecer o seguinte exemplo:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Saída de habilidade
Neste caso, o **Shaper** cria um tipo complexo. Essa estrutura existe na memória. Se você quiser salvá-lo em um [armazenamento de conhecimento,](knowledge-store-concept-intro.md)você deve criar uma projeção em seu conjunto de habilidades que define características de armazenamento.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Como usar tipos complexos](search-howto-complex-data-types.md)
+ [Repositório de conhecimento (versão prévia)](knowledge-store-concept-intro.md)
+ [Criar um repositório de conhecimento no REST](knowledge-store-create-rest.md)