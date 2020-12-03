---
title: Usar o reconhecimento de entidade com a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Saiba como identificar e desambiguar a identidade de uma entidade encontrada em texto com a API REST do Análise de Texto.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 804d739efa5ac96c0b2d7228573f031f324e590e
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558973"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Como usar o reconhecimento de entidade nomeada no Análise de Texto

O API de Análise de Texto permite que você assuma o texto não estruturado e retorne uma lista de entidades desambiguadas, com links para mais informações na Web. A API dá suporte ao reconhecimento de entidade nomeada (NER) para várias categorias de entidade e vinculação de entidade.

## <a name="entity-linking"></a>Vinculação de Identidade

A vinculação de entidades é a capacidade de identificar e desambiguar a identidade de uma entidade encontrada em texto (por exemplo, determinar se uma ocorrência da palavra "Mars" refere-se ao planeta ou ao Deus romano de guerra). Esse processo requer a presença de uma base de dados de conhecimento em um idioma apropriado para vincular entidades reconhecidas em texto. A vinculação de entidades usa a [Wikipédia](https://www.wikipedia.org/) como esta base de dados de conhecimento.

## <a name="named-entity-recognition-ner"></a>NER (Reconhecimento de Entidade Nomeada)

O NER (reconhecimento de entidade nomeada) é a capacidade de identificar diferentes entidades no texto e categorizá-las em classes predefinidas ou tipos como: pessoa, local, evento, produto e organização.  

## <a name="personally-identifiable-information-pii"></a>PII (Informações de Identificação Pessoal)

O recurso PII faz parte do NER e pode identificar e redação entidades confidenciais no texto associado a uma pessoa individual, como: número de telefone, endereço de email, endereço para correspondência, número do passaporte.

## <a name="named-entity-recognition-features-and-versions"></a>Recursos e versões de reconhecimento de entidade nomeada

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Recurso                                                         | NER v 3.0 | NER v 3.1-Preview. 3 |
|-----------------------------------------------------------------|--------|----------|
| Métodos para solicitações únicas e em lote                          | X      | X        |
| Reconhecimento de entidade expandido em várias categorias           | X      | X        |
| Separe os pontos de extremidade para enviar solicitações de vinculação e NER de entidade. | X      | X        |
| Reconhecimento de entidades de `PII` informações pessoais () e de integridade ( `PHI` )        |        | X        |
| Edição de `PII`        |        | X        |

Consulte [suporte a idiomas](../language-support.md) para obter informações.

O reconhecimento de entidade nomeada v3 fornece detecção expandida entre vários tipos. Atualmente, o NER v 3.0 pode reconhecer entidades na [categoria de entidade geral](../named-entity-types.md).

Reconhecimento de entidade nomeada v 3.1-Preview. 3 inclui os recursos de detecção do v 3.0 e: 
* A capacidade de detectar informações pessoais ( `PII` ) usando o `v3.1-preview.3/entities/recognition/pii` ponto de extremidade. 
* Um `domain=phi` parâmetro opcional para detectar informações de integridade confidencial ( `PHI` ).
* [Operação assíncrona](text-analytics-how-to-call-api.md) usando o `/analyze` ponto de extremidade.

Para obter mais informações, consulte o artigo [categorias de entidade](../named-entity-types.md) e a seção pontos de [extremidade de solicitação](#request-endpoints) abaixo. 

## <a name="sending-a-rest-api-request"></a>Como enviar uma solicitação da API REST

### <a name="preparation"></a>Preparação

Você deve ter documentos JSON neste formato: ID, texto, idioma.

Cada documento deve ter menos de 5.120 caracteres, e você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo da solicitação.

### <a name="structure-the-request"></a>Estruturar a solicitação

Crie uma solicitação POST. Você pode [usar o postmaster](text-analytics-how-to-call-api.md) ou o **console de teste de API** nos links a seguir para estruturar e enviar rapidamente um. 

> [!NOTE]
> Você pode encontrar sua chave e ponto de extremidade para o recurso da Análise de Texto no portal do Azure. Eles estarão localizados na página de **Início rápido** do recurso, em **Gerenciamento de recursos**. 


### <a name="request-endpoints"></a>Pontos de extremidade de solicitação

#### <a name="version-31-preview3"></a>[Versão 3,1-Preview. 3](#tab/version-3-preview)

O reconhecimento de entidade nomeada `v3.1-preview.3` usa pontos de extremidade separados para Ner, PII e solicitações de vinculação de entidade. Use um formato de URL abaixo com base em sua solicitação.

**Vinculação de entidade**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/linking`

[Versão de reconhecimento de entidade nomeada 3,1-referência de visualização para `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesLinking)

**Reconhecimento de entidade nomeada**
* Entidades gerais- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/general`

[Versão de reconhecimento de entidade nomeada 3,1-referência de visualização para `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionGeneral)

**PII (Informações de Identificação Pessoal)**
* Informações pessoais ( `PII` )- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii`

Você também pode usar o `domain=phi` parâmetro opcional para detectar `PHI` informações de integridade () em texto. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi`

A partir do `v3.1-preview.3` , a resposta JSON inclui uma `redactedText` propriedade, que contém o texto de entrada modificado em que as entidades PII detectadas são substituídas por um `*` para cada caractere nas entidades.

[Versão de reconhecimento de entidade nomeada 3,1-referência de visualização para `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionPii)

#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

O reconhecimento de entidade nomeada v3 usa pontos de extremidade separados para solicitações de vinculação de NER e entidade. Use um formato de URL abaixo com base em sua solicitação:

**Vinculação de entidade**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Referência de reconhecimento de entidade nomeada versão 3,0 para `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**Reconhecimento de entidade nomeada**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Referência de reconhecimento de entidade nomeada versão 3,0 para `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Defina um cabeçalho de solicitação para incluir sua chave de API de Análise de Texto. No corpo da solicitação, forneça os documentos JSON que você preparou.

### <a name="example-ner-request"></a>Exemplo de solicitação NER 

O JSON a seguir é um exemplo de conteúdo que você pode enviar para a API. O formato da solicitação é o mesmo para as duas versões da API.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}

```

## <a name="post-the-request"></a>Postar a solicitação

A análise é executada após o recebimento da solicitação. Consulte a seção [limites de dados](../overview.md#data-limits) na visão geral para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo.

A API de Análise de Texto é sem estado. Nenhum dado é armazenado em sua conta e os resultados são retornados imediatamente na resposta.

## <a name="view-results"></a>Exibir os resultados

Todas as solicitações POST retornam uma resposta formatada em JSON com as IDs e as propriedades de entidade detectadas.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local e, em seguida, importá-lo para um aplicativo que permite que você classifique, pesquise e manipule os dados. Devido ao suporte multilíngue e a emojis, a resposta pode conter deslocamentos de texto. Para obter mais informações, consulte [como processar deslocamentos de texto](../concepts/text-offsets.md).

### <a name="example-responses"></a>Respostas de exemplo

A versão 3 fornece pontos de extremidade separados para NER geral, PII e vinculação de entidade. As respostas para ambas as operações estão abaixo. 

#### <a name="version-31-preview"></a>[Versão 3.1 – versão prévia](#tab/version-3-preview)

Exemplo de uma resposta de PII:
```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

Exemplo de uma resposta de vinculação de entidade:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```


#### <a name="version-30"></a>[Versão 3.0](#tab/version-3)

Exemplo de uma resposta geral do NER:
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
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

Neste artigo, você aprendeu os conceitos e fluxo de trabalho para detecção de idioma usando a Análise de Texto em Serviços Cognitivos. Em resumo:

* Documentos JSON no corpo da solicitação incluem um código de idioma, texto e ID.
* As solicitações POST são enviadas para um ou mais pontos de extremidade, usando uma [chave de acesso personalizada e um ponto de extremidade](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para sua assinatura.
* A saída da resposta, composta por entidades vinculadas (incluindo pontuações de confiança, deslocamentos e links da Web, para cada ID de documento) pode ser usada em qualquer aplicativo

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da Análise de Texto](../overview.md)
* [Como usar a biblioteca de clientes da Análise de Texto](../quickstarts/text-analytics-sdk.md)
* [Novidades](../whats-new.md)
