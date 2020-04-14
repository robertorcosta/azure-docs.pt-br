---
title: Moderação de texto – Content Moderator
titleSuffix: Azure Cognitive Services
description: Use moderação de texto para possíveis textos indesejados, dados pessoais e listas personalizadas de termos.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 41e88dd5a08de485f770559959843ba3b54e590f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274003"
---
# <a name="learn-text-moderation-concepts"></a>Aprender os conceitos de moderação de texto

Use a moderação de texto assistida por máquina do Moderador de conteúdo e os recursos de [revisão humana](Review-Tool-User-Guide/human-in-the-loop.md) para moderar o conteúdo do texto.

Você bloqueia, aprova ou analisa o conteúdo com base nas políticas e limites. Use-o para aumentar a moderação humana de ambientes em que parceiros, funcionários e consumidores geram conteúdo de texto. Isso inclui salas de chat, fóruns de discussão, chatbots, catálogos de comércio eletrônico e documentos. 

A resposta de serviço inclui as informações a seguir:

- Conteúdo ofensivo: correspondência baseada em termos com lista interna de termos ofensivos em vários idiomas
- Classificação: classificação assistida por computador em três categorias
- Dados pessoais
- Texto corrigido automaticamente
- Texto original
- Linguagem

## <a name="profanity"></a>Conteúdo ofensivo

Se a API detectar termos ofensivos em qualquer um dos [idiomas com suporte](Text-Moderation-API-Languages.md), esses termos serão incluídos na resposta. A resposta também contém a localização (`Index`) no texto original. O `ListId` no JSON de exemplo a seguir refere-se aos termos encontrados nas [listas de termos personalizadas](try-terms-list-api.md), se disponíveis.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Para o parâmetro **language**, atribua `eng` ou deixe vazio para ver a resposta de **classificação** assistida por computador (recurso de visualização). **Esse recurso dá suporte ao inglês apenas**.
>
> Para a detecção de **termos obscenos**, use o [código ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) dos idiomas com suporte listados neste artigo ou deixe vazio.

## <a name="classification"></a>classificação

O recurso de **classificação** de texto assistido por máquina do Moderador suporta **apenas o inglês**e ajuda a detectar conteúdo potencialmente indesejado. O conteúdo sinalizado pode ser avaliado como inadequado, dependendo do contexto. Ele transmite a probabilidade de cada categoria e pode recomendar uma análise humana. O recurso utiliza um modelo treinado para identificar uma possível linguagem abusiva, depreciativa ou discriminatória. Isso inclui gírias, palavras abreviadas, palavras ofensivas e intencionalmente incorretas para a análise. 

A extração a seguir na extração JSON mostra uma saída de exemplo:

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
              "Score": 1.5113095059859916E-06
            },
        "Category2": {
              "Score": 0.12747249007225037
            },
        "Category3": {
              "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>Explicação

- `Category1` refere-se à presença potencial de linguagem que pode ser considerada sexualmente explícita ou para adulto em determinadas situações.
- `Category2` refere-se à presença potencial de linguagem que pode ser considerada sexualmente sugestiva ou para adulto em determinadas situações.
- `Category3` refere-se à potencial presença de linguagem que pode ser considerada ofensiva em determinadas situações.
- `Score` fica entre 0 e 1. Quanto maior a pontuação, mais o modelo irá prever que a categoria pode ser aplicável. Esse recurso se baseia em um modelo estatístico, em vez dos resultados codificados manualmente. É recomendável testar com seu próprio conteúdo para determinar como cada categoria se alinha com seus requisitos.
- `ReviewRecommended` é verdadeiro ou falso, dependendo dos limites internos da pontuação. Os clientes devem avaliar se é para usar esse valor ou escolher limites personalizados com base nas políticas de conteúdo.

## <a name="personal-data"></a>Dados pessoais

O recurso de dados pessoais detecta a presença potencial dessas informações:

- Endereço de email
- Endereço de e-mail dos EUA
- Endereço IP
- Número de telefone dos EUA

O exemplo a seguir mostra uma resposta de exemplo:

```json
"pii":{
  "email":[
      {
        "detected":"abcdef@abcd.com",
        "sub_type":"Regular",
        "text":"abcdef@abcd.com",
        "index":32
      }
  ],
  "ssn":[

  ],
  "ipa":[
      {
        "sub_type":"IPV4",
        "text":"255.255.255.255",
        "index":72
      }
  ],
  "phone":[
      {
        "country_code":"US",
        "text":"6657789887",
        "index":56
      }
  ],
  "address":[
      {
        "text":"1 Microsoft Way, Redmond, WA 98052",
        "index":89
      }
  ]
}
```

## <a name="auto-correction"></a>Correção automática

Suponha que o texto de entrada seja (o 'lzay' e 'f0x' são intencionais):

    The qu!ck brown f0x jumps over the lzay dog.

Se você solicitar correção automática, a resposta conterá a versão corrigida do texto:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Criar e gerenciar as listas de termos personalizadas

Embora a lista global de termos padrão funcione muito bem na maioria dos casos, convém filtrar os termos específicos das necessidades da sua empresa. Por exemplo, é possível querer filtrar qualquer marca competitiva das postagens dos usuários.

> [!NOTE]
> Há um limite máximo de **5 listas de termos** com cada lista para **não exceder 10.000 termos**.
>

O exemplo a seguir mostra a ID da lista correspondente:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

O Content Moderator fornece uma [API de Lista de Termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) com operações para gerenciar listas de termos personalizadas. Inicie com o [Console de API de Listas de Termos](try-terms-list-api.md) e use os exemplos de código de API REST. Consulte também o [Início rápido do .NET das Listas de Termos](term-lists-quickstart-dotnet.md), se você estiver familiarizado com Visual Studio e C#.

## <a name="next-steps"></a>Próximas etapas

Faça test drive do [Console de API de moderação de texto](try-text-api.md) e use os exemplos de código de API REST. Confira também a seção de moderação de texto do [.NET SDK quickstart](dotnet-sdk-quickstart.md) se você estiver familiarizado com visual studio e C#.
