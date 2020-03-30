---
title: Entender o roteamento de mensagens do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – como usar o roteamento de mensagens para enviar mensagens de dispositivo para nuvem. Inclui informações de como enviar dados telemétricos e dados que não são de telemetria.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d10744f2536cdf89115cdccd0bea6f1e5155774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370450"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Use o roteamento de mensagens do IoT Hub para enviar mensagens de dispositivo para nuvem para diferentes pontos finais

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Roteamento de mensagens permite que você envie mensagens de dispositivos para serviços de nuvem de maneira automatizada, escalonável e confiável. O roteamento de mensagens pode ser usado para: 

* **Enviar mensagens de telemetria e eventos do dispositivo**, ou seja, eventos de ciclo de vida do dispositivo e eventos de alteração de dispositivo gêmeo para o ponto de extremidade interno e para os pontos de extremidade personalizados. Saiba mais sobre [os pontos finais de roteamento](#routing-endpoints).

* **Filtrar dados antes de roteá-los para vários pontos de extremidade** aplicando consultas avançadas. O roteamento de mensagens permite consultar as propriedades da mensagem e o corpo da mensagem, bem como as marcas do dispositivo gêmeo e as propriedades do dispositivo gêmeo. Saiba mais sobre como usar [consultas no roteamento de mensagens](iot-hub-devguide-routing-query-syntax.md).

O Hub IoT precisa de acesso de gravação a esses pontos de extremidade para que o direcionamento de mensagens funcione. Se você configurar os pontos de extremidade por meio do Portal do Azure, as permissões necessárias serão adicionadas para você. Certifique-se de configurar os serviços para dar suporte à taxa de transferência esperada. Por exemplo, se você estiver usando o Event Hubs como um ponto final personalizado, você deve configurar as **unidades de throughput** para esse hub de eventos para que ele possa lidar com a ingestão de eventos que você planeja enviar via roteamento de mensagens do IoT Hub. Da mesma forma, ao usar uma fila de ônibus de serviço como ponto final, você deve configurar o **tamanho máximo** para garantir que a fila possa conter todos os dados instidados, até que sejam egressos pelos consumidores. Quando você configurar pela primeira vez sua solução IoT, talvez seja necessário monitorar seus pontos de extremidade adicionais ao configurar e fazer eventuais ajustes necessários para a carga real.

O Hub IoT define um [formato comum](iot-hub-devguide-messages-construct.md) para todas as mensagens de dispositivo para nuvem que permite a interoperabilidade entre protocolos. Se uma mensagem corresponder a várias rotas e todas apontarem para o mesmo ponto de extremidade, o Hub IoT entregará mensagens a esse ponto apenas uma vez. Portanto, você não precisa configurar a eliminação de duplicação nem no tópico nem na fila do Barramento de Serviço. Em filas particionados, a afinidade de partição garante a ordenação das mensagens. Use este tutorial para aprender a [configurar o roteamento de mensagem](tutorial-routing.md).

## <a name="routing-endpoints"></a>Pontos de extremidade de roteamento

Um Hub IoT tem um padrão de ponto de extremidade interno (**mensagens/eventos**) que é compatível com Hubs de Eventos. Você pode criar [pontos de extremidade personalizados](iot-hub-devguide-endpoints.md#custom-endpoints) para encaminhar mensagens vinculando outros serviços em sua assinatura ao Hub IoT. 

Cada mensagem é encaminhada para todos os pontos finais cujas consultas de roteamento correspondem. Em outras palavras, uma mensagem pode ser encaminhada para vários pontos finais.

No momento, o Hub IoT dá suporte aos seguintes serviços como pontos de extremidade personalizados:

### <a name="built-in-endpoint"></a>Ponto de extremidade interno

Você pode usar [SDKs e integração padrão dos Hubs de Eventos](iot-hub-devguide-messages-read-builtin.md) para receber mensagens de dispositivo para nuvem do ponto de extremidade interno (**mensagens/eventos**). Uma vez que uma rota é criada, os dados param de fluir para o ponto final incorporado, a menos que uma Rota seja criada para esse ponto final.

### <a name="azure-storage"></a>Armazenamento do Azure

Existem dois serviços de armazenamento que o IoT Hub pode direcionar mensagens para contas [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) e [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2). As contas do Azure Data Lake Storage são contas de armazenamento [hierárquicas](../storage/blobs/data-lake-storage-namespace.md)habilitadas para namespace construídas em cima do armazenamento blob. Ambos usam bolhas para o armazenamento.

O IoT Hub suporta a gravação de dados para o Azure Storage no formato [Apache Avro,](https://avro.apache.org/) bem como no formato JSON. O padrão é AVRO. O formato de codificação só pode ser definido quando o ponto final do armazenamento blob estiver configurado. O formato não pode ser editado para um ponto final existente. Ao usar a codificação JSON, você deve definir o conteúdoType para **aplicativo/json** e contentEncoding para **UTF-8** nas propriedades do [sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagens . Ambos os valores são insensíveis a casos. Se a codificação de conteúdo não estiver definida, o IoT Hub gravará as mensagens no formato codificado base 64. Você pode selecionar o formato de codificação usando a API IoT Hub Create ou Update REST, especificamente o [RoutingStorageContainerProperties,](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties)o portal Azure, [o Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)ou o [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). O diagrama a seguir mostra como selecionar o formato de codificação no portal Azure.

![Codificação do ponto final do armazenamento Blob](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

O IoT Hub emlote mensagens e grava dados para armazenamento sempre que o lote atinge um determinado tamanho ou um certo tempo. O Hub IoT segue a seguinte convenção de nomenclatura de arquivo padrão: 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Você pode usar qualquer convenção de nomenclatura de arquivo, no entanto, é necessário usar todos os tokens listados. O Hub IoT gravará em um blob vazio se não houver nenhum dado para gravação.

Recomendamos listar os blobs ou arquivos e, em seguida, iterar sobre eles, para garantir que todas as bolhas ou arquivos sejam lidos sem fazer quaisquer suposições de partição. O intervalo de partição potencialmente pode ser alterado durante um [failover iniciado pela Microsoft](iot-hub-ha-dr.md#microsoft-initiated-failover) ou [failover manual](iot-hub-ha-dr.md#manual-failover) do Hub IoT. Você pode usar a [API List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) para enumerar a lista de blobs ou [API List ADLS Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) para a lista de arquivos. Por favor, veja a seguinte amostra como orientação.

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

> [!NOTE]
> Se sua conta de armazenamento tiver configurações de firewall que restringem a conectividade do IoT Hub, considere usar [a exceção de primeira parte confiável da Microsoft](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) (disponível em regiões selecionadas para hubs De IoT com identidade de serviço gerenciada).

Para criar uma conta de armazenamento compatível com o Azure Data Lake Gen2, crie uma nova conta de armazenamento V2 e selecione *ativado* no campo *namespace hierárquico* na guia **Avançado,** conforme mostrado na imagem a seguir:

![Selecione o armazenamento Azure Date Lake Gen2](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>Filas do Barramento de Serviço e Tópicos do Barramento de Serviço

As filas e os tópicos do Barramento de Serviço utilizados como pontos de extremidade do Hub IoT não devem ter **Sessões** nem **Detecção Duplicada** habilitadas. Se qualquer uma dessas opções estiver habilitada, o ponto de extremidade aparecerá como **Inacessível** no Portal do Azure.

> [!NOTE]
> Se o recurso de barramento de serviço tiver configurações de firewall que restringem a conectividade do IoT Hub, considere usar [a exceção de primeira parte confiável da Microsoft](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing) (disponível em regiões selecionadas para hubs de IoT com identidade de serviço gerenciada).


### <a name="event-hubs"></a>Hubs de Eventos

Além do ponto de extremidade compatível com os Hubs de Eventos internos, você também pode encaminhar dados para pontos de extremidade personalizados do tipo Hubs de Eventos. 

> [!NOTE]
> Se o recurso de hubs de eventos tiver configurações de firewall que restringem a conectividade do IoT Hub, considere usar [a exceção de primeira parte confiável da Microsoft](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) (disponível em regiões selecionadas para hubs de IoT com identidade de serviço gerenciada).


## <a name="reading-data-that-has-been-routed"></a>Lendo dados que foram encaminhados

Configure uma rota seguindo este [tutorial](tutorial-routing.md).

Use os tutoriais a seguir para saber como ler a mensagem de um ponto de extremidade.

* Lendo do [ponto de extremidade interno](quickstart-send-telemetry-node.md)

* Lendo do [Armazenamento de Blobs](../storage/blobs/storage-blob-event-quickstart.md)

* Lendo dos [Hubs de Eventos](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Lendo das [Filas do Barramento de Serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Ler dos [Tópicos do Barramento de Serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)


## <a name="fallback-route"></a>Rota de fallback

A rota de fallback envia todas as mensagens que não atendem às condições de consulta em nenhuma das rotas existentes para os Hubs de Eventos internos em (**mensagens/eventos**), compatíveis com [Hubs de Eventos](/azure/event-hubs/). Se o roteamento de mensagens estiver habilitado, você poderá habilitar a funcionalidade de rota de fallback. Uma vez que uma rota é criada, os dados param de fluir para o ponto final incorporado, a menos que uma rota seja criada para esse ponto final. Se não houver nenhuma rota para o ponto de extremidade interno e houver uma rota de fallback habilitada, somente as mensagens que não corresponderem a nenhuma condição de consulta nas rotas serão enviadas ao ponto de extremidade interno. Além disso, se todas as rotas existentes forem excluídas, a rota de fallback precisará ser habilitada para receber todos os dados no ponto de extremidade interno.

Você pode ativar/desativar a rota de recuo na lâmina de roteamento de mensagens >portal Azure. Você também pode usar o Azure Resource Manager para [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) para usar um ponto de extremidade personalizado para a rota de fallback.

## <a name="non-telemetry-events"></a>Eventos que não são de telemetria

Além da telemetria do dispositivo, o roteamento de mensagens também permite o envio de eventos de dupla mudança de dispositivo, eventos do ciclo de vida do dispositivo e eventos de dupla mudança digital (em visualização pública). Por exemplo, se uma rota é criada com a fonte de dados definida como **eventos de alteração de dispositivo gêmeo**, o Hub IoT envia mensagens para o ponto de extremidade que contém a alteração no dispositivo gêmeo. Da mesma forma, se uma rota for criada com a fonte de dados definida para **eventos do ciclo de vida do dispositivo,** o IoT Hub envia uma mensagem indicando se o dispositivo foi excluído ou criado. Finalmente, como parte da [pré-visualização pública do IoT Plug and Play,](../iot-pnp/overview-iot-plug-and-play.md)um desenvolvedor pode criar rotas com fonte de dados definida para **eventos de mudança dupla digital** e o IoT Hub envia mensagens sempre que uma [propriedade](../iot-pnp/iot-plug-and-play-glossary.md) dupla digital é definida ou alterada, um [gêmeo digital](../iot-pnp/iot-plug-and-play-glossary.md) é substituído ou quando um evento de alteração acontece para o dispositivo gêmeo subjacente.

[O IoT Hub também se integra ao Azure Event Grid](iot-hub-event-grid.md) para publicar eventos de dispositivos para suportar integrações em tempo real e automação de fluxos de trabalho com base nesses eventos. Confira as principais [diferenças entre o roteamento de mensagens e Grade de Eventos](iot-hub-event-grid-routing-comparison.md) para saber o que funciona melhor para seu cenário.

## <a name="testing-routes"></a>Testando rotas

Ao criar uma rota ou editar uma rota existente, você deve testar a consulta de rota com uma mensagem de exemplo. Você pode testar rotas individuais ou todas as rotas de uma vez. Não será roteada nenhuma mensagem aos pontos de extremidade durante o teste. O portal Azure, o Azure Resource Manager, o Azure PowerShell e o Azure CLI podem ser usados para testes. Os resultados ajudam a identificar se a mensagem de amostra correspondia à consulta, a mensagem não correspondia à consulta ou o teste não podia ser executado porque a mensagem de amostra ou a sintaxe de consulta estão incorretas. Para saber mais, confira [Testar Rota](/rest/api/iothub/iothubresource/testroute) e [Testar todas as rotas](/rest/api/iothub/iothubresource/testallroutes).

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Encomendar garantias com pelo menos uma vez de entrega

As garantias de roteamento de mensagens do IoT Hub são ordenadas e pelo menos uma vez a entrega de mensagens aos pontos finais. Isso significa que pode haver mensagens duplicadas e uma série de mensagens podem ser retransmitidas honrando o pedido de mensagem original. Por exemplo, se a ordem de mensagem original for [1,2,3,4], você poderá receber uma seqüência de mensagens como [1,2,1,2,3,1,2,3,4]. A garantia de encomenda é que se você receber mensagem [1], ela sempre será seguida por [2,3,4].

Para o manuseio de duplicatas de mensagens, recomendamos carimbar um identificador exclusivo nas propriedades do aplicativo da mensagem no ponto de origem, que geralmente é um dispositivo ou um módulo. O serviço que consome as mensagens pode lidar com mensagens duplicadas usando este identificador.

## <a name="latency"></a>Latency

Ao rotear mensagens de telemetria do dispositivo para nuvem usando pontos de extremidade internos, haverá um pequeno aumento na latência de ponta a ponta após a criação da primeira rota.

Na maioria dos casos, o aumento médio da latência é inferior a 500 ms. Você pode monitorar a latência usando a métrica do Hub IoT **Roteamento: latência de mensagem para mensagens/eventos** ou **d2c.endpoints.latency.builtIn.events**. Criar ou excluir qualquer rota após a primeira não afeta a latência de ponta a ponta.

## <a name="monitoring-and-troubleshooting"></a>Monitoramento e solução de problemas

O IoT Hub fornece várias métricas relacionadas ao roteamento e pontos finais para lhe dar uma visão geral da saúde do seu hub e das mensagens enviadas. Você pode combinar informações de várias métricas para identificar a causa raiz de problemas. Por exemplo, use **roteamento métrico: mensagens de telemetria caíram** ou **d2c.telemettry.egress.caiu** para identificar o número de mensagens que foram descartadas quando não correspondiam a consultas em qualquer uma das rotas e a rota de recuo foi desativada. [Métricas do Hub IoT](iot-hub-metrics.md) lista todas as métricas que são habilitadas por padrão para o Hub IoT.

Você pode usar a API REST [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) para obter o estado de [saúde](iot-hub-devguide-endpoints.md#custom-endpoints) dos pontos finais. Recomendamos o uso das métricas do [IoT Hub relacionadas](iot-hub-metrics.md) à latência de mensagens de roteamento para identificar e depurar erros quando a saúde do ponto final estiver morta ou insalubre. Por exemplo, para hubs de eventos do tipo ponto final, você pode monitorar **d2c.endpoints.latency.eventHubs**. O status de um ponto final insalubre será atualizado para saudável quando o IoT Hub estabelecer um estado de saúde eventualmente consistente.

Usando os registros de diagnóstico de **rotas** nas [configurações de diagnóstico](../iot-hub/iot-hub-monitor-resource-health.md)do Azure Monitor, você pode rastrear erros que ocorrem durante a avaliação de uma consulta de roteamento e saúde de ponto final, conforme percebido pelo IoT Hub, por exemplo, quando um ponto final está morto. Esses registros de diagnóstico podem ser enviados para logs do Monitor do Azure, Hubs de Eventos ou Armazenamento Azure para processamento personalizado.

## <a name="next-steps"></a>Próximas etapas

* Para saber como criar rotas de mensagens, consulte [Processo de mensagens dispositivo para nuvem de Hub IoT usando as rotas](tutorial-routing.md).

* [Como enviar mensagens do dispositivo para nuvem](quickstart-send-telemetry-node.md)

* Para saber mais sobre os SDKs que você pode usar para enviar mensagens de dispositivo para a nuvem, confira [SDKs do Azure IoT](iot-hub-devguide-sdks.md).
