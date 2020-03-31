---
title: Filtragem, encomenda e paginação de entidades de Serviços de Mídia
titleSuffix: Azure Media Services
description: Saiba mais sobre filtragem, encomenda e paginação de entidades v3 do Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: bc5c983bc98c3b62df977c6765978cd45cd3c93b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500039"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtragem, encomenda e paginação de entidades de Serviços de Mídia

Este tópico discute as opções de consulta oData e o suporte à paginação disponíveis quando você está listando entidades v3 do Azure Media Services.

## <a name="considerations"></a>Considerações

* Propriedades de entidades do `Datetime` tipo estão sempre no formato UTC.
* O espaço em branco na seqüência de consultas deve ser codificado por URL antes de enviar uma solicitação.

## <a name="comparison-operators"></a>Operadores de comparação

Você pode usar os seguintes operadores para comparar um campo a um valor constante:

Operadores de igualdade:

- `eq`: Teste se um campo é *igual a* um valor constante.
- `ne`: Teste se um campo não é *igual a* um valor constante.

Operadores de alcance:

- `gt`: Teste se um campo é *maior do que* um valor constante.
- `lt`: Teste se um campo é *menor do que* um valor constante.
- `ge`: Teste se um campo é *maior ou igual a* um valor constante.
- `le`: Teste se um campo é *menor ou igual a* um valor constante.

## <a name="filter"></a>Filtrar

Use `$filter` para fornecer um parâmetro de filtro OData para encontrar apenas os objetos em que você está interessado.

O exemplo REST a `alternateId` seguir filtra o valor de um ativo:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

O exemplo c# a seguir filtra na data criada pelo ativo:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Ordem por

Use `$orderby` para classificar os objetos devolvidos pelo parâmetro especificado. Por exemplo:   

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Para classificar os resultados em ordem ascendente ou `asc` `desc` descendente, apêndice ou ao nome do campo, separado por um espaço. Por exemplo: `$orderby properties/created desc`.

## <a name="skip-token"></a>Token de pular

Se uma resposta de consulta contiver muitos `$skiptoken` itens, o serviço retorna um valor`@odata.nextLink`que você usa para obter a próxima página de resultados. Use-o para fazer a página de todo o conjunto de resultados.

No Media Services v3, você não pode configurar o tamanho da página. O tamanho da página varia de acordo com o tipo de entidade. Leia as seções individuais a seguir para obter detalhes.

Se as entidades forem criadas ou excluídas durante a coleta, as alterações serão refletidas nos resultados devolvidos (se essas alterações estiverem na parte da coleção que não foi baixada).

> [!TIP]
> Use `nextLink` sempre para enumerar a coleção e não dependa de um determinado tamanho de página.
>
> O `nextLink` valor só estará presente se houver mais de uma página de entidades.

Considere o seguinte `$skiptoken` exemplo de onde é usado. Certifique-se de substituir *amstestaccount* pelo seu nome de conta e defina o valor *api-version* valor para a versão mais recente.

Se você solicitar uma lista de ativos como este:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Você terá de volta uma resposta semelhante a esta:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Em seguida, você pode solicitar a próxima página, enviando uma solicitação get para:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

O exemplo C# a seguir mostra como enumerar todos os localizadores de streaming na conta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Usando operadores lógicos para combinar opções de consulta

O Media Services v3 suporta **operadores OR** **e lógicos.** 

O seguinte exemplo REST verifica o estado do trabalho:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Você constrói a mesma consulta em C# assim: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Filtrando e ordenando opções de entidades

A tabela a seguir mostra como você pode aplicar as opções de filtragem e pedido a diferentes entidades:

|Nome da entidade|Nome da propriedade|Filtrar|Order|
|---|---|---|---|
|[Ativos](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` e `desc`|
||properties.alternateId |`eq`||
||properties.alternateId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` e `desc`|
|[Políticas-chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.policyId|`eq`, `ne`||
|[Trabalhos](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` e `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` e `desc`| 
|[Localizadores de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` e `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Políticas de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Transformações](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` e `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` e `desc`|

## <a name="next-steps"></a>Próximas etapas

* [Listar ativos](https://docs.microsoft.com/rest/api/media/assets/list)
* [Listar políticas-chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Listar empregos](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Política de streaming de listas](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Localizadores de streaming de listas](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
* [Cotas e limitações](limits-quotas-constraints.md)
