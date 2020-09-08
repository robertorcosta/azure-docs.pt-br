---
title: Analisar o sentimento com a API REST de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como detectar sentimentos em texto com os Serviços Cognitivos da API REST de Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 08/25/2020
ms.author: aahi
ms.openlocfilehash: a0557c3ccf6510ab3ee2ae29cbef1fc754473345
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933011"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Como fazer: Detectar o sentimento usando a API de Análise de Texto

O recurso Análise de Sentimento da API de Análise de Texto avalia o texto e retorna pontuações e rótulos de sentimentos para cada frase. Isso é útil para detectar o sentimento positivo e negativo em fóruns de discussão, revisões de cliente e mídia social, entre outros. Os modelos de IA usados pela API são fornecidos pelo serviço; basta enviar o conteúdo para análise.

Depois de enviar uma solicitação de análise de sentimento, a API retorna rótulos de sentimento (como "negativo", "neutro" e "positivo") e pontuações de confiança no nível da frase e do documento.

A Análise de Sentimento dá suporte a uma ampla variedade de idiomas, com mais opções na versão prévia. Para obter mais informações, consulte [Linguagens com suporte](../text-analytics-supported-languages.md).

## <a name="sentiment-analysis-versions-and-features"></a>Versões e recursos da Análise de Sentimento

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Recurso                                   | Análise de Sentimento v3 | Análise de Sentimento v3.1 (versão prévia) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Métodos para solicitações únicas e em lote    | X                     | X                                 |
| Pontuações e rotulagem de sentimentos             | X                     | X                                 |
| [Contêiner do Docker](text-analytics-how-to-install-containers.md) baseado em Linux | X  |  |
| Mineração de opinião                            |                       | X                                 |

### <a name="sentiment-scoring-and-labeling"></a>Pontuação e rotulagem de sentimentos

A Análise de Sentimento na v3 aplica rótulos de sentimento a um texto, que são retornados em um nível da frase e do documento, com uma pontuação de confiança para cada um. 

Os rótulos são *positivos*, *negativos* e *neutros*. No nível do documento, o rótulo de sentimento *misto* também pode ser retornado. O sentimento do documento é determinado abaixo:

| Sentimento da frase                                                                            | Rótulo do documento retornado |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Há, pelo menos, uma frase `positive` no documento. O restante das frases é `neutral`. | `positive`              |
| Há, pelo menos, uma frase `negative` no documento. O restante das frases é `neutral`. | `negative`              |
| Há, pelo menos, uma frase `negative` e uma frase `positive` no documento.    | `mixed`                 |
| Todas as frases do documento são `neutral`.                                                  | `neutral`               |

As pontuações de confiança vão de 1 a 0. As pontuações mais próximas a 1 indicam uma maior confiança na classificação do rótulo, enquanto as pontuações inferiores indicam uma menor confiança. Para cada documento ou cada frase, as pontuações previstas associadas aos rótulos (positivo, negativo e neutro) somam 1.

### <a name="opinion-mining"></a>Mineração de opinião

A mineração de opinião é um recurso da Análise de Sentimento na versão 3.1-preview.1 em diante. Também conhecida como Análise de Sentimento baseada em aspecto no NLP (processamento de idioma natural), esse recurso fornece informações mais granulares sobre as opiniões relacionadas a aspectos (como os atributos de produtos ou serviços) no texto.

Por exemplo, se um cliente deixar comentários sobre um hotel, como "o quarto era excelente, mas a equipe foi antipática", a mineração de opinião localizará aspectos no texto e as respectivas opiniões e sentimentos associados:

| Aspecto | Opinião    | Sentimento |
|--------|------------|-----------|
| quarto   | excelente      | positivo  |
| staff  | antipática | negativo  |

Para incluir a mineração de opinião nos resultados, inclua o sinalizador `opinionMining=true` em uma solicitação de análise de sentimento. Os resultados da mineração de opinião serão incluídos na resposta da análise de sentimento.

## <a name="sending-a-rest-api-request"></a>Como enviar uma solicitação da API REST 

### <a name="preparation"></a>Preparação

A Análise de Sentimento produz um resultado de maior qualidade quando você fornece partes menores de texto nas quais ela trabalhará. Este é o oposto da extração de frase-chave que executa melhor em grandes blocos de texto. Para obter os melhores resultados de ambas as operações, considere a reestruturação de entradas adequadamente.

Você deve ter documentos JSON neste formato: ID, texto e idioma.

O tamanho do documento deve ser inferior a 5.120 caracteres. Você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo da solicitação.

## <a name="structure-the-request"></a>Estruturar a solicitação

Crie uma solicitação POST. Você pode [usar o Postman](text-analytics-how-to-call-api.md) ou o **console de teste da API** nos links de referência a seguir para estruturar e enviar uma rapidamente. 

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

[Referência da Análise de Sentimento v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

#### <a name="version-31-preview1"></a>[Versão 3.1-preview.1](#tab/version-3-1)

[Referência da Análise de Sentimento v3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Sentiment)

---

### <a name="request-endpoints"></a>Pontos de extremidade de solicitação

Defina o ponto de extremidade HTTPS para a Análise de Sentimento usando um recurso da Análise de Texto no Azure ou um [contêiner da Análise de Texto](text-analytics-how-to-install-containers.md) instanciado. É necessário incluir a URL correta para a versão que você deseja usar. Por exemplo:

> [!NOTE]
> Você pode encontrar sua chave e ponto de extremidade para o recurso da Análise de Texto no portal do Azure. Eles estarão localizados na página de **Início rápido** do recurso, em **Gerenciamento de recursos**. 

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

#### <a name="version-31-preview1"></a>[Versão 3.1-preview.1](#tab/version-3-1)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment`

Para obter os resultados da mineração de opinião, inclua o parâmetro `opinionMining=true`. Por exemplo:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment?opinionMining=true`

Esse parâmetro é definido como `false` por padrão. 

---

Defina um cabeçalho de solicitação para incluir sua chave de API de Análise de Texto. No corpo da solicitação, forneça a coleção de documentos JSON preparada para esta análise

### <a name="example-sentiment-analysis-request"></a>Solicitação de exemplo da Análise de Sentimento 

Este é um exemplo de conteúdo que você pode enviar para a detecção de sentimento. O formato da solicitação é o mesmo nas duas versões.
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>Postar a solicitação

A análise é executada após o recebimento da solicitação. Para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo, confira a seção [Limites de dados](../overview.md#data-limits) na visão geral.

A API de Análise de Texto é sem estado. Nenhum dado é armazenado em sua conta e os resultados são retornados imediatamente na resposta.


### <a name="view-the-results"></a>Exibir os resultados

A análise de sentimento retorna um rótulo de sentimento e uma pontuação de confiança para todo o documento e cada frase dentro dele. As pontuações mais próximas a 1 indicam uma maior confiança na classificação do rótulo, enquanto as pontuações inferiores indicam uma menor confiança. Um documento pode ter várias frases, e as pontuações de confiança dentro de cada documento ou frase somam 1.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local. Em seguida, importe a saída para um aplicativo que você possa usar para classificar, pesquisar e manipular os dados. Devido ao suporte multilíngue e a emojis, a resposta pode conter deslocamentos de texto. Confira [como processar deslocamentos](../concepts/text-offsets.md) para obter mais informações.

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

### <a name="sentiment-analysis-v30-example-response"></a>Exemplo de resposta da Análise de Sentimento v3.0

As respostas da Análise de Sentimento v3 contêm rótulos e pontuações de sentimentos para cada frase e documento analisado.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-31-preview1"></a>[Versão 3.1-preview.1](#tab/version-3-1)

### <a name="sentiment-analysis-v31-example-response"></a>Exemplo de resposta da Análise de Sentimento v3.1

A Análise de Sentimento v3.1 oferece a mineração de opinião além do objeto de resposta na guia **Versão 3.0**. Na resposta abaixo, a frase *O restaurante oferecia uma ótima comida e nosso garçom foi simpático* tem dois aspectos: *comida* e *garçom*. A propriedade `relations` de cada aspecto contém um valor `ref` com a referência de URI aos objetos `documents`, `sentences` e `opinions` associados.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>Resumo

Neste artigo, você conheceu os conceitos e o fluxo de trabalho para a análise de sentimento usando a API de Análise de Texto. Em resumo:

+ A Análise de Sentimento está disponível em idiomas seletos.
+ Documentos JSON no corpo da solicitação incluem um código de idioma, texto e ID.
+ A solicitação POST é feita para um ponto de extremidade `/sentiment` usando uma [chave de acesso personalizada e um ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que é válido para sua assinatura.
+ Saída de resposta, que pontuação de sentimento para cada ID do documento, pode ser transmitida para qualquer aplicativo que aceita JSON. Por exemplo, Excel e Power BI.

## <a name="see-also"></a>Confira também

* [Visão geral da Análise de Texto](../overview.md)
* [Como usar a biblioteca de clientes da Análise de Texto](../quickstarts/text-analytics-sdk.md)
* [Novidades](../whats-new.md)
