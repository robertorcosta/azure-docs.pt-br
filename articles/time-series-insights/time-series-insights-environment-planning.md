---
title: Planeje seu ambiente GA - Azure Time Series Insights | Microsoft Docs
description: Práticas recomendadas para preparar, configurar e implantar seu ambiente GA do Azure Time Series Insights.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 972bb2a804057037deedb448674abafcc175b21f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314803"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planeje o ambiente GA do Azure Time Series Insights

Este artigo descreve como planejar seu ambiente de disponibilidade geral (GA) do Azure Time Series Insights com base na taxa de ingestão esperada e nos requisitos de retenção de dados.

## <a name="video"></a>Vídeo

**Assista a este vídeo para saber mais sobre a retenção de dados no Azure Time Series Insights e como planejar:**<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Práticas recomendadas

Para começar com o Azure Time Series Insights, é melhor se você sabe quantos dados você espera empurrar por minuto e quanto tempo você precisa para armazenar seus dados.  

Para obter mais informações sobre capacidade e retenção para ambos os Insights da Série Do Tempo, leia [os preços do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Para planejar melhor o ambiente de Insights da série do tempo para o sucesso a longo prazo, considere os seguintes atributos:

- [Capacidade de armazenamento](#storage-capacity)
- [Período de retenção de dados](#data-retention)
- [Capacidade de entrada](#ingress-capacity)
- [Formação de seus eventos](#shape-your-events)
- [Garantir que você tenha dados de referência no lugar](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Capacidade de armazenamento

Por padrão, o Time Series Insights retém dados com base na quantidade de armazenamento que você prosente (unidades &#215; quantidade de armazenamento por unidade) e ingress.

## <a name="data-retention"></a>Retenção de dados

Você pode alterar a configuração de **tempo de retenção** de dados no ambiente Azure Time Series Insights. Você pode habilitar até 400 dias de retenção. 

O Azure Time Series Insights tem dois modos:

* Um modo otimiza para os dados mais atualizados. Ele impõe uma política para **expurgar dados antigos** deixando dados recentes disponíveis com a instância. Este modo está ligado, por padrão. 
* O outro otimiza os dados para permanecer abaixo dos limites de retenção configurados. **A pausa** impede que novos dados sejam insanados quando selecionados, pois o **limite de armazenamento excedeu**o comportamento .

Você pode ajustar a retenção e alternar entre os dois modos na página de configuração do ambiente no portal Azure.

> [!IMPORTANT]
> Você pode configurar um máximo de 400 dias de retenção de dados no ambiente GA do Azure Time Series Insights.

### <a name="configure-data-retention"></a>Configurar retenção de dados

1. No [portal do Azure](https://portal.azure.com), selecione o ambiente do Time Series Insights.

1. No painel ambiente **Time Series Insights,** em **Configurações,** selecione **Configuração de armazenamento**.

1. Na caixa **Tempo de retenção de dados (em dias),** digite um valor entre 1 e 400.

   [![Configurar retenção](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Para saber mais sobre como implementar uma política de retenção de dados apropriada, leia [como configurar a retenção](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Capacidade de entrada

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Planejamento ambiental

A segunda área a se concentrar no planejamento do seu ambiente Time Series Insights é a capacidade de ingestão. A capacidade de entrada é uma derivada da alocação por minuto.

De uma perspectiva de estrangulamento, um pacote de dados insonado que tem um tamanho de pacote de 32 KB é tratado como 32 eventos, cada um de 1 KB de tamanho. O tamanho máximo do evento permitido é de 32 KB. Pacotes de dados maiores que 32 KB são truncados.

Você pode aumentar a capacidade de uma SKU S1 ou S2 para até 10 unidades em um único ambiente. Você não pode migrar de um ambiente S1 para um S2. Você não pode migrar de um ambiente S2 para um S1.

Para a capacidade de entrada, primeiro determine o total de entrada que você precisa por mês. Em seguida, determine quais são suas necessidades por minuto. 

Estrangulamento e latência desempenham um papel na capacidade por minuto. Se você tiver um pico na sua ingestão de dados que dure menos de 24 horas, o Time Series Insights pode "recuperar" a uma taxa de ingestão de duas vezes as taxas listadas na tabela anterior.

Por exemplo, se você tem um único S1 SKU, você ingressa dados a uma taxa de 720 eventos por minuto, e a taxa de dados aumenta por menos de uma hora a uma taxa de 1.440 eventos ou menos, não há latência perceptível em seu ambiente. No entanto, se você exceder 1.440 eventos por minuto por mais de uma hora, você provavelmente experimentará latência em dados que são visualizados e disponíveis para consulta em seu ambiente.

Você pode não saber com antecedência quantos dados você espera empurrar. Neste caso, você pode encontrar telemetria de dados para [OZure IoT Hub](../iot-hub/iot-hub-metrics.md) e [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) em sua assinatura do portal Azure. A telemetria pode ajudá-lo a determinar como prover seu ambiente. Use o painel **Métricas** no portal Azure para a respectiva fonte do evento para visualizar sua telemetria. Se você compreender suas métricas de origem de eventos, poderá planejar e provisionar com mais eficiência o ambiente do Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcular os requisitos de entrada

Para calcular seus requisitos de ingestão:

- Verifique se sua capacidade de entrada está acima da sua taxa média por minuto e que seu ambiente é grande o suficiente para lidar com sua entrada antecipada equivalente a duas vezes sua capacidade por menos de uma hora.

- Se ocorrerem picos de ingressantes que duram mais de 1 hora, use a taxa de pico como sua média. Prover um ambiente com capacidade para lidar com a taxa de pico.

### <a name="mitigate-throttling-and-latency"></a>Reduzir a latência e a limitação

Para obter informações sobre como evitar o estrangulamento e a latência, leia [Mitigar a latência e o estrangulamento](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Formatar os eventos

É importante garantir que a maneira como você envia eventos para o Time Series Insights suporte o tamanho do ambiente que você está provisionando. (Por outro lado, você pode mapear o tamanho do ambiente para quantos eventos o Time Series Insights lê e o tamanho de cada evento.) Também é importante pensar sobre os atributos que você pode querer usar para fatiar e filtrar quando você consultar seus dados.

> [!TIP]
> Revise a documentação de modelagem json no [envio de eventos](time-series-insights-send-events.md).

## <a name="ensure-that-you-have-reference-data"></a>Certifique-se de que você tem dados de referência

Um *conjunto de dados* de referência é uma coleção de itens que aumentam os eventos da sua fonte de eventos. O mecanismo de inserção do Time Series Insights une cada evento a partir de sua fonte de evento com a linha de dados correspondente em seu conjunto de dados de referência. O evento aumentado está então disponível para consulta. A unidade é baseada nas colunas **Principal Chave** definidas no conjunto de dados de referência.

> [!NOTE]
> Os dados de referência não são unidos retroativamente. Somente os dados de ingestão atuais e futuros são combinados e unidos ao conjunto de dados de referência depois de configurado e carregado. Se você planeja enviar uma grande quantidade de dados históricos para o Time Series Insights e não enviar ou criar dados de referência no Time Series Insights, você pode ter que refazer seu trabalho (dica: não é divertido).  

Para saber mais sobre como criar, carregar e gerenciar seus dados de referência no Time Series Insights, leia nossa [documentação do conjunto de dados de referência](time-series-insights-add-reference-data-set.md).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximas etapas

- Comece criando [um novo ambiente de Insights da Série Time no portal Azure](time-series-insights-get-started.md).

- Aprenda a [adicionar uma fonte de eventos do Event Hubs](time-series-insights-how-to-add-an-event-source-eventhub.md) ao Time Series Insights.

- Leia sobre como [configurar uma fonte de eventos do IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
