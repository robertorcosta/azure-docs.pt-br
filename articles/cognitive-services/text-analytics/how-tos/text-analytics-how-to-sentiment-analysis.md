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
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 214c071e0d01908e2d46c932fcf87906de834102
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644674"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Como fazer: Detectar o sentimento usando a API de Análise de Texto

O recurso Análise de Sentimento da API de Análise de Texto avalia o texto e retorna pontuações e rótulos de sentimentos para cada frase. Isso é útil para detectar o sentimento positivo e negativo em fóruns de discussão, revisões de cliente e mídia social, entre outros. Os modelos de IA usados pela API são fornecidos pelo serviço; basta enviar o conteúdo para análise.

> [!TIP]
> A Análise de Texto também fornece um Docker baseado em imagem de contêiner do Linux para detecção de idioma, para que você possa [instalar e executar o contêiner de análise de texto](text-analytics-how-to-install-containers.md) perto de seus dados.

A Análise de Sentimento dá suporte a uma ampla variedade de idiomas, com mais opções na versão prévia. Para obter mais informações, consulte [Linguagens com suporte](../text-analytics-supported-languages.md).

## <a name="concepts"></a>Conceitos

A API de Análise de Texto usa um algoritmo de classificação de aprendizado de máquina para gerar uma pontuação de sentimento entre 0 e 1. As pontuações mais próximas de 1 indicam sentimento positivo, enquanto as classificações mais próximas de 0 indicam sentimento negativo.” A Análise de Sentimento é executada em todo o documento, em vez de entidades individuais no texto. Isso significa que as pontuações de sentimentos são retornadas em um nível de documento ou frase. 

O modelo usado é pré-treinado com um amplo corpus de associações de texto e sentimentos. Ele utiliza uma combinação de técnicas para a análise, incluindo processamento de texto, análise de classe gramatical, posicionamento e associações de palavras. Para obter mais informações sobre o algoritmo, consulte [Apresentando Análise de Texto do Azure Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/). Atualmente, não é possível fornecer seus próprios dados de treinamento. 

Há uma tendência de melhoria na precisão de pontuação quando os documentos contêm menos frases em vez de um grande bloco de texto. Durante a fase de avaliação objetividade, o modelo determina se um documento como um todo é objetivo ou contém sentimento. Um documento que é principalmente objetivo não faz progresso para a frase de detecção de sentimento, resultando em uma pontuação 0,50, sem nenhum processamento adicional. Para documentos que continuam no pipeline, a próxima fase gera uma pontuação acima ou abaixo de 0,50. A pontuação depende do grau de sentimento detectado no documento.

## <a name="sentiment-analysis-versions-and-features"></a>Versões e recursos da Análise de Sentimento

A API de Análise de Texto oferece duas versões da Análise de Sentimento: v2 e v3. A Análise de Sentimento v3 (versão prévia pública) fornece melhorias significativas na precisão e nos detalhes da pontuação e da categorização de texto da API.

> [!NOTE]
> * O formato de solicitação e os [limites de dados](../overview.md#data-limits) da Análise de Sentimento v3 são os mesmos que os da versão anterior.
> * A Análise de Sentimento v3 está disponível nas seguintes regiões: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe` e `West US 2`.

| Recurso                                   | Análise de Sentimento v2 | Análise de Sentimento v3 |
|-------------------------------------------|-----------------------|-----------------------|
| Métodos para solicitações únicas e em lote    | X                     | X                     |
| Pontuações de sentimentos para o documento inteiro  | X                     | X                     |
| Pontuações de sentimentos para frases individuais |                       | X                     |
| Rotulação de sentimento                        |                       | X                     |
| Controle de versão de modelo                   |                       | X                     |

#### <a name="version-2tabversion-2"></a>[Versão 2](#tab/version-2)

### <a name="sentiment-scoring"></a>Pontuação de sentimento

O analisador de sentimento classifica texto como predominantemente positivo ou negativo. Ele atribui uma pontuação no intervalo de 0 a 1. Valores próximos 0,5 são neutros ou indeterminados. Uma pontuação de 0,5 indica neutralidade. Quando uma cadeia de caracteres não pode ser analisada para sentimento ou não tem nenhum sentimento, a pontuação é sempre exatamente 0,5. Por exemplo, se você passar uma cadeia de caracteres espanhol com um código de idioma inglês, a pontuação é 0,5.


#### <a name="version-3-public-previewtabversion-3"></a>[Versão 3 (versão prévia pública)](#tab/version-3)

### <a name="sentiment-scoring"></a>Pontuação de sentimento

A Análise de Sentimento v3 classifica o texto com rótulos de sentimentos (descritos abaixo). As pontuações retornadas representam a confiança do modelo de que o texto é positivo, negativo ou neutro. Valores mais altos significam uma maior confiança. 

### <a name="sentiment-labeling"></a>Rotulação de sentimento

A Análise de Sentimento v3 pode retornar pontuações e rótulos no nível do documento e da frase. As pontuações e os rótulos são `positive`, `negative` e `neutral`. No nível do documento, o rótulo de sentimento `mixed` também pode ser retornado sem uma pontuação. O sentimento do documento é determinado abaixo:

| Sentimento da frase                                                                            | Rótulo do documento retornado |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Há, pelo menos, uma frase `positive` no documento. O restante das frases é `neutral`. | `positive`              |
| Há, pelo menos, uma frase `negative` no documento. O restante das frases é `neutral`. | `negative`              |
| Há, pelo menos, uma frase `negative` e uma frase `positive` no documento.    | `mixed`                 |
| Todas as frases do documento são `neutral`.                                                  | `neutral`               |

### <a name="model-versioning"></a>Controle de versão de modelo

> [!NOTE]
> O controle de versão de modelo para análise de sentimentos está disponível a partir da versão `v3.0-preview.1`.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>Código C# de exemplo

Você pode encontrar um aplicativo em C# de exemplo que chama esta versão da Análise de Sentimento no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs).

---

## <a name="sending-a-rest-api-request"></a>Como enviar uma solicitação da API REST 

### <a name="preparation"></a>Preparação

A Análise de Sentimento produz um resultado de maior qualidade quando você fornece partes menores de texto nas quais ela trabalhará. Este é o oposto da extração de frase-chave que executa melhor em grandes blocos de texto. Para obter os melhores resultados de ambas as operações, considere a reestruturação de entradas adequadamente.

Você deve ter documentos JSON neste formato: ID, texto e idioma.

O tamanho do documento deve ser inferior a 5.120 caracteres. Você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo da solicitação.

## <a name="structure-the-request"></a>Estruturar a solicitação

Crie uma solicitação POST. Você pode [usar o Postman](text-analytics-how-to-call-api.md) ou o **console de teste da API** nos links de referência a seguir para estruturar e enviar uma rapidamente. 

#### <a name="version-2tabversion-2"></a>[Versão 2](#tab/version-2)

[Referência da Análise de Sentimento v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

#### <a name="version-3-public-previewtabversion-3"></a>[Versão 3 (versão prévia pública)](#tab/version-3)

[Referência da Análise de Sentimento v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

---

Defina o ponto de extremidade HTTPS para a Análise de Sentimento usando um recurso da Análise de Texto no Azure ou um [contêiner da Análise de Texto](text-analytics-how-to-install-containers.md) instanciado. É necessário incluir a URL correta para a versão que você deseja usar. Por exemplo:
    
[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

#### <a name="version-2tabversion-2"></a>[Versão 2](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

#### <a name="version-3-public-previewtabversion-3"></a>[Versão 3 (versão prévia pública)](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

---

Defina um cabeçalho de solicitação para incluir sua chave de API de Análise de Texto. No corpo da solicitação, forneça a coleção de documentos JSON preparada para esta análise

### <a name="example-sentiment-analysis-request"></a>Solicitação de exemplo da Análise de Sentimento 

Este é um exemplo de conteúdo que você pode enviar para a detecção de sentimento. O formato da solicitação é o mesmo para as duas versões da API.
    
```json
{
    "documents": [
    {
        "language": "en",
        "id": "1",
        "text": "Hello world. This is some input text that I love."
    },
    {
        "language": "en",
        "id": "2",
        "text": "It's incredibly sunny outside! I'm so happy."
    }
    ],
}
```

### <a name="post-the-request"></a>Postar a solicitação

A análise é executada após o recebimento da solicitação. Para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo, confira a seção [Limites de dados](../overview.md#data-limits) na visão geral.

A API de Análise de Texto é sem estado. Nenhum dado é armazenado em sua conta e os resultados são retornados imediatamente na resposta.


### <a name="view-the-results"></a>Exibir os resultados

O analisador de sentimento classifica texto como predominantemente positivo ou negativo. Ele atribui uma pontuação no intervalo de 0 a 1. Valores próximos 0,5 são neutros ou indeterminados. Uma pontuação de 0,5 indica neutralidade. Quando uma cadeia de caracteres não pode ser analisada para sentimento ou não tem nenhum sentimento, a pontuação é sempre exatamente 0,5. Por exemplo, se você passar uma cadeia de caracteres espanhol com um código de idioma inglês, a pontuação é 0,5.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local. Em seguida, importe a saída para um aplicativo que você possa usar para classificar, pesquisar e manipular os dados.

#### <a name="version-2tabversion-2"></a>[Versão 2](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>Resposta de exemplo da Análise de Sentimento v2

As respostas da Análise de Sentimento v2 contêm pontuações de sentimentos para cada documento enviado.

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

#### <a name="version-3-public-previewtabversion-3"></a>[Versão 3 (versão prévia pública)](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>Exemplo de resposta da Análise de Sentimento v3

As respostas da Análise de Sentimento v3 contêm rótulos e pontuações de sentimentos para cada frase e documento analisado. `documentScores` não será retornado se o rótulo de sentimento do documento for `mixed`.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```
---

## <a name="summary"></a>Resumo

Neste artigo, você conheceu os conceitos e o fluxo de trabalho para a análise de sentimento usando a API de Análise de Texto. Em resumo:

+ A Análise de Sentimento está disponível para os idiomas selecionados em duas versões.
+ Documentos JSON no corpo da solicitação incluem um código de idioma, texto e ID.
+ A solicitação POST é feita para um ponto de extremidade `/sentiment` usando uma [chave de acesso personalizada e um ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que é válido para sua assinatura.
+ Saída de resposta, que pontuação de sentimento para cada ID do documento, pode ser transmitida para qualquer aplicativo que aceita JSON. Por exemplo, Excel e Power BI.

## <a name="see-also"></a>Confira também

* [Visão geral da Análise de Texto](../overview.md)
* [Como usar a biblioteca de clientes da Análise de Texto](../quickstarts/text-analytics-sdk.md)
* [Novidades](../whats-new.md)
