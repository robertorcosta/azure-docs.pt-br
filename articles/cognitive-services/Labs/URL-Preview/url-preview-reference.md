---
title: Referência de Visualização de URL de Projeto
titlesuffix: Azure Cognitive Services
description: Referência do ponto de extremidade de Visualização de URL de Projeto.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: f92c0faaaa3aa0cd2af16a031f3bed4c6b41fc22
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220782"
---
# <a name="project-url-preview-v7-reference"></a>Referência de Visualização de URL de Projeto v7

A visualização de URL dá suporte a breves descrições de recursos da Web para postagens de blog, discussões de fórum, páginas de visualização etc. A URL pode ser qualquer tipo de recurso da Internet: página da Web, notícias, imagem ou vídeo. A consulta precisa ser uma URL absoluta com um esquema HTTP ou HTTPS; não há suporte para URLs relativas ou outros esquemas como ftp://.

Os aplicativos que usam a Visualização de URL enviam as solicitações da Web para o ponto de extremidade com uma URL a ser visualizada em um parâmetro de consulta. A solicitação precisa incluir o cabeçalho *Ocp-Apim-Subscription-Key*.

A resposta JSON pode ser analisada quanto às informações de visualização: nome, descrição do recurso, sinalizador *isFamilyFriendly* e links que fornecem acesso a uma imagem representativa e a todo o recurso online.

Use somente os dados de Visualização de URL para visualizar snippets de código e imagens em miniatura com hiperlink para os sites de origem, no compartilhamento de URL iniciada pelo usuário final em mídias sociais, chat bot ou ofertas semelhantes. Não copie, armazene nem armazene em cache os dados recebidos da Visualização de URL de Projeto. Você deve cumprir todas as solicitações para desabilitar visualizações que você pode receber dos proprietários do site ou de conteúdo.

## <a name="endpoint"></a>Ponto de extremidade
Para solicitar resultados de Visualização de URL, envie uma solicitação para o ponto de extremidade a seguir. Use os cabeçalhos e parâmetros de URL para definir mais especificações.

Ponto de extremidade GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

A solicitação precisa usar o protocolo HTTPS e incluir o seguinte parâmetro de consulta:

q – a consulta que identifica a URL a ser visualizada

As seções a seguir fornecem detalhes técnicos sobre os objetos de resposta, os parâmetros de consulta e os cabeçalhos que afetam os resultados da pesquisa.

Para obter informações sobre os cabeçalhos que devem ser incluídos nas solicitações, confira [Cabeçalhos](#headers).

Para obter informações sobre os parâmetros de consulta que devem ser incluídos nas solicitações, confira [Parâmetros de consulta](#query-parameters).

Para obter informações sobre os objetos JSON incluídos na resposta, confira [Objetos de resposta](#response-objects).

O tamanho máximo da URL de consulta é de 2.048 caracteres. Para garantir que o tamanho da URL não exceda o limite, o tamanho máximo dos parâmetros de consulta deve ser inferior a 1.500 caracteres. Se a URL excede 2.048 caracteres, o servidor retorna 404 Não encontrado.

Para obter informações sobre o uso permitido e a exibição de resultados, confira [Requisitos de uso e exibição](use-display-requirements.md).

> [!NOTE]
> Alguns cabeçalhos de solicitação significativos para outras APIs de pesquisa não afetam a Visualização de URL
> - Pragma – o chamador não tem controle sobre se o cache é usado pela Visualização de URL
> - User-Agent – por enquanto, a API de Visualização de URL não fornece respostas diferentes para chamadas provenientes de Computadores, Laptops ou Dispositivos Móveis.
> 
> Além disso, alguns parâmetros não são atualmente significativos para a API de Visualização de URL, mas podem ser usados no futuro para uma melhor globalização.

## <a name="headers"></a>headers
Veja a seguir os cabeçalhos que podem ser incluídos em uma solicitação e uma resposta.

|Cabeçalho|DESCRIÇÃO|
|------------|-----------------|
|<a name="market" />BingAPIs-Market|Cabeçalho de resposta.<br /><br /> O mercado usado pela solicitação. O formulário é \<languageCode\>-\<countryCode\>. Por exemplo, en-US.|
|<a name="traceid" />BingAPIs-TraceId|Cabeçalho de resposta.<br /><br /> A ID da entrada de log que contém os detalhes da solicitação. Quando ocorrer um erro, capture essa ID. Se você não conseguir determinar e resolver o problema, inclua essa ID juntamente com outras informações fornecidas à equipe de Suporte.|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Cabeçalho de solicitação obrigatório.<br /><br /> A chave de assinatura que você recebeu quando se inscreveu nesse serviço nos [Serviços Cognitivos](https://www.microsoft.com/cognitive-services/).|
|<a name="clientid" />X-MSEdge-ClientID|Cabeçalho de solicitação e resposta opcional.<br /><br /> O Bing usa esse cabeçalho para fornecer aos usuários um comportamento consistente nas chamadas à API do Bing. Em geral, o Bing faz o pré-lançamento de novos recursos e novas melhorias e usa a ID do cliente como uma chave para atribuição de tráfego em versões de pré-lançamento diferentes. Se você não usar a mesma ID do cliente para um usuário em várias solicitações, o Bing poderá atribuir o usuário a várias versões de pré-lançamento conflitantes. A atribuição a várias versões de pré-lançamento conflitantes pode levar a uma experiência do usuário inconsistente. Por exemplo, se a segunda solicitação tem uma atribuição de versão de pré-lançamento diferente da primeira, a experiência pode ser inesperada. Além disso, o Bing pode usar a ID do cliente para adaptar os resultados da Web ao histórico de pesquisa dessa ID do cliente, fornecendo uma experiência mais rica para o usuário.<br /><br /> O Bing também usa esse cabeçalho para ajudar a melhorar as classificações de resultado pela análise da atividade gerada por uma ID do cliente. As melhorias de relevância ajudam com uma melhor qualidade dos resultados fornecidos pelas APIs do Bing e, por sua vez, permitem taxas de clickthrough mais altas para o consumidor da API.<br /><br />Veja a seguir as regras de uso básicas que se aplicam a esse cabeçalho.<br /><ul><li>Cada usuário que usa o aplicativo no dispositivo precisa ter uma ID do cliente exclusiva gerada pelo Bing.<br /><br/>Se você não incluir esse cabeçalho na solicitação, o Bing gerará uma ID e a retornará no cabeçalho de resposta X-MSEdge-ClientID. A única vez que você NÃO deve incluir esse cabeçalho em uma solicitação é a primeira vez que o usuário usa o aplicativo nesse dispositivo.<br /><br/></li><li>Use a ID do cliente para cada solicitação da API do Bing feita pelo aplicativo para esse usuário no dispositivo.<br /><br/></li><li>**ATENÇÃO:** garanta que essa ID do Cliente não seja vinculável aos dados da conta de usuário autenticável.</li><br/><li>Persista a ID do cliente. Para persistir a ID em um aplicativo de navegador, use um cookie HTTP persistente para garantir que a ID seja usada em todas as sessões. Não use um cookie de sessão. Para outros aplicativos, como aplicativos móveis, use o armazenamento persistente do dispositivo para persistir a ID.<br /><br/>Na próxima vez que o usuário usar o aplicativo no dispositivo, obtenha a ID do cliente persistente.</li></ul><br /> **OBSERVAÇÃO:** as respostas do Bing podem ou não incluir esse cabeçalho. Se a resposta incluir esse cabeçalho, capture a ID do cliente e use-a para todas as solicitações seguintes do Bing para o usuário no dispositivo.<br /><br /> **OBSERVAÇÃO:** se você incluir o X-MSEdge-ClientID, não deverá incluir cookies na solicitação.|
|<a name="clientip" />X-MSEdge-ClientIP|Cabeçalho de solicitação opcional.<br /><br /> O endereço IPv4 ou IPv6 do dispositivo cliente. O endereço IP é usado para descobrir o local do usuário. O Bing usa as informações de local para determinar o comportamento da pesquisa segura.<br /><br /> Não oculte o endereço (por exemplo, alterando o último octeto para 0). Se você ocultar o endereço, isso fará com que o local não fique em nenhum lugar próximo ao local real do dispositivo, o que poderá resultar no fornecimento de resultados incorretos pelo Bing.|

## <a name="query-parameters"></a>Parâmetros de consulta
A solicitação pode incluir os parâmetros de consulta a seguir. Confira a coluna Obrigatório para obter os parâmetros necessários. É necessário codificar os parâmetros de consulta em URL. A consulta precisa ser uma URL absoluta com um esquema HTTP ou HTTPS; não damos suporte para URLs relativas ou outros esquemas como ftp://

|Nome|Valor|Type|Obrigatório|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|O mercado do qual os resultados são obtidos. <br /><br />Para obter uma lista dos possíveis valores de mercado, confira Códigos de mercado.<br /><br /> **OBSERVAÇÃO:** atualmente, a API de Visualização de URL dá suporte apenas à geografia EUA e ao idioma inglês.<br /><br />|String|Sim|
|<a name="query" />q|A URL a ser visualizada|String|Sim|
|<a name="responseformat" />responseFormat|O tipo de mídia a ser usado para a resposta. Veja a seguir os possíveis valores que não diferenciam maiúsculas de minúsculas.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> O padrão é JSON. Para obter informações sobre os objetos JSON contidos na resposta, confira [Objetos de resposta](#response-objects).<br /><br />Se você especificar JsonLd, o corpo da resposta incluirá objetos JSON-LD que contêm os resultados da pesquisa. Para obter informações sobre o JSON-LD, confira [JSON-LD](https://json-ld.org/).|String|Não|
|<a name="safesearch"/>Pesquisa Segura|O conteúdo ilegal para adulto ou pirateado é bloqueado com o código de erro 400 e o sinalizador *isFamilyFriendly* não é retornado. <p>Para obter conteúdo legal para adulto, veja abaixo o comportamento. O código de status retorna 200 e o sinalizador *isFamilyFriendly* é definido como false.<ul><li>safeSearch=strict: Título, descrição, URL e imagem não serão retornados.</li><li>safeSearch=moderate; Obtém o título, a URL e a descrição, mas não a imagem descritiva.</li><li>safeSearch=off; Obtém todos os elementos/objetos de resposta – título, URL, descrição e imagem.</li></ul> |String|Não obrigatório. </br> Usa como padrão safeSearch=strict.|

## <a name="response-objects"></a>Objetos de resposta
O esquema de resposta é uma [WebPage] ou uma ErrorResponse, como na API de Pesquisa na Web. Se a solicitação falha, o objeto de nível superior é o objeto [ErrorResponse](#errorresponse).

|Objeto|DESCRIÇÃO|
|------------|-----------------|
|[WebPage](#webpage)|O objeto JSON de nível superior que contém atributos de visualização.|

### <a name="error"></a>Erro
Define o erro ocorrido.

|Elemento|DESCRIÇÃO|Type|
|-------------|-----------------|----------|
|<a name="error-code" />code|O código de erro que identifica a categoria de erro. Para obter uma lista dos possíveis códigos, confira [Códigos de erro](#error-codes).|String|
|<a name="error-message" />message|Uma descrição do erro.|String|
|<a name="error-moredetails" />moreDetails|Uma descrição que fornece informações adicionais sobre o erro.|String|
|<a name="error-parameter" />parameter|O parâmetro de consulta na solicitação que causou o erro.|String|
|<a name="error-subcode" />subCode|O código de erro que identifica o erro. Por exemplo, se `code` é InvalidRequest, `subCode` pode ser ParameterInvalid ou ParameterInvalidValue. |String|
|<a name="error-value" />value|O valor do parâmetro de consulta inválido.|String|

### <a name="errorresponse"></a>ErrorResponse
O objeto de nível superior incluído pela resposta quando a solicitação falha.

|Nome|Valor|Type|
|----------|-----------|----------|
|_type|Dica de tipo.|String|
|<a name="errors" />errors|Uma lista de erros que descreve os motivos pelos quais a solicitação falhou.|[Error](#error)[]|

### <a name="webpage"></a>WebPage
Define as informações sobre uma página da Web na visualização.

|Nome|Valor|Type|
|----------|-----------|----------|
|name|O título de página, não necessariamente o título HTML|String|
|url|A URL que foi realmente rastreada (a solicitação pode ter seguido os redirecionamentos)|String|
|descrição|Breve descrição da página e do conteúdo|String|
|isFamilyFriendly|Mais preciso para itens no índice da Web; os fetches em tempo real fazem essa detecção baseada somente na URL e não no conteúdo da página|booleano|
|primaryImageOfPage/contentUrl|A URL de uma imagem representativa a ser incluída na visualização|String|

### <a name="identifiable"></a>Identifiable
|Nome|Valor|Type|
|-------------|-----------------|----------|
|id|Um identificador de recurso|String|

## <a name="error-codes"></a>Códigos do Erro

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação.

|Código de status|DESCRIÇÃO|
|-----------------|-----------------|
|200|Sucesso.|
|400|Um dos parâmetros de consulta está ausente ou é inválido.|
|400|ServerError, subCode ResourceError: a URL solicitada não pôde ser acessada|
|400|ServerError, subCode ResourceError: a URL solicitada não retornou um código de êxito (incluindo se ela retornou HTTP 404)|
|400|InvalidRequest, subCode Blocked: a URL solicitada pode conter conteúdo para adulto e foi bloqueada|
|401|A chave de assinatura está ausente ou é inválida.|
|403|O usuário foi autenticado (por exemplo, ele usou uma chave de assinatura válida), mas não tem permissão para acessar o recurso solicitado.<br /><br /> O Bing também pode retornar esse status se o chamador excedeu suas consultas em relação à cota do mês.|
|410|A solicitação usou HTTP em vez do protocolo HTTPS. HTTPS é o único protocolo compatível.|
|429|O chamador excedeu suas consultas em relação à cota de segundos.|
|500|Erro de servidor inesperado.|

Se a solicitação falha, a resposta contém um objeto [ErrorResponse](#errorresponse), que contém uma lista de objetos [Error](#error) que descrevem a causa do erro. Se o erro está relacionado a um parâmetro, o campo `parameter` identifica o parâmetro que causa o problema. Além disso, se o erro está relacionado a um valor de parâmetro, o campo `value` identifica o valor que é inválido.

```json
{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidRequest",
      "subCode": "ParameterMissing",
      "message": "Required parameter is missing.",
      "parameter": "q"
    }
  ]
}

{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidAuthorization",
      "subCode": "AuthorizationMissing",
      "message": "Authorization is required.",
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Veja a seguir os possíveis valores de código de erro e de código de suberro.

|Código|Subcódigo|DESCRIÇÃO
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|O código de status HTTP é 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing retornará InvalidRequest sempre que qualquer parte da solicitação não for válida. Por exemplo, um parâmetro obrigatório está ausente ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de status HTTP será 400.<br/><br/>Se você usar o protocolo HTTP em vez de HTTPS, o Bing retornará HttpNotAllowed, e o código de status HTTP será 410.
|RateLimitExceeded|Nenhum subcódigo|O Bing retornará RateLimitExceeded sempre que você exceder a cota de consultas por segundo (QPS) ou consultas por mês (QPM).<br/><br/>Se você exceder o QPS, o Bing retornará o código de status HTTP 429 e, se você exceder o QPM, o Bing retornará 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing retorna InvalidAuthorization quando o Bing não pode autenticar o chamador. Por exemplo, o cabeçalho `Ocp-Apim-Subscription-Key` está ausente ou a chave de assinatura não é válida.<br/><br/>A redundância ocorrerá se você especificar mais de um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP será 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing retorna InsufficientAuthorization quando o chamador não tem permissões para acessar o recurso. Isso pode ocorrer se a chave de assinatura foi desabilitada ou expirou. <br/><br/>Se o erro for InsufficientAuthorization, o código de status HTTP será 403.

## <a name="next-steps"></a>Próximas etapas
- [Início Rápido do C#](csharp.md)
- [Início Rápido do Java](java-quickstart.md)
- [Início Rápido do JavaScript](javascript.md)
- [Início Rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
