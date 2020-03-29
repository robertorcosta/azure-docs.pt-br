---
title: Usar o reconhecimento de entidade com a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Saiba como identificar e desambiguizar a identidade de uma entidade encontrada no texto com a API Rest do Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79203484"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Como usar o reconhecimento de entidade nomeado no Text Analytics

A API text analytics permite que você pegue texto não estruturado e retorne uma lista de entidades desamparadas, com links para mais informações na web. A API apoia tanto o reconhecimento de entidades (NER) quanto a vinculação de entidades.

### <a name="entity-linking"></a>Vinculação de Identidade

A vinculação de entidades é a capacidade de identificar e desambigar a identidade de `Mars` uma entidade encontrada no texto (por exemplo, determinar se uma ocorrência da palavra se refere ao planeta, ou ao deus romano da guerra). Esse processo requer a presença de uma base de conhecimento em uma linguagem apropriada, para vincular entidades reconhecidas em texto. Entity Linking usa [a Wikipédia](https://www.wikipedia.org/) como base de conhecimento.


### <a name="named-entity-recognition-ner"></a>NER (Reconhecimento de Entidade Nomeada)

Denominado Reconhecimento de Entidades (NER) é a capacidade de identificar diferentes entidades em texto e categorizá-las em classes ou tipos pré-definidos, como: pessoa, local, evento, produto e organização.  

A partir da versão 3, esse recurso da API text analytics também pode identificar tipos de informações pessoais e sensíveis, como: número de telefone, número de segurança social, endereço de e-mail e número de conta bancária.  Identificar essas entidades pode ajudar a classificar documentos confidenciais e redigir informações pessoais.

## <a name="named-entity-recognition-versions-and-features"></a>Versões e recursos de reconhecimento de entidade socitados

A API Text Analytics oferece duas versões de Reconhecimento de Entidade Nomeado - v2 e v3. A versão 3 (Visualização pública) fornece maiores detalhes nas entidades que podem ser detectadas e categorizadas.

| Recurso                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Métodos para solicitações únicas e em lote                          | X      | X      |
| Reconhecimento de entidades básicas em várias categorias              | X      | X      |
| Classificação ampliada para entidades reconhecidas                 |        | X      |
| Pontos finais separados para o envio de solicitações de vinculação de entidades e NER. |        | X      |
| Controle de versão de modelo                                                |        | X      |

Consulte [o suporte ao idioma](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) para obter informações.


#### <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

### <a name="entity-types"></a>Tipos de entidade

O V3 de reconhecimento de entidade suscitado fornece detecção expandida entre vários tipos. Atualmente, ner v3 pode reconhecer as seguintes categorias de entidades:

* Geral
* Informações Pessoais 

Para obter uma lista detalhada de entidades e idiomas suportados, consulte o artigo [ner v3 supported entity types.](../named-entity-types.md)

### <a name="request-endpoints"></a>Solicitar pontos finais

O V3 de reconhecimento de entidade suinode pontos finais separados para solicitações de vinculação de NER e entidade. Use um formato de URL abaixo com base na sua solicitação:

Ner
* Entidades gerais -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Informações pessoais -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Vinculação de entidade
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Controle de versão de modelo

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

### <a name="entity-types"></a>Tipos de entidade

> [!NOTE]
> A versão 2 do Reconhecimento de Entidade (NER) só suporta as seguintes entidades. NER v3 está em pré-visualização pública, e amplia consideravelmente o número e a profundidade das entidades reconhecidas no texto.   

| Type  | SubType | Exemplo |
|:-----------   |:------------- |:---------|
| Person        | N/D\*         | "João", "Bill Gates"     |
| Location      | N/D\*         | "Redmond, Washington", "Paris"  |
| Organização  | N/D\*         | "Microsoft"   |
| Quantidade      | Número        | "6", "seis"     |
| Quantidade      | Porcentagem    | "50%", "cinquenta por cento"|
| Quantidade      | Ordinal       | "2º", "segundo"     |
| Quantidade      | Idade           | "90 dias", "30 anos"    |
| Quantidade      | Moeda      | "US$ 10,99"     |
| Quantidade      | Dimensão     | "10 milhas", "40 cm"     |
| Quantidade      | Temperatura   | "32 graus"    |
| Datetime      | N/D\*         | "18h30 em 4 de fevereiro de 2012"      |
| Datetime      | Data          | "2 de maio de 2017", "02/05/2017"   |
| Datetime      | Hora          | "8h", "8:00"  |
| Datetime      | DateRange     | "2 de maio a 5 de maio"    |
| Datetime      | TimeRange     | "18h às 19h"     |
| Datetime      | Duration      | "1 minuto e 45 segundos"   |
| Datetime      | Definir           | "toda terça-feira"     |
| URL           | N/D\*         | "https:\//www.bing.com"    |
| Email         | N/D\*         | "support@contoso.com" |
| Número de telefone dos EUA  | N/D\*         | (Somente números de telefone dos EUA) "(312) 555-0176" |
| Endereço IP    | N/D\*         | "10.0.0.100" |

\* Dependendo da entrada e das entidades extraídas, determinadas entidades podem omitir o `SubType`.  Todos os tipos de entidades suportadas listados estão disponíveis apenas para os idiomas inglês, chinês-simplificado, francês, alemão e espanhol.

### <a name="request-endpoints"></a>Solicitar pontos finais

O V2 de reconhecimento de entidade saqueado usa um único ponto final para solicitações de vinculação de NER e entidade:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>Como enviar uma solicitação da API REST

### <a name="preparation"></a>Preparação

Você deve ter documentos JSON neste formato: ID, texto, idioma.

Cada documento deve ter menos de 5.120 caracteres, e você pode ter até 1.000 itens (IDs) por coleção. A coleção é enviada no corpo da solicitação.

### <a name="structure-the-request"></a>Estruturar a solicitação

Crie uma solicitação POST. Você pode usar o [Carteiro](text-analytics-how-to-call-api.md) ou o **console de teste da API** nos seguintes links para estruturar rapidamente e enviar um. 

> [!NOTE]
> Você pode encontrar sua chave e ponto de extremidade para o recurso da Análise de Texto no portal do Azure. Eles estarão localizados na página de **Início rápido** do recurso, em **Gerenciamento de recursos**. 

#### <a name="version-30-preview"></a>[Versão prévia 3.0](#tab/version-3)

[Referência v3 de reconhecimento de entidade nomeada](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

A versão 3 usa pontos finais separados para solicitações de vinculação de NER e entidade. Use um formato de URL abaixo com base na sua solicitação:

Ner
* Entidades gerais -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Entidades de informações pessoais -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Vinculação de entidade
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

[Referência de reconhecimento de entidade (NER) v2](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

A versão 2 usa o seguinte ponto final para vinculação de entidades e solicitações NER: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Defina um cabeçalho de solicitação para incluir sua chave de API de Análise de Texto. No órgão de solicitação, forneça os documentos JSON que você preparou.

### <a name="example-ner-request"></a>Exemplo de solicitação NER 

A seguir, um exemplo de conteúdo que você pode enviar para a API. O formato da solicitação é o mesmo para as duas versões da API.

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>Postar a solicitação

A análise é executada após o recebimento da solicitação. Consulte a seção [limites de dados](../overview.md#data-limits) na visão geral para obter informações sobre o tamanho e o número de solicitações que você pode enviar por minuto e segundo.

A API de Análise de Texto é sem estado. Nenhum dado é armazenado em sua conta e os resultados são retornados imediatamente na resposta.

## <a name="view-results"></a>Exibir os resultados

Todas as solicitações POST retornam uma resposta formatada JSON com os IDs e propriedades de entidade detectadas.

A saída é retornada imediatamente. Você pode transmitir os resultados para um aplicativo que aceita JSON ou salvar a saída em um arquivo no sistema local e, em seguida, importá-lo para um aplicativo que permite que você classifique, pesquise e manipule os dados. Devido ao suporte multilíndrico e emoji, a resposta pode conter deslocamentos de texto. Veja [como processar deslocamentos de texto](../concepts/text-offsets.md) para obter mais informações.

#### <a name="version-30-preview"></a>[Versão 3.0-visualização)](#tab/version-3)

### <a name="example-v3-responses"></a>Exemplo de respostas v3

A versão 3 fornece pontos finais separados para ner e vinculação de entidades. As respostas para ambas as operações estão abaixo. 

#### <a name="example-ner-response"></a>Exemplo de resposta NER

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>Exemplo de resposta de vinculação de entidade

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21"></a>[Versão 2.1](#tab/version-2)

### <a name="example-ner-v2-response"></a>Exemplo de resposta NER v2
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e fluxo de trabalho para detecção de idioma usando a Análise de Texto em Serviços Cognitivos. Em resumo:

* O Reconhecimento de Entidade nomeado está disponível para idiomas selecionados em duas versões.
* Documentos JSON no corpo da solicitação incluem um código de idioma, texto e ID.
* As solicitações post são enviadas para um ou mais pontos finais, usando uma chave de acesso personalizada [e um ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) válido para sua assinatura.
* A saída da resposta, composta por entidades vinculadas (incluindo pontuações de confiança, deslocamentos e links da Web, para cada ID de documento) pode ser usada em qualquer aplicativo

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da Análise de Texto](../overview.md)
* [Como usar a biblioteca de clientes da Análise de Texto](../quickstarts/text-analytics-sdk.md)
* [Novidades](../whats-new.md)
