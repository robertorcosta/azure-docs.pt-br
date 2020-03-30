---
title: Recursos HTTP em Funções Duráveis - Funções Azure
description: Conheça os recursos HTTP integrados na extensão Funções Duráveis para Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 29d837446960b7535b26284efdfab7a1c59ea968
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132509"
---
# <a name="http-features"></a>Recursos HTTP

Durable Functions possui vários recursos que facilitam a incorporação de orquestrações e entidades duráveis em fluxos de trabalho HTTP. Este artigo entra em detalhes sobre algumas dessas características.

## <a name="exposing-http-apis"></a>Expondo APIs HTTP

Orquestrações e entidades podem ser invocadas e gerenciadas usando solicitações HTTP. A extensão Funções Duráveis expõe APIs HTTP incorporadas. Ele também fornece APIs para interagir com orquestrações e entidades de dentro de funções desencadeadas pelo HTTP.

### <a name="built-in-http-apis"></a>APIs HTTP incorporadas

A extensão Funções Duráveis adiciona automaticamente um conjunto de APIs HTTP ao host Funções do Azure. Com essas APIs, você pode interagir e gerenciar orquestrações e entidades sem escrever nenhum código.

As seguintes APIs HTTP incorporadas são suportadas.

* [Inicie uma nova orquestração](durable-functions-http-api.md#start-orchestration)
* [Exemplo de orquestração de consulta](durable-functions-http-api.md#get-instance-status)
* [Encerrar a instância de orquestração](durable-functions-http-api.md#terminate-instance)
* [Envie um evento externo para uma orquestração](durable-functions-http-api.md#raise-event)
* [História da orquestração de expurgo](durable-functions-http-api.md#purge-single-instance-history)
* [Envie um evento de operação para uma entidade](durable-functions-http-api.md#signal-entity)
* [Obter o estado de uma entidade](durable-functions-http-api.md#get-entity)
* [Consultar a lista de entidades](durable-functions-http-api.md#list-entities)

Consulte o [artigo HTTP APIs](durable-functions-http-api.md) para obter uma descrição completa de todas as APIs HTTP incorporadas expostas pela extensão Funções Duráveis.

### <a name="http-api-url-discovery"></a>Descoberta de URL na API HTTP

A [vinculação do cliente de orquestração](durable-functions-bindings.md#orchestration-client) expõe APIs que podem gerar cargas de resposta HTTP convenientes. Por exemplo, ele pode criar uma resposta contendo links para APIs de gerenciamento para uma instância específica de orquestração. Os exemplos a seguir mostram uma função http-trigger que demonstra como usar esta API para uma nova instância de orquestração:

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

Iniciar uma função orquestradora usando as funções http-trigger mostradas anteriormente pode ser feito usando qualquer cliente HTTP. O seguinte comando cURL inicia `DoWork`uma função orquestradora chamada:

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Em seguida, é uma resposta `abc123` de exemplo para uma orquestração que tem como seu ID. Alguns detalhes foram removidos para clareza.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

No exemplo anterior, cada um `Uri` dos campos que terminam corresponde a uma API HTTP incorporada. Você pode usar essas APIs para gerenciar a instância de orquestração de destino.

> [!NOTE]
> O formato dos URLs do webhook depende de qual versão do host Funções do Azure você está executando. O exemplo anterior é para o host Funções 2.0 do Azure.

Para obter uma descrição de todas as APIs HTTP incorporadas, consulte a [referência HTTP API](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Acompanhamento de operação assíncrona

A resposta HTTP mencionada anteriormente foi criada para ajudar a implementar APIs assíncronas HTTP de execução longa com as Durable Functions. Esse padrão é às vezes referido como o *padrão de consumo de votação.* O fluxo de cliente/servidor funciona da seguinte maneira:

1. O cliente emite uma solicitação HTTP para iniciar um processo de longa duração como uma função orquestradora.
1. O gatilho HTTP de destino retorna uma resposta HTTP 202 com um cabeçalho de localização que tem o valor "statusQueryGetUri".
1. O cliente pesquisa a URL no cabeçalho Localização. O cliente continua a ver respostas HTTP 202 com um cabeçalho de localização.
1. Quando a ocorrência termina ou falha, o ponto final no cabeçalho de localização retorna HTTP 200.

Este protocolo permite a coordenação de processos de longa duração com clientes ou serviços externos que podem sondar um ponto final HTTP e seguir o cabeçalho de localização. Tanto as implementações de cliente quanto de servidor desse padrão são incorporadas às APIs HTTP de funções duráveis.

> [!NOTE]
> Todas as ações baseadas em HTTP fornecidas pelos [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/) dão suporte ao padrão de operação assíncrona padrão. Essa capacidade possibilita inserir uma função durável de execução longa como parte de um fluxo de trabalho dos Aplicativos Lógicos. Você pode encontrar mais detalhes sobre o suporte a Aplicativos Lógicos para padrões HTTP assíncronos nas ações de fluxo de trabalho do [Azure Logic Apps e aciona a documentação](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> Interações com orquestrações podem ser feitas a partir de qualquer tipo de função, não apenas funções acionadas por HTTP.

Para obter mais informações sobre como gerenciar orquestrações e entidades usando APIs de clientes, consulte o [artigo de gerenciamento de instâncias](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>Consumindo APIs HTTP

Como descrito nas restrições do [código de função do orquestrador,](durable-functions-code-constraints.md)as funções orquestradoras não podem fazer I/O diretamente. Em vez disso, eles normalmente chamam [funções de atividade](durable-functions-types-features-overview.md#activity-functions) que fazem operações de I/O.

Começando com funções duráveis 2.0, as orquestrações podem consumir nativamente APIs HTTP usando a vinculação do [gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger).

O código de exemplo a seguir mostra uma função orquestradora fazendo uma solicitação HTTP de saída:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Usando a ação "chamar HTTP", você pode fazer as seguintes ações em suas funções orquestradoras:

* Ligue para as APIs HTTP diretamente das funções de orquestração, com algumas limitações que são mencionadas posteriormente.
* Suporte automaticamente aos padrões de pesquisa de status HTTP 202 do lado do cliente.
* Use [identidades gerenciadas do Azure](../../active-directory/managed-identities-azure-resources/overview.md) para fazer chamadas HTTP autorizadas para outros pontos finais do Azure.

A capacidade de consumir APIs HTTP diretamente de funções orquestradoras é destinada como uma conveniência para um determinado conjunto de cenários comuns. Você pode implementar todos esses recursos sozinho usando funções de atividade. Em muitos casos, as funções de atividade podem lhe dar mais flexibilidade.

### <a name="http-202-handling"></a>Manuseio http 202

A API "chamar HTTP" pode implementar automaticamente o lado cliente do padrão de consumo de votação. Se uma API chamada retornar uma resposta HTTP 202 com um cabeçalho de localização, a função orquestradora automaticamente pesquisa o recurso Localização até receber uma resposta diferente de 202. Esta resposta será a resposta retornada ao código de função do orquestrador.

> [!NOTE]
> As funções do orquestrador também suportam nativamente o padrão de consumo de pesquisa do lado do servidor, conforme descrito no [rastreamento de operação Async](#async-operation-tracking). Esse suporte significa que orquestrações em um aplicativo de função podem facilmente coordenar as funções do orquestrador em outros aplicativos de função. Isso é semelhante ao conceito [de suborquestração,](durable-functions-sub-orchestrations.md) mas com suporte para comunicação entre aplicativos. Esse suporte é particularmente útil para o desenvolvimento de aplicativos no estilo microservice.

### <a name="managed-identities"></a>Identidades gerenciadas

As funções duráveis suportam nativamente chamadas para APIs que aceitam tokens Azure Active Directory (Azure AD) para autorização. Esse suporte usa [identidades gerenciadas pelo Azure](../../active-directory/managed-identities-azure-resources/overview.md) para adquirir esses tokens.

O código a seguir é um exemplo de uma função orquestradora .NET. A função faz chamadas autenticadas para reiniciar uma máquina virtual usando as máquinas virtuais Do Azure Resource Manager [REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines).

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

---

No exemplo anterior, `tokenSource` o parâmetro é configurado para adquirir tokens Azure AD para [o Azure Resource Manager](../../azure-resource-manager/management/overview.md). Os tokens são identificados `https://management.core.windows.net`pelo recurso URI . O exemplo pressupõe que o aplicativo de função atual esteja sendo executado localmente ou foi implantado como um aplicativo de função com uma identidade gerenciada. Supõe-se que a identidade local ou a identidade gerenciada tenham `myRG`permissão para gerenciar VMs no grupo de recursos especificado .

Em tempo de execução, a fonte de token configurada retorna automaticamente um token de acesso OAuth 2.0. A fonte então adiciona o token como um token portador ao cabeçalho de autorização da solicitação de saída. Este modelo é uma melhoria em relação à adição manual de cabeçalhos de autorização às solicitações HTTP pelas seguintes razões:

* A atualização do token é tratada automaticamente. Não precisa se preocupar com tokens vencidos.
* Os tokens nunca são armazenados no estado de orquestração durável.
* Você não precisa escrever nenhum código para gerenciar a aquisição de tokens.

Você pode encontrar um exemplo mais completo na [amostra c# RestartVMs pré-compilada](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs).

As identidades gerenciadas não se limitam ao gerenciamento de recursos do Azure. Você pode usar identidades gerenciadas para acessar qualquer API que aceite tokens portadores do Azure AD, incluindo serviços Azure da Microsoft e aplicativos web de parceiros. O aplicativo web de um parceiro pode até ser outro aplicativo de função. Para obter uma lista de serviços Azure da Microsoft que suportam autenticação com o Azure AD, consulte [os serviços do Azure que suportam a autenticação Azure AD](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Limitações

O suporte incorporado para chamar APIs HTTP é um recurso de conveniência. Não é apropriado para todos os cenários.

As solicitações HTTP enviadas por funções orquestradoras e suas respostas são serializadas e persistentes como mensagens de fila. Esse comportamento de fila garante que as chamadas HTTP sejam [confiáveis e seguras para a repetição de orquestração](durable-functions-orchestrations.md#reliability). No entanto, o comportamento de fila também tem limitações:

* Cada solicitação HTTP envolve latência adicional quando comparada a um cliente HTTP nativo.
* Grandes mensagens de solicitação ou resposta que não podem caber em uma mensagem de fila podem degradar significativamente o desempenho de orquestração. A sobrecarga de descarregar cargas de mensagens para o armazenamento blob pode causar uma potencial degradação do desempenho.
* Não são suportadas cargas de streaming, em pedaços e binárias.
* A capacidade de personalizar o comportamento do cliente HTTP é limitada.

Se alguma dessas limitações pode afetar seu caso de uso, considere, em vez disso, usar funções de atividade e bibliotecas de clientes HTTP específicas do idioma para fazer chamadas HTTP de saída.

> [!NOTE]
> Se você é um desenvolvedor .NET, você pode se perguntar por que esse recurso usa os tipos **DurableHttpRequest** e **DurableHttpResponse** em vez dos tipos .NET **HttpRequestMessage** e **HttpResponseMessage.**
>
> Essa opção de design é intencional. A principal razão é que os tipos personalizados ajudam a garantir que os usuários não façam suposições incorretas sobre os comportamentos suportados do cliente HTTP interno. Tipos específicos para Funções Duráveis também tornam possível simplificar o design da API. Eles também podem disponibilizar mais facilmente recursos especiais, como [a integração de identidade gerenciada](#managed-identities) e o [padrão de consumo de votação.](#http-202-handling) 

### <a name="extensibility-net-only"></a>Extensibilidade (somente.NET)

Personalizar o comportamento do cliente HTTP interno da orquestração é possível usando [a injeção de dependência do Azure Functions .NET](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection). Essa habilidade pode ser útil para fazer pequenas mudanças comportamentais. Ele também pode ser útil para testar a unidade do cliente HTTP injetando objetos falsos.

O exemplo a seguir demonstra o uso de injeção de dependência para desativar a validação do certificado TLS/SSL para funções orquestradoras que chamam de pontos finais HTTP externos.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Conheça entidades duráveis](durable-functions-entities.md)
