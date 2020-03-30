---
title: Habilidade cognitiva do Text Split
titleSuffix: Azure Cognitive Search
description: Quebre o texto em pedaços ou páginas de texto com base no comprimento de um pipeline de enriquecimento de IA na Pesquisa Cognitiva do Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3f80169808b1e6420f04b786d2bb06bde9c96231
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73479658"
---
# <a name="text-split-cognitive-skill"></a>Habilidade cognitiva do Text Split

A habilidade **Text Split** quebra o texto em partes do texto. Você pode especificar se deseja quebrar o texto em sentenças ou em páginas de um tamanho específico. Essa habilidade é especialmente útil se houver requisitos de comprimento em outras habilidades downstream em texto. 

> [!NOTE]
> Essa habilidade não está associada a uma API de Serviços Cognitivos e você não é cobrado por utilizá-la. No entanto, você ainda deverá [anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) para substituir a opção de recurso **Gratuito** que limita você a um pequeno número de enriquecimentos por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| textSplitMode      | "Páginas" ou "sentenças" | 
| maximumPageLength | Se o textSplitMode for definido como "páginas", isso se refere ao comprimento máximo da página, conforme medido pelo `String.Length`. O valor mínimo é 100.  Se o textSplitMode for definido como "pages", o algoritmo tentará dividir o texto em partes com um tamanho de, no máximo, "maximumPageLength". Nesse caso, o algoritmo fará o melhor para quebrar a frase em um limite de orações, de modo que o tamanho da parte possa ser um pouco menor que "maximumPageLength". | 
| defaultLanguageCode   | (opcional) Um dos seguintes códigos de idioma: `da, de, en, es, fi, fr, it, ko, pt`. O padrão é inglês (en). Algumas coisas para levar em consideração:<ul><li>Se você passar um formato languagecode-countrycode, somente a parte languagecode do formato é usada.</li><li>Se o idioma não estiver na lista anterior, a habilidade de divisão quebra o texto em limites de caractere.</li><li>Fornecer um código de idioma é útil para evitar cortar uma palavra ao meio para línguas não brancas, como chinês, japonês e coreano.</li><li>Se você não conhece o idioma (ou seja, você precisa dividir o texto para entrada no [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), o padrão de inglês (en) deve ser suficiente. </li></ul>  |


## <a name="skill-inputs"></a>Entradas de habilidades

| Nome do parâmetro       | Descrição      |
|----------------------|------------------|
| text  | O texto a ser dividido em subcadeias. |
| languageCode  | (opcional) Código de idioma para o documento. Se você não conhece o idioma (ou seja, você precisa dividir o texto para entrada no [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), é seguro remover essa entrada.  |

## <a name="skill-outputs"></a>Saídas de habilidades 

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| textItems | Uma matriz de subcadeias de caracteres que foram extraídos. |


##  <a name="sample-definition"></a>Definição de exemplo

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
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
            "data": {
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
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
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Casos de erro
Se não há suporte para um idioma, um aviso será gerado e o texto é dividido em limites de caractere.

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
