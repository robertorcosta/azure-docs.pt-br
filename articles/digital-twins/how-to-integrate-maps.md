---
title: Usar o gêmeos digital do Azure para atualizar um mapa interno do Azure Maps
titleSuffix: Azure Digital Twins
description: Veja como criar uma função do Azure que pode usar as notificações do grafo gêmeos e do Azure digital para atualizar as informações mostradas no Azure Maps.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/3/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 8f3e670a4f2a49bcce48be1ba0452a36cbf96df1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392311"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Usar o gêmeos digital do Azure para atualizar um mapa interno do Azure Maps

Este artigo percorre as etapas necessárias para usar os dados do gêmeos digital do Azure para atualizar as informações exibidas em um *mapa interno* usando [mapas do Azure](../azure-maps/about-azure-maps.md). O Azure digital gêmeos armazena um grafo de suas relações de dispositivo IoT e encaminha a telemetria para diferentes pontos de extremidade, tornando-o o serviço perfeito para atualizar sobreposições informativas em mapas.

Este "como" abordará:

1. Configurar sua instância do gêmeos digital do Azure para enviar eventos de atualização de cópia para uma função no [Azure Functions](../azure-functions/functions-overview.md).
2. Criar uma função do Azure para atualizar um estado do recurso de mapas de recursos do Azure Maps.
3. Como armazenar a ID do mapa e a ID do estado do recurso no grafo do gêmeos digital do Azure.

### <a name="prerequisites"></a>Pré-requisitos

* Siga o tutorial do Azure digital gêmeos [: Conecte uma solução de ponta a ponta](./tutorial-end-to-end.md).
    * Você estenderá essa tenda com um ponto de extremidade e rota adicionais. Você também adicionará outra função ao seu aplicativo de funções a partir desse tutorial. 
* Siga o tutorial do Azure Maps [: usar o Azure Maps Creator para criar mapas](../azure-maps/tutorial-creator-indoor-maps.md) em destaque para criar um mapa interno do Azure Maps com um *estado de recurso*.
    * Os [statesets de recursos](../azure-maps/creator-indoor-maps.md#feature-statesets) são coleções de propriedades dinâmicas (Estados) atribuídas a recursos de conjuntos de de, como salas ou equipamentos. No tutorial do Azure Maps acima, o stateset do recurso armazena o status da sala que você exibirá em um mapa.
    * Você precisará de sua *ID de estado* do recurso e da *ID da assinatura*do Azure Maps.

### <a name="topology"></a>Topologia

A imagem abaixo ilustra onde os elementos de integração de mapas internos neste tutorial se enquadram em um cenário maior de ponta a ponta do Azure digital gêmeos.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="Uma exibição dos serviços do Azure em um cenário de ponta a ponta, destacando a parte de integração dos mapas em destaque" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>Criar uma função para atualizar um mapa quando gêmeos Update

Primeiro, você criará uma rota no Azure digital gêmeos para encaminhar todos os eventos de atualização de entrelaçamento para um tópico da grade de eventos. Em seguida, você usará uma função do Azure para ler essas mensagens de atualização e atualizar um estado do recurso no Azure Maps. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Criar uma rota e um filtro para as notificações de atualização do entrelaçamento

As instâncias de gêmeos digitais do Azure podem emitir eventos de atualização de entrelaçar sempre que um estado de entrelaçamento é atualizado. O [tutorial do gêmeos digital do Azure: conectar uma solução de ponta a ponta](./tutorial-end-to-end.md) vinculada acima percorre um cenário em que um termômetro é usado para atualizar um atributo de temperatura anexado a uma sala de salas. Você estará estendendo essa solução assinando as notificações de atualização para gêmeos e usando essas informações para atualizar nossos mapas.

Esse padrão lê-se diretamente da sala, em vez do dispositivo IoT, que nos dá a flexibilidade de alterar a fonte de dados subjacente para temperatura sem a necessidade de atualizar nossa lógica de mapeamento. Por exemplo, você pode adicionar vários termômetros ou definir essa sala para compartilhar um termômetro com outra sala, tudo sem a necessidade de atualizar nossa lógica de mapa.

1. Crie um tópico de grade de eventos, que receberá eventos de nossa instância do gêmeos digital do Azure.
    ```azurecli
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Crie um ponto de extremidade para vincular seu tópico da grade de eventos ao Azure digital gêmeos.
    ```azurecli
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. Crie uma rota no gêmeos digital do Azure para enviar eventos de atualização de entrelaçar para seu ponto de extremidade.
    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "{ "endpointId": "<endpoint-ID>","filter": "type = 'Microsoft.DigitalTwins.Twin.Update'"}"
    ```

## <a name="create-an-azure-function-to-update-maps"></a>Criar uma função do Azure para atualizar mapas

Você vai criar uma função disparada por grade de eventos dentro do nosso aplicativo de funções do [tutorial de ponta a ponta](./tutorial-end-to-end.md). Essa função desempacotará essas notificações e enviará atualizações para um recurso do Azure Maps de estado para atualizar a temperatura de uma sala. 

Consulte o seguinte documento para obter informações de referência: [gatilho de grade de eventos do Azure para Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger).

Substitua o código da função pelo código a seguir. Ele filtrará somente as atualizações no espaço gêmeos, lerá a temperatura atualizada e enviará essas informações para o Azure Maps.

```C#
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Threading.Tasks;
using System.Net.Http;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoMaps
    {   //Read maps credentials from application settings on function startup
        private static string statesetID = Environment.GetEnvironmentVariable("statesetID");
        private static string subscriptionKey = Environment.GetEnvironmentVariable("subscription-key");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("ProcessDTUpdatetoMaps")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
            log.LogInformation("Reading event from twinID:" + eventGridEvent.Subject.ToString() + ": " +
                eventGridEvent.EventType.ToString() + ": " + message["data"]);

            //Parse updates to "space" twins
            if (message["data"]["modelId"].ToString() == "dtmi:contosocom:DigitalTwins:Space;1")
            {   //Set the ID of the room to be updated in our map. 
                //Replace this line with your logic for retrieving featureID. 
                string featureID = "UNIT103";

                //Iterate through the properties that have changed
                foreach (var operation in message["data"]["patch"])
                {
                    if (operation["op"].ToString() == "replace" && operation["path"].ToString() == "/Temperature")
                    {   //Update the maps feature stateset
                        var postcontent = new JObject(new JProperty("States", new JArray(
                            new JObject(new JProperty("keyName", "temperature"),
                                 new JProperty("value", operation["value"].ToString()),
                                 new JProperty("eventTimestamp", DateTime.Now.ToString("s"))))));

                        var response = await httpClient.PostAsync(
                            $"https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetID}&featureID={featureID}&subscription-key={subscriptionKey}",
                            new StringContent(postcontent.ToString()));

                        log.LogInformation(await response.Content.ReadAsStringAsync());
                    }
                }
            }
        }
    }
}
```

Você precisará definir duas variáveis de ambiente em seu aplicativo de funções. Uma é a [chave de assinatura primária do Azure Maps](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)e uma é sua [ID de estado do Azure Maps](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset).

```azurecli-interactive
az functionapp config appsettings set --settings "subscription-key=<your-Azure-Maps-primary-subscription-key> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
az functionapp config appsettings set --settings "statesetID=<your-Azure-Maps-stateset-ID> -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

### <a name="view-live-updates-on-your-map"></a>Exibir atualizações dinâmicas em seu mapa

Para ver a temperatura de atualização dinâmica, siga as etapas abaixo:

1. Comece a enviar dados de IoT simulados executando o projeto **DeviceSimulator** do tutorial do gêmeos digital do Azure [: Conecte uma solução de ponta a ponta](tutorial-end-to-end.md). As instruções para isso estão na seção [*configurar e executar a simulação*](././tutorial-end-to-end.md#configure-and-run-the-simulation) .
2. Use [o módulo de **interno do Azure Maps** ](../azure-maps/how-to-use-indoor-module.md) para renderizar seus mapas em interno criados no Azure Maps Creator.
    1. Copie o HTML do [*exemplo: Use a seção módulo de mapas de interno*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) do tutorial de mapas em interno [: Use o módulo](../azure-maps/how-to-use-indoor-module.md) mapas de cópia para o Azure Maps para um arquivo local.
    1. Substitua *tilesetId* e *STATESETID* no arquivo HTML local por seus valores.
    1. Abra esse arquivo em seu navegador.

Ambos os exemplos enviam a temperatura em um intervalo compatível, portanto, você deve ver a cor da atualização 121 do espaço no mapa a cada 30 segundos.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="Um mapa do Office mostrando a sala 121 colorida laranja":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Armazenar suas informações de mapas no Azure digital gêmeos

Agora que você tem uma solução codificada para atualizar suas informações de mapas, você pode usar o grafo gêmeos do Azure digital para armazenar todas as informações necessárias para atualizar seu mapa interno. Isso incluiria a ID do estadoset, a ID da assinatura do Maps e a ID do recurso de cada mapa e local, respectivamente. 

Uma solução para esse exemplo específico envolveria a atualização de cada espaço de nível superior para ter uma ID de estado e um atributo de ID de assinatura do Maps e atualizar cada sala para ter uma ID de recurso. Você precisaria definir esses valores uma vez ao inicializar o grafo de entrelaçamento e, em seguida, consultar esses valores para cada evento de atualização de entrelaçamento.

Dependendo da configuração de sua topologia, você poderá armazenar esses três atributos em diferentes níveis, correlacionando à granularidade do mapa.

## <a name="next-steps"></a>Próximas etapas

Para ler mais sobre como gerenciar, atualizar e recuperar informações do grafo gêmeos, consulte as seguintes referências:

* [Como: gerenciar gêmeos digitais](./how-to-manage-twin.md)
* [Como consultar o gráfico de entrelaçamento](./how-to-query-graph.md)