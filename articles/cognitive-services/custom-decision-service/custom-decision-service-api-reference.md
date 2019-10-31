---
title: Referência de API - Serviço de Decisão Personalizada
titlesuffix: Azure Cognitive Services
description: Um guia de API completo para o Serviço de Decisão Personalizada.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 569a1c83562a995f15e12013c864ef4c0447d963
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161703"
---
# <a name="api"></a>API

O Serviço de Decisão Personalizada do Azure fornece duas APIs que são chamadas para cada decisão: a [API de Classificação](#ranking-api) para inserir a classificação das ações e a [API de Recompensa](#reward-api) para gerar a recompensa. Além disso, você fornece uma [API de Conjunto de Ações](#action-set-api-customer-provided) para especificar as ações para o Serviço de Decisão Personalizada do Azure. Este artigo aborda essas três APIs. Um cenário típico é usado abaixo para mostrar quando o Serviço de Decisão Personalizada otimiza a classificação de artigos.

## <a name="ranking-api"></a>API de Classificação

A API de classificação usa um padrão de comunicação de estilo [JSONP](https://en.wikipedia.org/wiki/JSONP) para otimizar a latência e ignorar a [política de mesma origem](https://en.wikipedia.org/wiki/Same-origin_policy). A última proíbe o JavaScript de efetuar fetch de dados externamente à origem da página.

Insira este snippet no cabeçalho HTML da página frontal (na qual uma lista personalizada de artigos é exibida):

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> A função de retorno de chamada precisa ser definida antes da chamada à API de Classificação.

> [!TIP]
> Para melhorar a latência, a API de Classificação é exposta por meio de HTTP, em vez de HTTPS, como em `https://ds.microsoft.com/api/v2/<appId>/rank/*`.
> No entanto, um ponto de extremidade HTTPS precisa ser usado se a página frontal é atendida por meio de HTTPS.

Quando não são usados parâmetros, a resposta HTTP da API de Classificação é uma cadeia de caracteres formatada em JSONP:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

Em seguida, o navegador executa essa cadeia de caracteres como uma chamada à função `callback()`.

O parâmetro para a função de retorno de chamada no exemplo anterior tem o seguinte esquema:

- `ranking` fornece a classificação de URLs a ser exibida.
- `eventId` é usada internamente pelo Serviço de Decisão Personalizada para corresponder essa classificação com os cliques correspondentes.
- `appId` permite que a função de retorno de chamada diferencie os vários aplicativos do Serviço de Decisão Personalizada em execução na mesma página da Web.
- `actionSets` lista cada conjunto de ações usado na chamada à API de Classificação, juntamente com o carimbo de data/hora UTC da última atualização bem-sucedida. O Serviço de Decisão Personalizada atualiza periodicamente os feeds do conjunto de ações. Por exemplo, se um dos conjuntos de ações não for atual, a função de retorno de chamada precisará recorrer à classificação padrão.

> [!IMPORTANT]
> Os conjuntos de ações especificados são processados e, possivelmente, removidos, para formar a classificação padrão dos artigos. Em seguida, a classificação padrão é reordenada e retornada na resposta HTTP. A classificação padrão é definida aqui:
>
> - Em cada conjunto de ações, os artigos são removidos para obter os 15 artigos mais recentes (se mais do que 15 são retornados).
> - Quando vários conjuntos de ações são especificados, eles são mesclados na mesma ordem da chamada à API. A ordenação original dos artigos é preservada em cada conjunto de ações. As duplicatas são removidas em favor das cópias anteriores.
> - Os primeiros artigos `n` são mantidos fora da lista mesclada de artigos, em que `n=20`, por padrão.

### <a name="ranking-api-with-parameters"></a>API de Classificação com parâmetros

A API de Classificação permite estes parâmetros:

- `details=1` e `details=2` inserem detalhes adicionais sobre cada artigo listado em `ranking`.
- `limit=<n>` especifica o número máximo de artigos na classificação padrão. `n` precisa estar entre `2` e `30` (caso contrário, ele é truncado para `2` ou `30`, respectivamente).
- `dnt=1` desabilita os cookies do usuário.

Os parâmetros podem ser combinados em uma sintaxe de cadeia de caracteres de consulta padrão, por exemplo, `details=2&dnt=1`.

> [!IMPORTANT]
> A configuração padrão na Europa deve ser `dnt=1`, até que o cliente concorde com a notificação de cookie. Ela também deve ser a configuração padrão para sites direcionados a menores. Para obter mais informações, confira os [termos de uso](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

O elemento `details=1` insere o `guid` de cada artigo, caso ele seja fornecido pela API de Conjunto de Ações. A resposta HTTP:

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

O elemento `details=2` adiciona mais detalhes que o Serviço de Decisão Personalizada pode extrair do [código de personalização](https://github.com/Microsoft/mwt-ds/tree/master/Crawl) das metamarcas SEO dos artigos:

- `title` de `<meta property="og:title" content="..." />` ou `<meta property="twitter:title" content="..." />` ou `<title>...</title>`
- `description` de `<meta property="og:description" ... />` ou `<meta property="twitter:description" content="..." />` ou `<meta property="description" content="..." />`
- `image` de `<meta property="og:image" content="..." />`
- `ds_id` de `<meta name="microsoft:ds_id" content="..." />`

A resposta HTTP:

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

O elemento `<details>`:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>API de Recompensa

O Serviço de Decisão Personalizada usa cliques somente no slot superior. Cada clique é interpretado como uma recompensa igual a 1. A ausência de um clique é interpretada como uma recompensa igual a 0. Os cliques são combinados com as classificações correspondentes usando as IDs de evento, que são geradas pela chamada à [API de Classificação](#ranking-api). Se necessário, as IDs de evento podem ser passadas por meio de cookies de sessão.

Para manipular um clique no slot superior, coloque este código em sua página frontal:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Aqui, `data` é o argumento para a função `callback()`, conforme descrito anteriormente. O uso de `data` no código de tratamento de cliques exige um pouco de cuidado. Um exemplo é mostrado neste [tutorial](custom-decision-service-tutorial-news.md#use-the-apis).

Somente para fins de teste, a API de Recompensa pode ser chamada por meio do [cURL](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

O efeito esperado é uma resposta HTTP 200 (OK). Você pode ver a recompensa igual a 1 para esse evento no log (caso uma chave de conta de armazenamento do Azure tenha sido fornecida no portal).

## <a name="action-set-api-customer-provided"></a>API de Conjunto de Ações (fornecida pelo cliente)

Em um nível superior, a API de Conjunto de Ações retorna uma lista de artigos (ações). Cada artigo é especificado por sua URL e (opcionalmente) pelo título do artigo e pela data de publicação. Você pode especificar vários conjuntos de ações no portal. Uma API de Conjunto de Ações diferente deve ser usada para cada conjunto de ações, como uma URL distinta.

Cada API de Conjunto de Ações pode ser implementada de duas maneiras: como um RSS feed ou como um feed Atom. As duas devem estar em conformidade com o padrão e retornar um XML correto. Para o RSS, este é um exemplo:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Cada elemento `<item>` de nível superior descreve uma ação:

- `<link>` é obrigatório e é usado como uma ID de ação.
- `<date>` é ignorado se é inferior ou igual a 15 itens; caso contrário, é obrigatório.
  - Se há mais de 15 itens, os 15 mais recentes são usados.
  - Ele precisa estar no formato padrão do RSS ou do Atom, respectivamente:
    - [RFC 822](https://tools.ietf.org/html/rfc822) para o RSS: por exemplo, `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) para o Atom: por exemplo, `"2016-12-19T16:39:57-08:00"`
- `<title>` é opcional e é usado para gerar recursos que descrevem o artigo.
- `<guid>` é opcional e passado por meio do sistema para a função de retorno de chamada (se o parâmetro `?details` é especificado na chamada à API de Classificação).

Outros elementos dentro de um `<item>` são ignorados.

A versão do feed Atom usa a mesma sintaxe XML e convenções.

> [!TIP]
> Se o sistema usar suas próprias IDs de artigo, elas poderão ser passadas para a função de retorno de chamada com o uso de `<guid>`.
