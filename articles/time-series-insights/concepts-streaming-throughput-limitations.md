---
title: Limitações de taxa de transferência de ingestão de streaming-Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre limites de taxa de transferência de entrada em Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: baa132589f5242ab480c9caa55ca793e0652ad6e
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135002"
---
# <a name="streaming-ingestion-throughput-limits"></a>Limites de taxa de transferência de ingestão de streaming

Azure Time Series Insights as limitações de entrada de dados de streaming estão descritas abaixo.

> [!TIP]
> Leia [Planeje seu ambiente de Versão Prévia](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) para obter uma lista abrangente de todos os limites de Versão Prévia.

## <a name="per-environment-limitations"></a>Limitações por ambiente

Em geral, as taxas de entrada são exibidas como o fator do número de dispositivos que estão em sua organização, a frequência de emissão de eventos e o tamanho de cada evento:

*  **Número de dispositivos** × **Frequência de emissão de eventos** × **Tamanho de cada evento**.

Por padrão, Time Series Insights pode ingerir dados de entrada a uma taxa de **até 1 megabyte por segundo (Mbps) por ambiente de time Series insights**. Há limitações adicionais [por partição de hub](concepts-streaming-throughput-limitations.md#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * O suporte de ambiente para velocidades de ingestão de até 16 MBps pode ser fornecido por solicitação.
> * Entre em contato conosco se precisar de uma taxa de transferência maior enviando um tíquete de suporte pelo portal do Azure.
 
* **Exemplo 1:**

    O envio da Contoso tem 100.000 dispositivos que emitem um evento três vezes por minuto. O tamanho de um evento é de 200 bytes. Eles estão usando um Hub IoT com quatro partições como a origem do evento do Time Series Insights.

    * A taxa de ingestão para seu ambiente do Time Series Insights seria: **100.000 dispositivos * 200 bytes/evento * (3/60 evento/s) = 1 MBps**.
    * A taxa de ingestão por partição seria de 0,25 MBps.
    * A taxa de ingestão da Contoso Shipping estaria dentro da limitação de escala.

* **Exemplo 2:**

    A Análise de Frota da Contoso tem 60.000 dispositivos que emitem um evento a cada segundo. Eles estão usando um Hub de Eventos com uma contagem de 4 partições como a origem do evento do Time Series Insights. O tamanho de um evento é de 200 bytes.

    * A taxa de ingestão do ambiente seria: **60.000 dispositivos * 200 bytes/evento * 1 evento/s = 12 MBps**.
    * A taxa por partição seria de 3 MBps.
    * A taxa de ingestão da Análise de Frota da Contoso está acima dos limites do ambiente e da partição. Eles podem enviar uma solicitação para o Time Series Insights pelo portal do Azure para aumentar a taxa de ingestão no ambiente deles e criar um Hub de Eventos com mais partições dentro dos limites da Versão Prévia.

## <a name="hub-partitions-and-per-partition-limits"></a>Partições de Hub e limites por partição

Ao planejar seu ambiente do Time Series Insights, é importante considerar a configuração das origens de eventos com as quais você se conectará ao Time Series Insights. O Hub IoT do Azure e os Hubs de Eventos utilizam partições para habilitar a escala horizontal para processamento de eventos. 

Uma *partição* é uma sequência ordenada de eventos mantidos em um hub. A contagem de partições é definida durante a fase de criação do hub e não pode ser alterada.

Para consultar as melhores práticas de particionamento de Hubs de Eventos, analise [Quantas partições eu preciso?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> A maioria dos Hubs IoT usados com o Azure Time Series Insights só precisa de quatro partições.

Se você estiver criando um novo hub para seu ambiente de Time Series Insights ou usando um existente, será necessário calcular a taxa de ingestão por partição para determinar se ele está dentro dos limites. 

Atualmente, a Versão Prévia do Azure Time Series Insights tem um **limite geral por partição de 0,5 MBps**.

### <a name="iot-hub-specific-considerations"></a>Considerações específicas do Hub IoT

Quando um dispositivo é criado no Hub IoT, ele é atribuído permanentemente a uma partição. Ao fazer isso, o Hub IoT é capaz de garantir a ordenação de eventos (já que a atribuição nunca é alterada).

Uma atribuição de partição fixa também afeta as instâncias do Time Series Insights que estão ingerindo dados enviados do Hub IoT downstream. Quando as mensagens de vários dispositivos são encaminhadas para o Hub usando a mesma ID de dispositivo de gateway, elas podem chegar na mesma partição ao mesmo tempo, potencialmente excedendo os limites de escala por partição.

**Impacto**:

* Se uma única partição apresentar uma taxa sustentada de ingestão sobre o limite da Versão Prévia, é possível que o Time Series Insights não sincronize toda a telemetria do dispositivo antes que o período de retenção de dados do Hub IoT seja excedido. Como resultado, os dados enviados podem ser perdidos se os limites de ingestão forem excedidos de modo consistente.

Para atenuar essa situação, recomendamos as seguintes melhores práticas:

* Calcule suas taxas de ingestão por ambiente e por partição antes de implantar sua solução.
* Verifique se os dispositivos do Hub IoT têm balanceamento de carga para a extensão mais distante possível.

> [!IMPORTANT]
> Para ambientes que usam o Hub IoT como uma origem de evento, calcule a taxa de ingestão usando o número de dispositivos de Hub em uso para ter certeza de que a taxa está abaixo da limitação de 0,5 MBps por partição.
>
> * Mesmo que vários eventos cheguem simultaneamente, o limite da Versão Prévia não será excedido.

  ![Diagrama de partição do Hub IoT](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Consulte os seguintes recursos para saber mais sobre como otimizar a taxa de transferência e as partições do hub:

* [Escala do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Escala do Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partições do Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

## <a name="next-steps"></a>Próximas etapas

* Leia sobre o [armazenamento](concepts-storage.md) de dados