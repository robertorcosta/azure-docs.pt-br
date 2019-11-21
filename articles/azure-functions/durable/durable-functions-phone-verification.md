---
title: Interação humana e tempos limite nas Funções Duráveis – Azure
description: Saiba como lidar com interação humana e tempos limite na extensão de Funções Duráveis do Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 9346c53ec122b3e6fac124298029c7f8e70bf622
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232818"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Interação humana nas Funções Duráveis – exemplo de verificação por telefone

Este exemplo demonstra como criar uma orquestração de [Funções Duráveis](durable-functions-overview.md) que envolve interação humana. Sempre que uma pessoa real está envolvida em um processo automatizado, o processo deve ser capaz de enviar notificações para a pessoa e de receber respostas de forma assíncrona. Ele também deve contar com a possibilidade de a pessoa não estar disponível. (É nesta última parte que os tempos limite tornam-se importantes.)

Este exemplo implementa um sistema de verificação por telefone baseado em SMS. Esses tipos de fluxo costumam ser usados para verificar o número de telefone de um cliente ou para MFA (autenticação multifator). It is a powerful example because the entire implementation is done using a couple small functions. Não é necessário um armazenamento de dados externo, como um banco de dados.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Visão geral do cenário

A verificação por telefone é usada para confirmar a identidade dos usuários finais de seu aplicativo e que eles não são remetentes de spam. A autenticação multifator é um caso de uso comum para proteger contas de usuário contra hackers. O desafio ao implementar sua própria verificação por telefone é que ela requer uma **interação com estado** com uma pessoa. Normalmente, um usuário final recebe um código (por exemplo, um número de 4 dígitos) e deve responder **dentro de um período razoável**.

O Azure Functions comum é sem estado (assim como muitos outros pontos de extremidade de nuvem em outras plataformas), portanto, esses tipos de interações envolvem explicitamente o gerenciamento de estado externamente em um banco de dados ou algum outro armazenamento persistente. Além disso, a interação deve ser dividida em várias funções que possam ser coordenadas juntas. Por exemplo, você precisa de pelo menos uma função para decidir sobre um código, persisti-lo em algum lugar e enviá-lo para o telefone do usuário. Além disso, você precisa de pelo menos uma outra função para receber uma resposta do usuário e, de alguma forma, mapeá-la de volta para a chamada de função original para fazer a validação do código. O tempo limite também é um aspecto importante para garantir a segurança. It can get fairly complex quickly.

A complexidade desse cenário é bastante reduzida quando você usa as Funções Duráveis. Como você verá neste exemplo, uma função de orquestrador pode gerenciar a interação com estado facilmente e sem envolver nenhum armazenamento de dados externo. Como as funções de orquestrador são *duráveis*, esses fluxos interativos também são altamente confiáveis.

## <a name="configuring-twilio-integration"></a>Configurando a integração com o Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>As funções

Este artigo aborda as seguintes funções no aplicativo de exemplo:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

The following sections explain the configuration and code that is used for C# scripting and JavaScript. O código para desenvolvimento no Visual Studio é exibido no final do artigo.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A orquestração de verificação de SMS (código de exemplo do Visual Studio Code e do portal do Azure)

A função **E4_SmsPhoneVerification** usa o *function.json* padrão para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Este é o código que implementa a função:

### <a name="c-script"></a>Script do C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Uma vez iniciada, essa função de orquestrador faz o seguinte:

1. Obtém um número de telefone para o qual *enviará* a notificação de SMS.
2. Chama **E4_SendSmsChallenge** para enviar uma mensagem SMS para o usuário e retorna o código de desafio de 4 dígitos esperado.
3. Cria um temporizador durável que dispara 90 segundos a partir da hora atual.
4. Em paralelo com o temporizador, aguarda um evento **SmsChallengeResponse** do usuário.

O usuário recebe uma mensagem SMS com um código de 4 dígitos. Ele tem 90 segundos para enviar o mesmo código de 4 dígitos para a instância da função de orquestrador para concluir o processo de verificação. Se enviar o código errado, ele terá mais três tentativas para acertar (na mesma janela de 90 segundos).

> [!NOTE]
> Pode não ser óbvio a princípio, mas essa função de orquestrador é totalmente determinística. It is deterministic because the `CurrentUtcDateTime` (.NET) and `currentUtcDateTime` (JavaScript) properties are used to calculate the timer expiration time, and these properties return the same value on every replay at this point in the orchestrator code. This behavior is important to ensure that the same `winner` results from every repeated call to `Task.WhenAny` (.NET) or `context.df.Task.any` (JavaScript).

> [!WARNING]
> É importante [cancelar os temporizadores](durable-functions-timers.md) se você não precisar mais que eles expirem, como no exemplo acima quando uma resposta ao desafio é aceita.

## <a name="send-the-sms-message"></a>Enviar a mensagem SMS

A função **E4_SendSmsChallenge** usa a associação ao Twilio para enviar a mensagem SMS com o código de 4 dígitos para o usuário final. O *function.json* é definido da seguinte maneira:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

E este é o código que gera o código de desafio de 4 dígitos e envia a mensagem SMS:

### <a name="c-script"></a>Script do C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

Essa função **E4_SendSmsChallenge** é chamada apenas uma vez, mesmo que o processo falhe ou seja reproduzido. Isso é bom porque você não quer que o usuário final receba várias mensagens SMS. O valor retornado `challengeCode` é persistido automaticamente, de modo que a função de orquestrador sempre sabe qual é o código correto.

## <a name="run-the-sample"></a>Execute o exemplo

Usando as funções disparadas por HTTP incluídas no exemplo, você pode iniciar a orquestração enviando a seguinte solicitação HTTP POST:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

A função de orquestrador recebe o número de telefone fornecido e envia imediatamente uma mensagem SMS para ele com um código de verificação de 4 dígitos gerado aleatoriamente &mdash;, por exemplo, *2168*. Em seguida, a função espera durante 90 segundos por uma resposta.

Para responder com o código, você pode usar [`RaiseEventAsync` (.NET) ou `raiseEvent` (JavaScript)](durable-functions-instance-management.md) dentro de outra função ou invocar o webhook de HTTP POST **sendEventUrl**, referenciado na resposta 202 acima, substituindo `{eventName}` pelo nome do evento, `SmsChallengeResponse`:

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Se você enviá-lo antes que o temporizador expire, a orquestração será concluída e o campo `output` será definido como `true`, indicando uma verificação bem-sucedida.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Se deixar que o temporizador expire ou se inserir o código errado quatro vezes, você poderá consultar o status e ver uma saída de função de orquestração `false`, indicando que houve falha na verificação por telefone.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Esta é a orquestração como um único arquivo em C# em um projeto do Visual Studio:

> [!NOTE]
> Você precisará instalar o `Microsoft.Azure.WebJobs.Extensions.Twilio` pacote do Nuget para executar o código de exemplo abaixo.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Próximos passos

This sample has demonstrated some of the advanced capabilities of Durable Functions, notably `WaitForExternalEvent` and `CreateTimer` APIs. Você viu como elas podem ser combinadas com `Task.WaitAny` para implementar um sistema de tempo limite confiável, que geralmente é útil para interagir com pessoas reais. Você pode aprender mais sobre como usar Funções Duráveis lendo uma série de artigos que oferecem uma cobertura aprofundada de tópicos específicos.

> [!div class="nextstepaction"]
> [Ir para o primeiro artigo na série](durable-functions-bindings.md)
