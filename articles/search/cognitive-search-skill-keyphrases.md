---
title: Habilidade cognitiva de Extração de Frases-Chave
titleSuffix: Azure Cognitive Search
description: Avalia texto não estruturado e, para cada registro, retorna uma lista de frases-chave em um pipeline de enriquecimento de IA na Pesquisa Cognitiva do Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ccdd25d82af2b4893260af18dac818816d9e4579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791968"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Habilidade cognitiva de Extração de Frases-Chave

A habilidade de **Extração de Frases-Chave** avalia o texto não estruturado e para cada registro, retorna uma lista de frases-chave. Essa habilidade usa os modelos de machine learning fornecidos pela [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) nos Serviços Cognitivos.

Esse recurso é útil se você precisar identificar rapidamente os principais pontos de estratégias no registro. Por exemplo, texto de entrada especificado “A comida estava deliciosa e a equipe foi maravilhosa”, o serviço retorna “comida” e “equipe maravilhosa”.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem, como parte do estágio de quebra de documento na Pesquisa Cognitiva do Azure. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço da extração de imagem é descrito na [página de preços da Pesquisa Cognitiva do Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres, medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar interromper o backup de seus dados antes de enviá-la para o extrator de frases-chave, considere o uso de [habilidade de Texto Dividido](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Entradas                | Descrição |
|---------------------|-------------|
| defaultLanguageCode | (opcional) O código de idioma a ser aplicado a documentos que não especifica explicitamente o idioma.  Se o código de idioma padrão não for especificado, em inglês (en) será usado como o código de idioma padrão. <br/> Consulte [Lista completa dos idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (opcional) O número máximo de frases-chave para produzir. |

## <a name="skill-inputs"></a>Entradas de habilidades

| Entradas     | Descrição |
|--------------------|-------------|
| text | O texto a ser analisado.|
| languageCode  |  Uma cadeia de caracteres que indica o idioma dos registros. Se esse parâmetro não for especificado, o código de idioma padrão será usado para analisar os registros. <br/>Veja [a lista completa de idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Definição de exemplo

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>Saída de exemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Erros e avisos
Se você fornecer um código de idioma sem suporte, será gerado um erro e frases-chave não são extraídos.
Se o texto estiver vazio, um aviso será gerado.
Se o texto for maior que 50.000 caracteres, somente os primeiros 50.000 caracteres serão analisados e um aviso será emitido.

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
