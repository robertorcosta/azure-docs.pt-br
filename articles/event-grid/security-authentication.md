---
title: Segurança e autenticação da Grade de Eventos do Azure
description: Este artigo descreve diferentes maneiras de autenticar o acesso aos recursos da grade de eventos (webhook, assinaturas, tópicos personalizados)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 528c3613549ee49009f99d45e5bd9c2cf1745d78
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779987"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Autenticando o acesso aos recursos da grade de eventos do Azure
Este artigo fornece informações sobre os seguintes cenários:  

- Autentique clientes que publicam eventos nos tópicos da grade de eventos do Azure usando a SAS (assinatura de acesso compartilhado) ou a chave. 
- Proteja seu ponto de extremidade do webhook usando o Azure Active Directory (Azure AD) para autenticar a grade de eventos para **entregar** eventos ao ponto de extremidade.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Autenticar clientes de publicação usando SAS ou Key
Os tópicos personalizados usam a SAS (Assinatura de Acesso Compartilhado) ou a autenticação de chave. Recomendamos a SAS, mas a autenticação de chave fornece programação simples e é compatível com vários publicadores de webhook existentes.

Você pode incluir o valor da autenticação no cabeçalho HTTP. Para SAS, use **aeg-sas-token** para o valor do cabeçalho. Para autenticação de chave, use **aeg-sas-token** para o valor do cabeçalho.

### <a name="key-authentication"></a>Autenticação de chave

A autenticação de chave é a forma mais simples de autenticação. Use o formato: `aeg-sas-key: <your key>`

Por exemplo, você pode passar uma chave com:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokens SAS

Os tokens SAS para a Grade de Eventos incluem o recurso, um tempo de expiração e uma assinatura. O formato do token SAS é: `r={resource}&e={expiration}&s={signature}`.

O recurso é o caminho para o tópico da grade de eventos para o qual você está enviando eventos. Por exemplo, um caminho de recurso válido é `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`:. Para ver todas as versões de API com suporte, consulte [tipos de recurso Microsoft. EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

Você gera a assinatura de uma chave.

Por exemplo, um valor válido de **aeg-sas-token** é:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

O exemplo a seguir cria um token SAS para uso com a Grade de Eventos:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="encryption-at-rest"></a>Criptografia em repouso

Todos os eventos ou dados gravados no disco pelo serviço de grade de eventos são criptografados por uma chave gerenciada pela Microsoft, garantindo que ele seja criptografado em repouso. Além disso, o período máximo de tempo que os eventos ou os dados retidos é de 24 horas em conformidade com a [política de repetição de grade de eventos](delivery-and-retry.md). A grade de eventos excluirá automaticamente todos os eventos ou dados após 24 horas ou a vida útil do evento, o que for menor.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autenticar a entrega de eventos em pontos de extremidade de webhook
As seções a seguir descrevem como autenticar a entrega de eventos para pontos de extremidade de webhook. Você precisa usar um mecanismo de handshake de validação, independentemente do método usado. Consulte [entrega de evento de webhook](webhook-event-delivery.md) para obter detalhes. 

### <a name="using-azure-active-directory-azure-ad"></a>Usando Azure Active Directory (Azure AD)
Você pode proteger seu ponto de extremidade do webhook usando Azure Active Directory (Azure AD) para autenticar e autorizar a grade de eventos a entregar eventos aos seus pontos de extremidade. Você precisará criar um aplicativo do Azure AD, criar uma função e uma entidade de serviço em seu aplicativo que autoriza a grade de eventos e configurar a assinatura de evento para usar o aplicativo do Azure AD. [Saiba como configurar Azure Active Directory com a grade de eventos](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Usando o segredo do cliente como um parâmetro de consulta
É possível proteger o ponto de extremidade do webhook adicionando parâmetros de consulta à URL do webhook ao criar uma Assinatura de Evento. Defina um desses parâmetros de consulta como um segredo do cliente, como um [token de acesso](https://en.wikipedia.org/wiki/Access_token) ou um segredo compartilhado. O webhook pode usar o segredo para reconhecer que o evento é proveniente da Grade de Eventos com permissões válidas. A Grade de Eventos inclui esses parâmetros de consulta em cada entrega de evento para o webhook. Se o segredo do cliente for atualizado, a assinatura de evento também precisará ser atualizada. Para evitar falhas de entrega durante essa rotação secreta, faça com que o webhook aceite segredos novos e antigos por uma duração limitada. 

Como os parâmetros de consulta podem conter segredos do cliente, eles são tratados com cuidado extra. Eles são armazenados como criptografados e não acessíveis para operadores de serviço. Eles não são registrados como parte dos logs/rastreamentos do serviço. Ao editar a Assinatura de Evento, os parâmetros de consulta não serão exibidos nem retornados, a menos que o parâmetro [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) seja usado na [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) do Azure.

Para obter mais informações sobre como entregar eventos a WebHooks, consulte [entrega de eventos de webhook](webhook-event-delivery.md)

> [!IMPORTANT]
A grade de eventos do Azure só dá suporte a pontos de extremidade de webhook **https** . 

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução à grade de eventos, consulte [sobre a grade de eventos](overview.md)
