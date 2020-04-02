---
title: Autenticar acesso ao Azure Event Hubs com assinaturas de acesso compartilhadas
description: Este artigo mostra como autenticar o acesso aos recursos do Event Hubs usando assinaturas de acesso compartilhadas.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: 82773ab6decfe15ee1a9d839a1f10a158ae72c42
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521314"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Autenticar o acesso aos recursos do Event Hubs usando assinaturas de acesso compartilhado (SAS)
A assinatura de acesso compartilhado (SAS) lhe dá controle granular sobre o tipo de acesso que você concede aos clientes que têm a assinatura de acesso compartilhado. Aqui estão alguns dos controles que você pode definir em um SAS: 

- O intervalo sobre o qual o SAS é válido, incluindo o tempo de início e o tempo de validade.
- As permissões concedidas pelas SAS. Por exemplo, um sas para um namespace do Event Hubs pode conceder a permissão de ouvir, mas não a permissão de envio.
- Somente clientes que apresentam credenciais válidas podem enviar dados para um hub de eventos.
- Um cliente não pode se passar por outro cliente.
- Um cliente rouge pode ser impedido de enviar dados para um hub de eventos.

Este artigo abrange a autenticação do acesso aos recursos do Event Hubs usando o SAS. Para saber como **autorizar o** acesso aos recursos do Event Hubs usando o SAS, consulte [este artigo](authorize-access-shared-access-signature.md). 

> [!NOTE]
> A Microsoft recomenda que você use credenciais Azure AD quando possível como uma prática recomendada de segurança, em vez de usar as assinaturas de acesso compartilhadas, que podem ser mais facilmente comprometidas. Embora você possa continuar a usar assinaturas de acesso compartilhado (SAS) para conceder acesso fino aos seus recursos do Event Hubs, o Azure AD oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar em revogar um SAS comprometido.
> 
> Para obter mais informações sobre a integração do Azure AD no Azure Event Hubs, consulte [Autorizar o acesso aos Hubs de Eventos usando o Azure AD](authorize-access-azure-active-directory.md). 


## <a name="configuring-for-sas-authentication"></a>Configuração para autenticação SAS
Você pode configurar a regra de autorização de acesso compartilhado EventHubs em um namespace do Event Hubs ou em uma entidade (instância do hub de eventos ou Tópico Kafka em um hub de eventos). A configuração de uma regra de autorização de acesso compartilhado em um grupo de consumidores não é suportada no momento, mas você pode usar regras configuradas em um namespace ou entidade para garantir o acesso ao grupo de consumidores. 

A imagem a seguir mostra como as regras de autorização se aplicam às entidades amostrais. 

![Configurar regra de autorização](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

Neste exemplo, o namespace do Event Hubs de exemplo (ExampleNamespace) tem duas entidades: eh1 e topic1. As regras de autorização são definidas tanto no nível da entidade quanto no nível de namespace.  

O manageRuleNS, sendRuleNS e listenRuleNS regras de autorização aplicam-se tanto à instância do hub de eventos eh1 quanto ao tópico t1. As regras de autorização listenRule-eh e sendRule-eh aplicam-se apenas à instância do hub de eventos eh1 e a regra de autorização sendRuleT se aplica apenas ao tópico tópico1. 

Ao usar a regra de autorização sendRuleNS, os aplicativos clientes podem enviar para eh1 e tópico1. Quando a regra de autorização sendRuleT é usada, ela impõe o acesso granular apenas ao tópico1 e, portanto, os aplicativos clientes que usam esta regra para acesso agora não podem enviar para o eh1, mas apenas para o topic1.

## <a name="generate-a-shared-access-signature-token"></a>Gerar um token de Assinatura de Acesso Compartilhado 
Qualquer cliente que tenha acesso ao nome de uma regra de autorização e a uma de suas chaves de assinatura pode gerar um token SAS. O token é gerado ao criar uma cadeia de caracteres no seguinte formato:

- `se`– Expiração do token instantâneo. Inteiro refletindo segundos desde a época 00:00:00 UTC em 1 de janeiro de 1970 (época UNIX) quando o token expira
- `skn`– Nome da regra de autorização, que é o nome-chave SAS.
- `sr`– URI do recurso que está sendo acessado.
- `sig`– Assinatura.

A seqüência de assinaturas é o hash SHA-256 calculado sobre o recurso URI (escopo descrito na seção anterior) e a representação de seqüência do instantâneo de expiração do token, separada pelo CRLF.

O cálculo de hash é semelhante ao seguinte pseudocódigo e retorna um valor de hash de 256 bits/32 bytes. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

O token contém os valores não hash para que o destinatário possa recalcular o hash com os mesmos parâmetros, verificando se o emissor possui uma chave de assinatura válida.

O URI do recurso é o URI completo do recurso do Barramento de Serviço ao qual o acesso é solicitado. Por exemplo,<namespace>http://<entityPath> `sb://<namespace>.servicebus.windows.net/<entityPath>;` .servicebus.windows.net/ `http://contoso.servicebus.windows.net/eventhubs/eh1`ou seja, .

O URI deve ser codificado por percentual.

A regra de autorização de acesso compartilhado usada para assinar deve ser configurada na entidade especificada por esse URI ou por um de seus pais hierárquicos. Por exemplo, `http://contoso.servicebus.windows.net/eventhubs/eh1` ou `http://contoso.servicebus.windows.net` no exemplo anterior.

Um token SAS é válido para <resourceURI> todos os recursos prefixados com o usado na cadeia de assinaturas.

> [!NOTE]
> Você gera um token de acesso para Hubs de eventos usando a política de acesso compartilhado. Para obter mais informações, consulte [a política de autorização de acesso compartilhado](authorize-access-shared-access-signature.md#shared-access-authorization-policies).

### <a name="generating-a-signaturetoken-from-a-policy"></a>Gerando uma assinatura (token) a partir de uma política 
A seção a seguir mostra a geração de um token SAS usando políticas de assinatura de acesso compartilhado,

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>Java

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Autenticando editores do Event Hubs com SAS 
Um editor de eventos define um ponto de extremidade virtual para um hub de eventos. O editor só pode ser usado para enviar mensagens para um hub de eventos e não receber mensagens.

Normalmente, um hub de eventos emprega um editor por cliente. Todas as mensagens enviadas a um dos publicadores de um hub de eventos são enfileiradas nesse hub de eventos. Os editores permitem o controle de acesso de grãos finos.

Cada cliente dos hubs de eventos recebe um token exclusivo que é carregado no cliente. Os tokens são produzidos de forma que cada token único conceda acesso a diferentes editores exclusivos. Um cliente que possui um token só pode enviar para um editor, e nenhum outro editor. Se vários clientes compartilham o mesmo token, então cada um deles compartilha o editor.

Todos os tokens são atribuídos com chaves SAS. Normalmente, todos os tokens são assinados com a mesma chave. Os clientes não estão cientes da chave, o que impede os clientes de fabricar tokens. Os clientes operam com os mesmos tokens até expirarem.

Por exemplo, para definir as regras de autorização escopo para apenas o envio/publicação para hubs de eventos, você precisa definir uma regra de autorização de envio. Isso pode ser feito em um nível de namespace ou dar um escopo mais granular a uma entidade específica (instância de hubs de eventos ou um tópico). Um cliente ou um aplicativo que é escopo com esse acesso granular é chamado de editor do Event Hubs. Para fazer isso, siga estas etapas:

1. Crie uma chave SAS na entidade que deseja publicar para atribuir o **escopo de envio** nela. Para obter mais informações, consulte [políticas de autorização de acesso compartilhado](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Gere um token SAS com um tempo de validade para um editor específico usando a chave gerada na etapa 1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Forneça o token ao cliente do editor, que só pode enviar para a entidade e para o editor ao qual o token concede acesso.

    Uma vez que o token expira, o cliente perde o acesso ao envio/publicação para a entidade. 


> [!NOTE]
> Embora não seja recomendado, é possível equipar dispositivos com tokens que concedem acesso a um hub de eventos ou a um namespace. Qualquer dispositivo que possua este token pode enviar mensagens diretamente para esse hub de eventos. Além disso, o dispositivo não pode ser incluído na lista de bloqueados para ser impedido de enviar para esse hub de eventos.
> 
> É sempre recomendado dar escopos específicos e granulares.

> [!IMPORTANT]
> Depois que os tokens são criados, cada cliente é configurado com seu próprio token exclusivo.
>
> Quando o cliente envia dados para um hub de eventos, ele marca sua solicitação com o token. Para evitar que um invasor intercepte e roube o token, a comunicação entre o cliente e o hub de eventos deve ocorrer em um canal criptografado.
> 
> Se um token for roubado por um invasor, este poderá representar o cliente cujo token foi roubado. A lista negra de um editor torna esse cliente inutilizável até que ele receba um novo token que usa um editor diferente.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Autenticação de hubs de eventos com SAS 
Para autenticar aplicativos back-end que consomem dos dados gerados pelos produtores do Event Hubs, a autenticação do token Event Hubs exige que seus clientes tenham os direitos **de gerenciamento** ou os privilégios de **escuta** atribuídos ao seu namespace ou instância do hub de eventos do Event Hubs. Os dados são consumidos a partir de Event Hubs usando grupos de consumidores. Embora a política SAS lhe dê escopo granular, esse escopo é definido apenas no nível da entidade e não no nível do consumidor. Isso significa que os privilégios definidos no nível namespace ou na instância do hub de eventos ou no nível de tópico serão aplicados aos grupos de consumidores dessa entidade.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Autorizar o uso do SAS](authenticate-shared-access-signature.md)
- [Autorize o uso do controle de acesso role-base (RBAC)](authenticate-shared-access-signature.md)
- [Saiba mais sobre hubs de eventos](event-hubs-about.md)

Veja os seguintes artigos relacionados:

- [Autenticar solicitações ao Azure Event Hubs a partir de um aplicativo usando o Azure Active Directory](authenticate-application.md)
- [Autenticar uma identidade gerenciada com o Azure Active Directory para acessar recursos do Event Hubs](authenticate-managed-identity.md)
- [Autorize o acesso aos recursos do Event Hubs usando o Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorize o acesso aos recursos do Event Hubs usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)