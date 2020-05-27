---
title: Segurança e autenticação da Grade de Eventos do Azure
description: Este artigo descreve diferentes maneiras de autenticar o acesso aos recursos da Grade de Eventos (webhook, assinaturas e tópicos personalizados).
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 71d47c83586f7e5e31b148714e2804686422326a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588251"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Autenticando o acesso aos recursos da Grade de Eventos do Azure
Este artigo fornece informações sobre os seguintes cenários:  

- Autentique clientes que publicam eventos nos tópicos da Grade de Eventos do Azure usando SAS (assinatura de acesso compartilhado) ou chave. 
- Proteja seu ponto de extremidade do webhook usando o Azure Active Directory (Azure AD) para autenticar a Grade de Eventos para **entregar** eventos ao ponto de extremidade.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Autenticar clientes de publicação usando SAS ou chave
Os tópicos personalizados usam a SAS (Assinatura de Acesso Compartilhado) ou a autenticação de chave. Recomendamos a SAS, mas a autenticação de chave fornece programação simples e é compatível com vários publicadores de webhook existentes.

Você pode incluir o valor da autenticação no cabeçalho HTTP. Para SAS, use **aeg-sas-token** para o valor do cabeçalho. Para autenticação de chave, use **aeg-sas-token** para o valor do cabeçalho.

### <a name="key-authentication"></a>Autenticação de chave

A autenticação de chave é a forma mais simples de autenticação. Use o formato `aeg-sas-key: <your key>` no cabeçalho da mensagem.

Por exemplo, você pode passar uma chave com:

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Você também pode especificar `aeg-sas-key` como um parâmetro de consulta. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokens SAS

Os tokens SAS para a Grade de Eventos incluem o recurso, um tempo de expiração e uma assinatura. O formato do token SAS é: `r={resource}&e={expiration}&s={signature}`.

O recurso é o caminho para o tópico da grade de eventos para o qual você está enviando eventos. Por exemplo, um caminho de recurso válido é: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`. Para ver todas as versões de API compatíveis, confira [Tipos de recurso do Microsoft.EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

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

Todos os eventos ou dados gravados no disco pelo serviço Grade de Eventos são criptografados por uma chave gerenciada pela Microsoft, garantindo que estejam criptografados quando inativos. Além disso, o período máximo de retenção dos eventos ou dados é de 24 horas, em conformidade com a [Política de repetição da Grade de Eventos](delivery-and-retry.md). A Grade de Eventos excluirá automaticamente todos os eventos ou dados após 24 horas ou a vida útil do evento, o que for menor.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autenticar a entrega de eventos em pontos de extremidade de webhook
As seções a seguir descrevem como autenticar a entrega de eventos em pontos de extremidade do webhook. Você precisa usar um mecanismo de handshake de validação, independentemente do método usado. Confira [Entrega de eventos do webhook](webhook-event-delivery.md) para obter detalhes. 

### <a name="using-azure-active-directory-azure-ad"></a>Usando o Azure Active Directory (Azure AD)
Você pode proteger seu ponto de extremidade do webhook usando o Azure Active Directory (Azure AD) para autenticar e autorizar a Grade de Eventos para entregar eventos aos seus pontos de extremidade. Crie um aplicativo do Azure AD, uma função e um princípio de serviço em seu aplicativo que autoriza a Grade de Eventos e configure a assinatura do evento para usar o aplicativo do Azure AD. [Saiba como configurar o Azure Active Directory com a Grade de Eventos](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Usando o segredo do cliente como um parâmetro de consulta
É possível proteger o ponto de extremidade do webhook adicionando parâmetros de consulta à URL do webhook ao criar uma Assinatura de Evento. Defina um desses parâmetros de consulta como segredo do cliente; por exemplo, um [token de acesso](https://en.wikipedia.org/wiki/Access_token) ou um segredo compartilhado. O webhook pode usar o segredo para reconhecer que o evento é proveniente da Grade de Eventos com permissões válidas. A Grade de Eventos inclui esses parâmetros de consulta em cada entrega de evento para o webhook. Se o segredo do cliente for atualizado, a assinatura do evento também precisará de atualização. Para evitar falhas na entrega durante essa rotação de segredo, faça com que o webhook aceite segredos novos e antigos por uma duração limitada. 

Como os parâmetros de consulta podem conter segredos do cliente, eles são tratados com cuidado extra. Eles são armazenados como criptografados e não acessíveis a operadores de serviço. Eles não são registrados como parte dos logs/rastreamentos do serviço. Ao editar a Assinatura de Evento, os parâmetros de consulta não serão exibidos nem retornados, a menos que o parâmetro [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) seja usado na [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) do Azure.

Para obter mais informações sobre como entregar eventos a webhooks, confira [Entrega de eventos do webhook](webhook-event-delivery.md)

> [!IMPORTANT]
A Grade de Eventos do Azure é compatível somente com pontos de extremidade **HTTPS** do webhook. 

## <a name="next-steps"></a>Próximas etapas

- Para ver uma introdução à Grade de Eventos, confira [Sobre a Grade de Eventos](overview.md)
