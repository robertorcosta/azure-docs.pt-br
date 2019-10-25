---
title: Habilidades cognitivas de reconhecimento de entidade
titleSuffix: Azure Cognitive Search
description: Extrair diferentes tipos de entidades de texto em um pipeline de enriquecimento no Azure Pesquisa Cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 08e9656e3b899cbb6d4de733696175e8f31b0e66
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792003"
---
#   <a name="entity-recognition-cognitive-skill"></a>Habilidades cognitivas de reconhecimento de entidade

A habilidade **Entity Recognition** extrai entidades de diferentes tipos do texto. Essa habilidade usa os modelos de machine learning fornecidos pela [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) nos Serviços Cognitivos.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Pesquisa Cognitiva. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço de extração de imagem é descrito na [página de preços do Azure pesquisa cognitiva](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres conforme medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar interromper o backup de seus dados antes de enviá-la para o extrator de frases-chave, considere o uso de [habilidade de Texto Dividido](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas e são todos opcionais.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| Categorias    | Matriz de categorias que devem ser extraídas.  Os tipos possíveis de categoria: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Se nenhuma categoria for fornecida, todos os tipos são retornados.|
|defaultLanguageCode |  Código de idioma do texto de entrada. Há suporte para vários idiomas: `de, en, es, fr, it`|
|minimumPrecision | Não utilizado. Reservado para uso futuro. |
|includeTypelessEntities | Quando configurado como verdadeiro, se o texto contiver uma entidade conhecida, mas não puder ser categorizado em uma das categorias compatíveis, ela será retornada como parte do campo de saída complexa `"entities"`. 
Essas são entidades que são bem conhecidas, mas não classificadas como parte das "categorias" com suporte atual. Por exemplo, "Windows 10" é uma entidade bem conhecida (um produto), mas "produtos" não estão nas categorias com suporte atualmente. O padrão é `false` |


## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                   |
|---------------|-------------------------------|
| languageCode  | Opcional. O padrão é `"en"`.  |
| text          | O texto para analisar.          |

## <a name="skill-outputs"></a>Saídas de habilidades

> [!NOTE]
> Não há suporte para todas as categorias de entidade em todos os idiomas. Somente _en_, _es_ dão suporte à extração dos tipos `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`.

| Nome de saída     | Descrição                   |
|---------------|-------------------------------|
| pessoas      | Uma matriz de cadeias de caracteres onde cada cadeia de caracteres representa o nome de uma pessoa. |
| Locais  | Uma matriz de cadeias de caracteres onde cada cadeia de caracteres representa um local. |
| organizações  | Uma matriz de cadeias de caracteres onde cada cadeia de caracteres representa uma organização. |
| quantidades  | Um array de strings onde cada cadeia de caracteres representa uma quantidade. |
| dateTimes  | Uma matriz de cadeia de caracteres onde cada cadeia de caracteres representa um valor DateTime (como aparece no texto). |
| urls | Uma matriz de cadeia de caracteres onde cada cadeia de caracteres representa um URL |
| e-mails | Uma matriz de cadeia de caracteres onde cada cadeia de caracteres representa um e-mail |
| namedEntities | Uma matriz de tipos complexos que contêm os seguintes campos: <ul><li>category</li> <li>valor (o nome real da entidade)</li><li>deslocamento (o local onde ele foi encontrado no texto)</li><li>confiança (não usado por enquanto. Será definido como um valor de -1)</li></ul> |
| entidades | Uma matriz de tipos complexos que contém informações ricas sobre as entidades extraídas do texto, com os seguintes campos <ul><li> nome (o nome da entidade real. Isso representa um formulário "normalizado")</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (um link para a página da Wikipedia para a entidade)</li><li>bingId</li><li>tipo (a categoria da entidade reconhecida)</li><li>subType (disponível apenas para algumas categorias; oferece uma exibição mais granular do tipo de entidade)</li><li> correspondências (uma coleção complexa que contém)<ul><li>texto (texto bruto para a entidade)</li><li>deslocamento (o local onde ele foi encontrado)</li><li>comprimento (o comprimento do texto bruto de entidade)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Definição de exemplo

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##  <a name="sample-input"></a>Entrada de exemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Saída de exemplo

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": -1
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Casos de erro
Se o código do idioma do documento não for suportado, um erro será retornado e nenhuma entidade será extraída.

## <a name="see-also"></a>Consulte

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
