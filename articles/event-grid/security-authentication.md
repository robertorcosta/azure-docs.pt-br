---
title: Segurança e autenticação da Grade de Eventos do Azure
description: Este artigo descreve diferentes maneiras de autenticar o acesso aos seus recursos da Event Grid (WebHook, assinaturas, tópicos personalizados)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532386"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Autenticação de acesso aos recursos da Event Grid

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

1. **Aperto de mão síncrono**: No momento da criação da assinatura do evento, event grid envia um evento de validação de assinatura para o seu ponto final. O esquema desse evento é semelhante a qualquer outro evento da Grade de Eventos. A parte de dados desse evento inclui um `validationCode` propriedade. Seu aplicativo verifica se a solicitação de validação é para uma assinatura de evento esperada e retorna o código de validação na resposta sincronizadamente. Esse mecanismo de handshake é compatível com todas as versões da Grade de Eventos.

2. **Aperto de mão assíncrono**: Em certos casos, você não pode retornar o Código de Validação em resposta sincronizadamente. Por exemplo, se você usar um [`Zapier`](https://zapier.com) serviço de terceiros (como ou [IFTTT),](https://ifttt.com/)você não poderá responder programáticamente com o código de validação.

   A partir da versão 2018-05-01-preview, a Grade de Eventos dá suporte a um handshake de validação manual. Se você estiver criando uma inscrição de evento com um SDK ou ferramenta que usa a versão da API 2018-05-01-preview ou posterior, a Grade de Eventos envia uma propriedade `validationUrl` na parte de dados do evento de validação da assinatura. Para completar o aperto de mão, encontre essa URL nos dados do evento e faça uma solicitação GET para ele. Você pode usar um cliente REST ou o navegador da web.

   A URL fornecida é válida por **5 minutos**. Durante esse tempo, o estado de fornecimento da assinatura do evento é `AwaitingManualAction`. Se você não completar a validação manual dentro de 5 `Failed`minutos, o estado de provisionamento será definido como . Você terá que criar a inscrição do evento novamente antes de iniciar a validação manual.

   Este mecanismo de autenticação também requer que o ponto final do webhook retorne um código de status HTTP de 200 para que ele saiba que o POST para o evento de validação foi aceito antes que ele possa ser colocado no modo de validação manual. Em outras palavras, se o ponto final retornar 200, mas não retornar uma resposta de validação de forma sincronizada, o modo será transicionado para o modo de validação manual. Se houver um GET na URL de validação dentro de 5 minutos, o aperto de mão de validação será considerado bem-sucedido.

> [!NOTE]
> O uso de certificados auto-assinados para validação não é suportado. Use um certificado assinado de uma autoridade certificadora (CA) em vez disso.

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

Você deve retornar um código de status de resposta HTTP 200 Okey. HTTP 202 aceito não é reconhecido como uma resposta de validação de assinatura de Grade de Eventos válida. A solicitação http deve ser concluída em até 30 segundos. Se a operação não terminar dentro de 30 segundos, a operação será cancelada e poderá ser novamente tentada após 5 segundos. Se todas as tentativas falharem, então será tratado como erro de aperto de mão de validação.

Ou então, você pode validar a assinatura manualmente, enviando uma solicitação GET para a URL de validação. A inscrição do evento permanece em um estado pendente até que validada. A Url de validação usa a porta 553. Se suas regras de firewall bloqueiam a porta 553, as regras podem precisar ser atualizadas para um aperto de mão manual bem-sucedido.

Para obter um exemplo de lidar com o handshake de validação de assinatura, consulte uma [ amostra C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="troubleshooting-eventsubsciption-validation"></a>Validação de subscção de eventos de solução de problemas

Durante a criação da assinatura do evento, se você estiver vendo uma mensagem\/de erro como "A tentativa de validar o ponto final fornecido https: /your-endpoint-here falhou. Para mais detalhes,\/visite https: /aka.ms/esvalidation", indica que há uma falha no aperto de mão de validação. Para resolver esse erro, verifique os seguintes aspectos:

- Faça um POST HTTP para a url do webhook com uma [amostra de assinaturaValidaçãode](#validation-details) documento de solicitação usando carteiro ou cacho ou ferramenta semelhante.
- Se o webhook estiver implementando um mecanismo de aperto de mão de validação síncrona, verifique se o Código de Validação é devolvido como parte da resposta.
- Se o webhook estiver implementando um mecanismo de aperto de mão de validação assíncrona, verifique se você está o HTTP POST retornando 200 OK.
- Se o webhook estiver retornando 403 (Proibido) na resposta, verifique se o webhook está por trás de um Gateway de aplicativo Azure ou do Web Application Firewall. Se for, então você precisa desativar essas regras de firewall e fazer um HTTP POST novamente:

  920300 (Solicitar falta de um cabeçalho de aceitação, podemos corrigir isso)

  942430 (Detecção restrita de anomalia de caracteres SQL (args): # de caracteres especiais excedidos (12))

  920230 (Várias codificações de URL detectadas)

  942130 (Ataque de Injeção SQL: Tautology SQL Detectado.)

  931130 (Possível Ataque de Inclusão de Arquivos Remotos (RFI) = Referência/Link fora do domínio)

### <a name="event-delivery-security"></a>Segurança de entrega de evento

#### <a name="azure-ad"></a>AD do Azure

Você pode proteger seu ponto final do webhook usando o Azure Active Directory para autenticar e autorizar a Event Grid a publicar eventos em seus pontos finais. Você precisará criar um aplicativo de diretório ativo do Azure, criar um princípio de função e serviço em seu aplicativo que autoriza a Grade de Eventos e configurar a assinatura do evento para usar o Aplicativo Azure AD. [Saiba como configurar OAD com a Grade de Eventos](secure-webhook-delivery.md).

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

O recurso é o caminho para o tópico da grade de eventos para o qual você está enviando eventos. Por exemplo, um caminho `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`de recurso válido é: . Para ver todas as versões de API suportadas, consulte os [tipos de recursos microsoft.eventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

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

Todos os eventos ou dados gravados em disco pelo serviço Event Grid são criptografados por uma chave gerenciada pela Microsoft, garantindo que ele seja criptografado em repouso. Além disso, o período máximo de tempo que os eventos ou dados retidos são de 24 horas em adesão à [política de reteste da Grade de Eventos](delivery-and-retry.md). Event Grid excluirá automaticamente todos os eventos ou dados após 24 horas, ou o tempo de evento para viver, o que for menor.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validação de ponto final com CloudEvents v1.0
Se você já está familiarizado com event grid, você pode estar ciente do aperto de mão de validação de ponto final da Event Grid para evitar abusos. CloudEvents v1.0 implementa sua própria [semântica de proteção](security-authentication.md#webhook-event-delivery) contra abuso usando o método HTTP OPTIONS. Saiba mais sobre isso [aqui](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Ao usar o esquema CloudEvents para saída, event grid usa com a proteção de abuso CloudEvents v1.0 no lugar do mecanismo de evento de validação event Grid.

## <a name="next-steps"></a>Próximas etapas

- Para uma introdução ao Event Grid, consulte [Sobre a Grade de Eventos](overview.md)
