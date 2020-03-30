---
title: Habilidade cognitiva de Tradução de Texto
titleSuffix: Azure Cognitive Search
description: Avalia o texto e, para cada registro, retorna o texto traduzido para o idioma de destino especificado em um pipeline de enriquecimento de IA na Pesquisa Cognitiva do Azure.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5089174fcfd5a97128c1f789b818243243a5282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460761"
---
#   <a name="text-translation-cognitive-skill"></a>Habilidade cognitiva de Tradução de Texto

A habilidade **de Tradução** de Texto avalia o texto e, para cada registro, retorna o texto traduzido para o idioma de destino especificado. Esta habilidade utiliza a [API de texto tradutor v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) disponível em Serviços Cognitivos.

Esse recurso é útil se você espera que seus documentos não estejam todos em um idioma, nesse caso você pode normalizar o texto para um único idioma antes de indexar para pesquisa traduzindo-o.  Também é útil para casos de uso de localização, onde você pode querer ter cópias do mesmo texto disponíveis em vários idiomas.

A [API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) do Tradutor texto é um serviço cognitivo não regional, o que significa que seus dados não são garantidos para permanecer na mesma região que o seu Azure Cognitive Search ou recurso de Serviços Cognitivos anexados.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem, como parte do estágio de quebra de documento na Pesquisa Cognitiva do Azure. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço da extração de imagem é descrito na [página de preços da Pesquisa Cognitiva do Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser de 50.000 caracteres, medido por [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se você precisar dividir seus dados antes de enviá-los para a habilidade de tradução de texto, considere usar a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Entradas                | Descrição |
|---------------------|-------------|
| padrãoToLanguageCode | (Obrigatório) O código de idioma para traduzir documentos para documentos que não especificam o idioma explicitamente. <br/> Consulte [Lista completa dos idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| defaultFromLanguageCode | (Opcional) O código de idioma para traduzir documentos de documentos que não especificam o idioma explicitamente.  Se o padrãoFromLanguageCode não for especificado, a detecção automática de idioma fornecida pela API de texto tradutor será usada para determinar o idioma. <br/> Consulte [Lista completa dos idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| suggestedFrom | (Opcional) O código de idioma para traduzir documentos de quando nem a entrada do LanguageCode nem o parâmetro padrãoFromLanguageCode são fornecidos, e a detecção automática de idiomas não é bem sucedida.  Se o idioma sugeridoNão for especificado, o inglês (en) será usado como o idioma sugeridoDe. <br/> Consulte [Lista completa dos idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada     | Descrição |
|--------------------|-------------|
| text | O texto a ser traduzido.|
| toLanguageCode    | Uma seqüência indicando o idioma para o idioma para o que o texto deve ser traduzido. Se essa entrada não for especificada, o padrãoToLanguageCode será usado para traduzir o texto. <br/>Veja [a lista completa de idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| fromLanguageCode  | Uma seqüência indicando a linguagem atual do texto. Se esse parâmetro não for especificado, o padrãoFromLanguageCode (ou detecção automática de idiomas se o padrãoFromLanguageCode não for fornecido) será usado para traduzir o texto. <br/>Veja [a lista completa de idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída    | Descrição |
|--------------------|-------------|
| translatedText | O resultado da seqüência de texto da tradução de texto do translatedFromLanguageCode para o translatedToLanguageCode.|
| translatedToLanguageCode  | Uma seqüência indicando o código do idioma para o que o texto foi traduzido. Útil se você estiver traduzindo para vários idiomas e quiser ser capaz de acompanhar qual texto é qual idioma.|
| traduzidoFromLanguageCode    | Uma seqüência indicando o código do idioma do que o texto foi traduzido. Útil se você optou pela opção de detecção automática de linguagem, pois esta saída lhe dará o resultado dessa detecção.|

##  <a name="sample-definition"></a>Definição de exemplo

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
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
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Erros e avisos
Se você fornecer um código de idioma não suportado para o idioma de ou para o idioma, um erro será gerado e o texto não será traduzido.
Se o texto estiver vazio, um aviso será gerado.
Se o seu texto for maior que 50.000 caracteres, apenas os primeiros 50.000 caracteres serão traduzidos e um aviso será emitido.

## <a name="see-also"></a>Confira também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
