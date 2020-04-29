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
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81532386"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Autenticando o acesso aos recursos da grade de eventos

A Grade de Eventos do Azure tem três tipos de autenticação:

- Entrega de eventos do WebHook
- Assinaturas de evento
- Publicação de tópico personalizado

## <a name="webhook-event-delivery"></a>Entrega de eventos do WebHook

Webhooks são uma dentre várias maneiras de receber eventos da Grade de Eventos do Azure. Quando um novo evento estiver pronto, o serviço da Grade de Eventos do Azure POSTs uma solicitação HTTP para o ponto de extremidade configurado com o evento o corpo da solicitação.

Como muitos outros serviços que dão suporte a webhooks, a Grade de Eventos do Azure exige que você comprovar a "propriedade" de seu ponto de extremidade do Webhook antes de começar a entrega de eventos para esse ponto de extremidade. Esse requisito impede que um usuário mal-intencionado inunde seu ponto de extremidade com eventos. Quando você usa qualquer um dos três serviços do Azure listado abaixo, a infraestrutura do Azure trata automaticamente essa validação:

- Aplicativos Lógicos do Azure com [Conector da Grade de Eventos](https://docs.microsoft.com/connectors/azureeventgrid/)
- Automação do Azure por meio de [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions com [Gatilho de Grade de Eventos](../azure-functions/functions-bindings-event-grid.md)

Se você estiver usando qualquer outro tipo de ponto de extremidade, como uma função do Azure baseada no gatilho HTTP, o código do ponto de extremidade precisará participar de um handshake de validação com o EventGrid. A Grade de Eventos dá suporte a duas maneiras de validar a assinatura.

1. **Handshake síncrono**: no momento da criação da assinatura do evento, a grade de eventos envia um evento de validação de assinatura para seu ponto de extremidade. O esquema desse evento é semelhante a qualquer outro evento da Grade de Eventos. A parte de dados desse evento inclui um `validationCode` propriedade. Seu aplicativo verifica se a solicitação de validação é para uma assinatura de evento esperada e retorna o código de validação na resposta de forma síncrona. Esse mecanismo de handshake é compatível com todas as versões da Grade de Eventos.

2. **Handshake assíncrono**: em certos casos, você não pode retornar o ValidationCode em resposta de forma síncrona. Por exemplo, se você usar um serviço de terceiros (como [`Zapier`](https://zapier.com) ou [IFTTT](https://ifttt.com/)), não poderá responder de forma programática com o código de validação.

   A partir da versão 2018-05-01-preview, a Grade de Eventos dá suporte a um handshake de validação manual. Se você estiver criando uma inscrição de evento com um SDK ou ferramenta que usa a versão da API 2018-05-01-preview ou posterior, a Grade de Eventos envia uma propriedade `validationUrl` na parte de dados do evento de validação da assinatura. Para concluir o handshake, localize essa URL nos dados do evento e faça uma solicitação GET para ele. Você pode usar um cliente REST ou o navegador da web.

   A URL fornecida é válida por **5 minutos**. Durante esse tempo, o estado de fornecimento da assinatura do evento é `AwaitingManualAction`. Se você não concluir a validação manual em 5 minutos, o estado de provisionamento será definido como `Failed`. Você terá que criar a inscrição do evento novamente antes de iniciar a validação manual.

   Esse mecanismo de autenticação também exige que o ponto de extremidade do webhook retorne um código de status HTTP 200 para que ele saiba que a POSTAgem do evento de validação foi aceita antes que possa ser colocada no modo de validação manual. Em outras palavras, se o ponto de extremidade retornar 200, mas não retornar uma resposta de validação de forma síncrona, o modo será transferido para o modo de validação manual. Se houver um GET na URL de validação em 5 minutos, o handshake de validação será considerado com êxito.

> [!NOTE]
> Não há suporte para o uso de certificados autoassinados para validação. Em vez disso, use um certificado assinado de uma autoridade de certificação (CA).

### <a name="validation-details"></a>Detalhes da validação

- No momento da criação/atualização da assinatura, a Grade de Eventos posta um Evento de Validação de Assinatura para o ponto de extremidade de destino.
- O evento contém um valor de cabeçalho "Aeg-Event-Type: SubscriptionValidation".
- O corpo do evento tem o mesmo esquema que outros eventos da Grade de Eventos.
- A propriedade eventType do evento é `Microsoft.EventGrid.SubscriptionValidationEvent`.
- Os dados de propriedade do evento incluem uma propriedade `validationCode` com uma cadeia de caracteres gerada aleatoriamente. Por exemplo, "validationCode: acb13…".
- Os dados do evento também incluem uma propriedade `validationUrl` com uma URL para validar manualmente a assinatura.
- A matriz contém apenas o evento de validação. Outros eventos serão enviados em uma solicitação separada, após retornar o código de validação.
- Os SDKs do plano de dados EventGrid têm classes correspondentes para os dados de evento de validação de assinatura e a resposta de validação de assinatura.

Um SubscriptionValidationEvent de exemplo é mostrado no exemplo a seguir:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Para provar a propriedade do pronto de extremidade, retorne o código de validação na propriedade validationResponse, conforme mostrado no exemplo a seguir:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Você deve retornar um código de status de resposta HTTP 200 Okey. HTTP 202 aceito não é reconhecido como uma resposta de validação de assinatura de Grade de Eventos válida. A solicitação HTTP deve ser concluída dentro de 30 segundos. Se a operação não for concluída dentro de 30 segundos, a operação será cancelada e poderá ser tentada novamente após 5 segundos. Se todas as tentativas falharem, elas serão tratadas como um erro de handshake de validação.

Ou então, você pode validar a assinatura manualmente, enviando uma solicitação GET para a URL de validação. A inscrição do evento permanece em um estado pendente até que validada. A URL de validação usa a porta 553. Se suas regras de firewall bloquearem a porta 553, talvez seja necessário atualizar as regras para um handshake manual bem-sucedido.

Para obter um exemplo de lidar com o handshake de validação de assinatura, consulte uma [ amostra C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="troubleshooting-eventsubsciption-validation"></a>Solucionando problemas de validação de EventSubsciption

Durante a criação da assinatura do evento, se você estiver vendo uma mensagem de erro como "a tentativa de validar o ponto\/de extremidade fornecido https:/Your-Endpoint-Here falhou. Para obter mais detalhes, visite https\/:/aka.ms/esvalidation ", que indica que há uma falha no handshake de validação. Para resolver esse erro, verifique os seguintes aspectos:

- Faça um HTTP POST para a URL do webhook com um corpo de solicitação [SubscriptionValidationEvent de exemplo](#validation-details) usando o postmaster ou a ondulação ou ferramenta semelhante.
- Se o webhook estiver implementando o mecanismo de handshake de validação síncrona, verifique se o ValidationCode é retornado como parte da resposta.
- Se o seu webhook estiver implementando o mecanismo de handshake de validação assíncrona, verifique se você é o HTTP POST está retornando 200 OK.
- Se seu webhook estiver retornando 403 (proibido) na resposta, verifique se o webhook está atrás de um gateway de Aplicativo Azure ou de um firewall do aplicativo Web. Se for, você precisará desabilitar essas regras de firewall e executar um HTTP POST novamente:

  920300 (a solicitação não tem um cabeçalho Accept, podemos corrigir isso)

  942430 (detecção de anomalias de caracteres SQL restritos (args): número de caracteres especiais excedido (12))

  920230 (várias codificações de URL detectadas)

  942130 (ataque de injeção de SQL: SQL tautology detectado.)

  931130 (possível ataque de RFI (inclusão de arquivo remoto) = link/referência fora do domínio)

### <a name="event-delivery-security"></a>Segurança de entrega de evento

#### <a name="azure-ad"></a>Azure AD

Você pode proteger seu ponto de extremidade do webhook usando Azure Active Directory para autenticar e autorizar a grade de eventos a publicar eventos em seus pontos de extremidade. Você precisará criar um aplicativo Azure Active Directory, criar uma função e uma entidade de serviço em seu aplicativo autorizando a grade de eventos e configurar a assinatura de evento para usar o aplicativo do Azure AD. [Saiba como configurar o AAD com a grade de eventos](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Parâmetros de consulta

É possível proteger o ponto de extremidade do webhook adicionando parâmetros de consulta à URL do webhook ao criar uma Assinatura de Evento. Defina um desses parâmetros de consulta para ser um segredo, como um [token de acesso](https://en.wikipedia.org/wiki/Access_token). O webhook pode usar o segredo para reconhecer que o evento é proveniente da Grade de Eventos com permissões válidas. A Grade de Eventos inclui esses parâmetros de consulta em cada entrega de evento para o webhook.

Ao editar a Assinatura de Evento, os parâmetros de consulta não serão exibidos nem retornados, a menos que o parâmetro [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) seja usado na [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) do Azure.

Por fim, é importante observar que a Grade de Eventos do Azure oferece suporte apenas a ponto de extremidade do webhook HTTPS.

## <a name="event-subscription"></a>Assinatura do evento

Para assinar um evento, você deve provar que tem acesso à origem do evento e ao manipulador. A prova de que você tem um WebHook foi abordada na seção anterior. Se você estiver usando um manipulador de eventos que não seja um WebHook (como um armazenamento de fila ou hub de eventos), será necessário acesso de gravação a esse recurso. Essa verificação de permissões impede que um usuário não autorizado envie eventos para seu recurso.

Você deve ter a permissão **Microsoft.EventGrid/EventSubscriptions/Write** no recurso que é a origem do evento. Essa permissão é necessária porque está gravando uma nova assinatura no escopo do recurso. O recurso necessário varia de acordo com se você estiver assinando um tópico de sistema ou um tópico personalizado. Ambos os tipos são descritos nesta seção.

### <a name="system-topics-azure-service-publishers"></a>Tópicos do sistema (publicadores de serviço do Azure)

Para tópicos do sistema, você precisa de permissão para gravar uma nova assinatura de evento no escopo do recurso que está publicando o evento. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Por exemplo, para assinar um evento em uma conta de armazenamento chamada **myacct**, você precisa da permissão Microsoft.EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Tópicos personalizados

Para tópicos personalizados, você precisa de permissão para gravar uma nova assinatura de evento no escopo do tópico da grade de eventos. O formato do recurso é: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Por exemplo, para assinar um tópico personalizado chamado **mytopic**, você precisa da permissão Microsoft.EventGrid/EventSubscriptions/Write em: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publicação de tópico personalizado

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

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validação de ponto de extremidade com CloudEvents v 1.0
Se você já estiver familiarizado com a grade de eventos, talvez esteja ciente do handshake de validação do ponto de extremidade da grade de eventos para evitar abusos. O CloudEvents v 1.0 implementa sua própria [semântica de proteção de abuso](security-authentication.md#webhook-event-delivery) usando o método de opções http. Saiba mais sobre isso [aqui](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Ao usar o esquema CloudEvents para saída, a grade de eventos usa com a proteção de abuso do CloudEvents v 1.0 em vez do mecanismo de evento de validação da grade de eventos.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução à grade de eventos, consulte [sobre a grade de eventos](overview.md)
