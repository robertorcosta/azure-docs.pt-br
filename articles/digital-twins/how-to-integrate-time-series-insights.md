---
title: Integrar com o Time Series Insights
titleSuffix: Azure Digital Twins
description: Consulte como configurar rotas de eventos do Azure digital gêmeos para Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9be44d29ab08a7404bfb14d691bb5bb339284714
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131592"
---
# <a name="integrate-digital-twins-with-azure-time-series-insights"></a>Integrar gêmeos digital com Azure Time Series Insights

Nesta referência, você aprenderá a integrar o gêmeos digital do Azure ao [TSI (Azure Time Series insights)](../time-series-insights/overview-what-is-tsi.md). Essa solução permitirá que você reúna e analise dados históricos sobre sua solução de IoT. O gêmeos digital do Azure é uma ótima opção para alimentar dados em Time Series Insights, pois permite que você correlacione vários fluxos de dados e padronize suas informações antes de enviá-las para Time Series Insights. 

## <a name="solution-architecture"></a>Arquitetura da solução

Você estará anexando análises de séries temporais ao gêmeos digital do Azure por meio do caminho abaixo.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Uma exibição dos serviços do Azure em um cenário de ponta a ponta, realçando Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Pré-requisitos

* Você precisa de uma instância do gêmeos digital do Azure, que pode atualizar informações de cópia apenas algumas vezes para ver os dados rastreados no Time Series Insights. 
    * Se você não tiver um, siga o tutorial do Azure digital gêmeos [*: Conecte uma solução de ponta a ponta*](./tutorial-end-to-end.md) para configurar uma instância do gêmeos digital do Azure e um dispositivo IOT virtual para gerar alterações de cópia.

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Criar uma rota e um filtro para as notificações de atualização do entrelaçamento

As instâncias de gêmeos digitais do Azure podem emitir [eventos de atualização de entrelaçar](how-to-interpret-event-data.md) sempre que um estado de entrelaçamento é atualizado. Você criará uma rota que direcionará esses eventos de atualização para um hub de eventos para processamento adicional.

O tutorial do Azure digital gêmeos [*: conectar uma solução de ponta a ponta*](./tutorial-end-to-end.md) percorre um cenário em que um termômetro é usado para atualizar um atributo de temperatura anexado a uma sala de salas. Esse padrão usa as atualizações de atualização, em vez de encaminhar a telemetria de um dispositivo IoT, que oferece a você a flexibilidade de alterar a fonte de dados subjacente sem a necessidade de atualizar sua lógica de Time Series Insights.

1. Crie um namespace de Hub de eventos, que receberá eventos de sua instância de gêmeos digital do Azure. Você pode usar as instruções de CLI do Azure abaixo ou usar o portal do Azure: [*início rápido: criar um hub de eventos usando portal do Azure*](../event-hubs/event-hubs-create.md).

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Crie um hub de eventos.

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```

3. Crie uma [regra de autorização](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) com permissões de envio e recebimento.

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

4. Crie um ponto de extremidade para vincular seu tópico da grade de eventos ao Azure digital gêmeos.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace> --eventhub <twins event hub name> --eventhub-policy <twins auth rule> -n <your-Azure-Digital-Twins-instance-name>
    ```

5. Crie uma rota no gêmeos digital do Azure para enviar eventos de atualização de entrelaçar para seu ponto de extremidade. O filtro nessa rota permitirá que somente as mensagens de atualização de entrelaçamento sejam passadas para o ponto de extremidade.

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Hub-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function"></a>Criar uma função do Azure 

Em seguida, você criará uma função disparada por hubs de eventos dentro de um novo aplicativo de funções, o aplicativo de funções do tutorial de ponta a ponta ([*tutorial: conectar uma solução de ponta a ponta*](./tutorial-end-to-end.md)). Essa função converterá essas atualizações de seu formato original como documentos de patch JSON em objetos JSON que contêm apenas valores atualizados e adicionados de seu gêmeos.

Para obter mais informações sobre como usar hubs de eventos com o Azure functions, consulte [*gatilho de hubs de eventos do Azure para Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Dentro de seu aplicativo de funções publicado, substitua o código de função pelo código a seguir.

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-fx-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("alkarche-tsi-demo-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

A partir daqui, a função enviará os objetos JSON que ele cria para um segundo Hub de eventos, ao qual você se conectará ao Time Series Insights.

## <a name="send-telemetry-to-an-event-hub"></a>Enviar telemetria para um hub de eventos

Agora, você criará um segundo Hub de eventos e configurará sua função para transmitir sua saída para o Hub de eventos. Esse Hub de eventos será então conectado a Time Series Insights.

### <a name="create-an-event-hub"></a>Criar um hub de eventos

Você pode usar as instruções de CLI do Azure abaixo ou usar o portal do Azure: [*início rápido: criar um hub de eventos usando portal do Azure*](../event-hubs/event-hubs-create.md).

1. Preparar o namespace do hub de eventos e o nome do grupo de recursos do anterior 

2. Criar um hub de eventos
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <TSI event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```
3. Criar uma [regra de autorização](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) com permissões de envio e recebimento
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <event hub name> --name <TSI auth rule>
    ```

### <a name="configure-your-function"></a>Configurar sua função

Você precisará definir uma variável de ambiente em seu aplicativo de funções anteriormente, contendo a cadeia de conexão do hub de eventos.

#### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Definir a Time Series Insights cadeia de conexão do hub de eventos

1. Obtenha a [cadeia de conexão do hub de eventos](../event-hubs/event-hubs-get-connection-string.md) para as regras de autorização que você criou acima para o hub de time Series insights:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

2. Em seu aplicativo de funções, crie uma configuração de aplicativo contendo a cadeia de conexão:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

#### <a name="set-the-twins-event-hub-connection-string"></a>Definir a cadeia de conexão do hub de eventos gêmeos

1. Obtenha a [cadeia de conexão do hub de eventos](../event-hubs/event-hubs-get-connection-string.md) para as regras de autorização que você criou acima para o Hub gêmeos.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Em seu aplicativo de funções, crie uma configuração de aplicativo contendo a cadeia de conexão:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Criar e conectar uma instância de Time Series Insights

Em seguida, você irá configurar uma instância de Time Series Insights para receber os dados do segundo Hub de eventos. Para obter mais detalhes sobre este processo, consulte [ *tutorial: configurar um ambiente de Azure Time Series insights Gen2 PAYG*](../time-series-insights/tutorials-set-up-tsi-environment.md)

1. Na portal do Azure, comece a criar um recurso de Time Series Insights. 
    1. Selecione o tipo de preço **PAYG (visualização)** .
    2. Será necessário escolher uma ID de série temporal para esse ambiente. Sua ID de série temporal pode ter até três valores que você usará para pesquisar seus dados em Time Series Insights. Para este tutorial, você pode usar **$dtId**. Leia mais sobre como selecionar um valor de ID nas [*práticas recomendadas para escolher uma ID de série temporal*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="O UX do portal de criação para um ambiente de Time Series Insights. O tipo de preço PAYG (visualização) é selecionado e o nome da propriedade ID da série temporal é $dtId":::

2. Selecione **Avançar: origem do evento** e selecione as informações dos hubs de eventos acima. Você também precisará criar um novo grupo de consumidores de hubs de eventos.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="O UX do portal de criação para uma fonte de evento de Time Series Insights ambiente. Você está criando uma origem do evento com as informações do hub de eventos acima. Você também está criando um novo grupo de consumidores.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Começar a enviar dados de IoT para o Azure digital gêmeos

Para começar a enviar dados para Time Series Insights, você precisará começar a atualizar as propriedades de atualização de troca digital com valores de dados de alteração. Use o comando [AZ DT myupdate](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) .

Se você estiver usando o tutorial de ponta a ponta para auxiliar na configuração do ambiente, você pode começar a enviar dados de IoT simulados executando o `DeviceSimulator` projeto do tutorial do gêmeos digital do Azure [*: conectar uma solução de ponta a ponta*](tutorial-end-to-end.md). As instruções estão na seção [*configurar e executar a simulação*](tutorial-end-to-end.md#configure-and-run-the-simulation) do tutorial.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualize seus dados no Time Series Insights

Agora, os dados devem estar fluindo para sua instância de Time Series Insights, prontos para serem analisados. Siga as etapas abaixo para explorar os dados recebidos.

1. Abra sua instância de Time Series Insights no portal do Azure. Visite a URL do Time Series Insights Explorer mostrada na visão geral.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Clique na URL do Time Series Insights Explorer na guia Visão geral do ambiente de Time Series Insights":::

2. No Gerenciador, você verá os três gêmeos mostrados à esquerda. Clique em **thermostat67**, selecione **patch_value**e clique em **Adicionar**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Clique em * * thermostat67 * *, selecione * * temperatura * * e clique em * * Adicionar * *":::

3. Agora você deve estar vendo as leituras de temperatura iniciais de seu termostato, conforme mostrado abaixo. A mesma leitura de temperatura é atualizada para room21 e FLOOR1, e você pode visualizar esses fluxos de dados em tandem.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Os dados iniciais de temperatura estão grafos no Gerenciador de TSI. É uma linha de valores aleatórios entre 68 e 85":::

4. Se você permitir que a simulação seja executada por muito mais tempo, sua visualização terá uma aparência semelhante a esta:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Os dados de temperatura para cada linhas de entrelaçamento estão grafos em três linhas paralelas de cores diferentes.":::

## <a name="next-steps"></a>Próximas etapas

Os gêmeos digitais são armazenados por padrão como uma hierarquia simples no Time Series Insights, mas podem ser aprimorados com informações de modelo e uma hierarquia de vários níveis para a organização. Para saber mais sobre esse processo, leia: 

* [*Tutorial: definir e aplicar um modelo*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Você pode escrever uma lógica personalizada para fornecer automaticamente essas informações usando os dados do modelo e do grafo já armazenados no Azure digital gêmeos. Para ler mais sobre como gerenciar, atualizar e recuperar informações do grafo gêmeos, consulte as seguintes referências:

* [*Como gerenciar um teledigital*](./how-to-manage-twin.md)
* [*Como consultar o gráfico de entrelaçamento*](./how-to-query-graph.md)