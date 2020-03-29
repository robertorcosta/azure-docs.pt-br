---
title: Como monitorar e reduzir o estrangulamento - Azure Time Series Insights | Microsoft Docs
description: Aprenda a monitorar, diagnosticar e mitigar problemas de desempenho que causam latência e estrangulamento no Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 245a0b18187ff1c1b226e94b03374f2c071e51c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314820"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Monitorar e melhorar a limitação para reduzir a latência no Azure Time Series Insights

Quando a quantidade de dados de entrada excede a configuração do ambiente, você pode enfrentar latência ou limitação no Azure Time Series Insights.

É possível evitar a latência e a limitação configurando corretamente o ambiente para a quantidade de dados que você deseja analisar.

Provavelmente, você enfrentará latência e limitação quando:

- Adicionar uma origem de evento que contém os dados antigos que podem exceder a taxa de entrada alocada (o Time Series Insights precisará ser atualizado).
- Adicionar mais origens de evento em um ambiente, resultando em um pico de eventos adicionais (que poderá exceder a capacidade do ambiente).
- Enviar por push grandes quantidades de eventos históricos para uma origem de evento, resultando em um retardo (o Time Series Insights precisará ser atualizado).
- Unir os dados de referência com a telemetria, resultando em um tamanho de evento maior. De uma perspectiva de limitação, um pacote de dados recebido com um tamanho de pacote igual a 32 KB é tratado como 32 eventos, cada um com um tamanho de 1 KB. O tamanho máximo permitido do evento é de 32 KB; pacotes de dados maiores que 32 KB são truncados.

## <a name="video"></a>Vídeo

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Saiba mais sobre o comportamento de inserção de dados do Time Series Insights e como planejar.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Monitorar a latência e a limitação com alertas

Os alertas podem ajudá-lo a diagnosticar e mitigar problemas de latência que ocorrem em seu ambiente.

1. No portal do Azure, selecione o ambiente do Time Series Insights. Em seguida, **selecione Alertas**.

   [![Adicione um alerta ao seu ambiente de Insights da série do tempo](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. Selecione **+ Nova regra de alerta**. Em seguida, será exibido o painel **De criar regras.** Selecione **Adicionar** em **CONDITION**.

   [![Adicionar painel de alerta](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. Em seguida, configure as condições exatas para a lógica do sinal.

   [![Configurar sinal lógico](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   A partir daí, você pode configurar alertas usando algumas das seguintes condições:

   |Métrica  |Descrição  |
   |---------|---------|
   |**Bytes de entrada recebidos**     | Contagem de bytes brutos lidos de origens de evento. A contagem bruta geralmente inclui o nome e o valor da propriedade.  |  
   |**Mensagens de entrada inválidas recebidas**     | Contagem de mensagens inválidas lidas de todas as origens de evento dos Hubs de Eventos do Azure ou do Hub IoT do Azure.      |
   |**Mensagens de entrada recebidas**   | Contagem de mensagens lidas de todas as origens de evento dos Hubs de Eventos ou Hubs IoT.        |
   |**Bytes de entrada armazenados**     | Tamanho total de eventos armazenados e disponíveis para consulta. O tamanho é calculado apenas no valor da propriedade.        |
   |**Eventos de entrada armazenados**     |   Contagem de eventos nivelados armazenados e disponíveis para consulta.      |
   |**Tempo de atraso das mensagens de entrada recebidas**    |  Diferença em segundos entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada.      |
   |**Atraso na contagem das mensagens de entrada recebidas**    |  Diferença entre o número de sequência da última mensagem enfileirada na partição da origem do evento e número de sequência da mensagem que está sendo processada na entrada.      |

   Selecione **Feito**.

1. Depois de configurar a lógica de sinal desejada, revise visualmente a regra de alerta escolhida.

   [![Exibição de latência e gráficos](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Gestão de estrangulamento e ingestão

* Se você estiver sendo estrangulado, um valor para o *Defasagem de Tempo de Mensagem Recebida da Ingress será* registrado informando quantos segundos atrás do seu ambiente TIme Series Insights estão a partir do momento em que a mensagem atinge a fonte do evento (excluindo o tempo de indexação do appx. 30-60 segundos).  

  *Retardo de contagem de mensagens recebidas de entrada* também deve ter um valor, permitindo que você determine a quantas mensagens atrás você está.  A maneira mais fácil de alcançar é aumentar a capacidade do seu ambiente para um tamanho que permitirá superar a diferença.  

  Por exemplo, se o seu ambiente S1 está demonstrando atraso de 5.000.000 mensagens, você pode aumentar o tamanho do seu ambiente para seis unidades por cerca de um dia para ficar preso.  Você pode aumentar ainda mais para alcançar mais rapidamente. O período de atualização é uma ocorrência comum ao provisionar inicialmente um ambiente, especialmente ao se conectar a uma origem de evento que já tenha eventos nela ou quando você carrega em massa grandes quantidades de dados históricos.

* Outra técnica é definir um alerta **Eventos de Entrada Armazenados** >= um limite um pouco abaixo da capacidade total do ambiente por um período de 2 horas.  Esse alerta pode ajudá-lo a entender se você está constantemente na capacidade, o que indica uma alta probabilidade de latência. 

  Por exemplo, se você tiver três unidades S1 provisionadas (ou 2.100 eventos por capacidade de entrada por minuto), poderá definir um alerta **Eventos de Entrada Armazenados** para >= 1.900 eventos por 2 horas. Caso você esteja excedendo esse limite constantemente e, portanto, disparando o alerta, provavelmente, está com um provisionamento insuficiente.  

* Se você suspeitar que está sendo estrangulado, você pode comparar suas **Mensagens Recebidas da Ingress** com as mensagens de saída da sua fonte de evento.  Se a entrada no Hub de Eventos for maior que as **Mensagens de Entrada Recebidas**, provavelmente, o Time Series Insights está sendo limitado.

## <a name="improving-performance"></a>Melhorando o desempenho

Para reduzir a limitação ou a latência enfrentada, a melhor maneira de corrigir isso é aumentar a capacidade do ambiente.

É possível evitar a latência e a limitação configurando corretamente o ambiente para a quantidade de dados que você deseja analisar. Para obter mais informações sobre como adicionar capacidade ao seu ambiente, leia [Dimensionar seu ambiente](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Próximas etapas

- Leia sobre [Diagnosticar e resolver problemas em seu ambiente Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).

- Aprenda [a dimensionar seu ambiente de Insights de séries tempo .](time-series-insights-how-to-scale-your-environment.md)