---
title: Ingerir telemetria do Hub IoT
titleSuffix: Azure Digital Twins
description: Consulte como ingerir mensagens de telemetria do dispositivo do Hub IoT.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7c73f007f85a963a09de4e05222082fd52f784c0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131558"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingerir telemetria do Hub IoT no gêmeos digital do Azure

O Azure digital gêmeos é orientado com dados de dispositivos IoT e outras fontes. Uma fonte comum de dados de dispositivo a ser usada no Azure digital gêmeos é o [Hub IOT](../iot-hub/about-iot-hub.md).

Durante a visualização, o processo de ingestão de dados no Azure digital gêmeos é configurar um recurso de computação externo, como uma [função do Azure](../azure-functions/functions-overview.md), que recebe os dados e usa as [APIs do DigitalTwins](how-to-use-apis-sdks.md) para definir propriedades ou disparar eventos de telemetria em [gêmeos digital](concepts-twins-graph.md) adequadamente. 

Este documento de instruções orienta o processo de gravação de uma função do Azure que pode incluir telemetria do Hub IoT.

## <a name="example-telemetry-scenario"></a>Cenário de telemetria de exemplo

Este "como" descreve como enviar mensagens do Hub IoT para o Azure digital gêmeos usando uma função do Azure. Há muitas configurações possíveis e estratégias de correspondência que você pode usar para isso, mas o exemplo deste artigo contém as seguintes partes:
* Um dispositivo de termômetro no Hub IoT, com uma ID de dispositivo conhecida.
* Um teledigital para representar o dispositivo, com uma ID correspondente
* Uma teledigital que representa uma sala

> [!NOTE]
> Este exemplo usa uma correspondência de ID direta entre a ID do dispositivo e uma ID de mesa digital correspondente, mas é possível fornecer mapeamentos mais sofisticados do dispositivo para seu número de mesa (como com uma tabela de mapeamento).

Sempre que um evento de telemetria de temperatura é enviado pelo dispositivo de termômetro, a propriedade de *temperatura* da *sala de espaço* é atualizada. Para fazer isso acontecer, você mapeará de um evento de telemetria em um dispositivo para um setter de propriedade na folha de dispositivos digitais. Você usará as informações de topologia do [grafo de entrelaçamento](concepts-twins-graph.md) para localizar a *sala* e, em seguida, poderá definir a propriedade de pressione. Em outros cenários, os usuários talvez queiram definir uma propriedade no conjunto de entrelaças correspondente (neste exemplo, o conjunto de entrelaçar com a ID de *123*). O Azure digital gêmeos oferece muita flexibilidade para decidir como os dados de telemetria são mapeados no gêmeos. 

Este cenário é descrito em um diagrama abaixo:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Um dispositivo de Hub IoT envia telemetria de temperatura por meio de tópicos do Hub IoT, da grade de eventos ou do sistema para uma função do Azure, que atualiza uma propriedade de temperatura em gêmeos no Azure digital gêmeos." border="false":::

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar com este exemplo, você precisará concluir os seguintes pré-requisitos.
1. Crie um Hub IoT. Consulte a seção *criar um hub IOT* deste [início rápido do Hub IOT](../iot-hub/quickstart-send-telemetry-cli.md) para obter instruções.
2. Crie pelo menos uma função do Azure para processar eventos do Hub IoT. Consulte [*como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md) para criar uma função básica do Azure que pode se conectar ao Azure digital gêmeos e chamar funções da API gêmeos do Azure digital. O restante deste "como" será criado nessa função.
3. Configure um destino de evento para dados de Hub. Na [portal do Azure](https://portal.azure.com/), navegue até a instância do Hub IOT. Em *eventos*, crie uma assinatura para sua função do Azure. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Portal do Azure: adicionar uma assinatura de evento":::

4. Na página *criar assinatura de evento* , preencha os campos da seguinte maneira:
   * Em *detalhes da assinatura de evento*, nomeie a assinatura como você deseja
   * Em *tipos de evento*, escolha *telemetria do dispositivo* como o tipo de evento para filtrar
      - Adicione filtros a outros tipos de evento, se desejar.
   * Em *detalhes do ponto de extremidade*, selecione sua função do Azure como um ponto de extremidade

## <a name="create-an-azure-function-in-visual-studio"></a>Criar uma função do Azure no Visual Studio

Esta seção usa as mesmas etapas de inicialização do Visual Studio e o esqueleto do Azure function de [*como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md). O esqueleto manipula a autenticação e cria um cliente de serviço, pronto para processar dados e chamar as APIs do Azure digital gêmeos em resposta. 

O coração da função de esqueleto é:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Nas etapas a seguir, você adicionará um código específico a ele para processar eventos de telemetria IoT do Hub IoT.  

## <a name="add-telemetry-processing"></a>Adicionar processamento de telemetria

Os eventos de telemetria vêm na forma de mensagens do dispositivo. A primeira etapa da adição do código de processamento de telemetria é a extração da parte relevante desta mensagem de dispositivo do evento de grade de eventos. 

Dispositivos diferentes podem estruturar suas mensagens de forma diferente, portanto, o código dessa etapa depende do dispositivo conectado. 

O código a seguir mostra um exemplo de um dispositivo simples que envia telemetria como JSON. O exemplo extrai a ID do dispositivo que enviou a mensagem, bem como o valor de temperatura.

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

Lembre-se de que a finalidade deste exercício é atualizar a temperatura de uma *sala* no grafo de entrelaçamento. Isso significa que nosso destino da mensagem não é o email digital que está associado a esse dispositivo, mas a *sala* é o seu pai. Você pode encontrar o myparent usando o valor da ID do dispositivo que você extraiu da mensagem de telemetria usando o código acima.

Para fazer isso, use as APIs do gêmeos digital do Azure para acessar as relações de entrada para o dispositivo que representa o "(que, nesse caso, tem a mesma ID do dispositivo). Na relação de entrada, você pode encontrar a ID do pai com o trecho de código abaixo.

O trecho de código a seguir mostra como recuperar relações de entrada de um "/".

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

O pai de seu FileUp está na propriedade *SourceID* da relação.

É bastante comum que um modelo de um matreme representando um dispositivo tenha apenas uma única relação de entrada. Nesse caso, você pode escolher a primeira relação (e somente) retornada. Se seus modelos permitirem vários tipos de relações com esse '/', talvez seja necessário especificar mais para escolher entre várias relações de entrada. Uma maneira comum de fazer isso é escolher a relação por `RelationshipName` . 

Depois que você tiver a ID do pai de '//' que representa a *sala*, poderá "patch" (fazer atualizações) que se entrelaçar. Para fazer isso, use o seguinte código:

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>Código de função do Azure completo

Usando o código dos exemplos anteriores, aqui está a função inteira do Azure no contexto:

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

A função do utilitário para localizar as relações de entrada:
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

E a função do utilitário para corrigir a atualização de entrelaçamento:
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

Agora você tem uma função do Azure que é equipada para ler e interpretar os dados do cenário provenientes do Hub IoT.

## <a name="debug-azure-function-apps-locally"></a>Depurar aplicativos de funções do Azure localmente

É possível depurar o Azure Functions com um gatilho de grade de eventos localmente. Para obter mais informações sobre isso, consulte [*depurar o gatilho de grade de eventos localmente*](../azure-functions/functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Próximas etapas

Leia sobre entrada e saída de dados com o Azure digital gêmeos:
* [*Conceitos: integração com outros serviços*](concepts-integration.md)
