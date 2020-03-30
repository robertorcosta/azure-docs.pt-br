---
title: Configuração de políticas de pedidos de eventos para Azure Stream Analytics
description: Este artigo descreve como configurar até mesmo configurações de pedidos no Stream Analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c0a108565a6a0f62c6252113f984e8b10967c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461199"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Configuração de políticas de pedidos de eventos para Azure Stream Analytics

Este artigo descreve como configurar e usar políticas de eventos de chegada tardia e fora de ordem no Azure Stream Analytics. Essas políticas são aplicadas somente quando você usa a cláusula [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) em sua consulta.

## <a name="event-time-and-arrival-time"></a>Hora do evento e hora de chegada

Seu trabalho no Stream Analytics pode processar eventos com base na hora do *evento* ou *na hora de chegada.* **Tempo de evento/aplicação** é o carimbo de tempo presente na carga útil do evento (quando o evento foi gerado). **A hora de chegada** é o carimbo de data e hora quando o evento foi recebido na fonte de entrada (Event Hubs/IoT Hub/Blob storage). 

Por padrão, o Stream Analytics processa eventos por *tempo de chegada,* mas você pode optar por processar eventos por *tempo de evento* usando cláusula [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) em sua consulta. As políticas de chegada tardia e fora de ordem são aplicáveis apenas se você processar eventos por hora do evento. Ao definir essas configurações, considere os requisitos de latência e exatidão para seu cenário. 

## <a name="what-is-late-arrival-policy"></a>O que é a política de entrada tardia?

Às vezes os eventos chegam atrasados por várias razões. Por exemplo, um evento que chega 40 segundos atrasado terá tempo de evento = 00:10:00 e hora de chegada = 00:10:40. Se você definir a política de chegada tardia para 15 segundos, qualquer evento que chegue depois de 15 segundos será descartado (não processado pelo Stream Analytics) ou terá seu tempo de evento ajustado. No exemplo acima, como o evento chegou 40 segundos atrasado (mais do que o definido pela política), seu tempo de evento será ajustado para o máximo da política de chegada tardia 00:10:25 (tempo de chegada - valor da política de chegada tardia). A política padrão de chegada tardia é de 5 segundos.

## <a name="what-is-out-of-order-policy"></a>O que é política fora de ordem? 

O evento também pode sair fora de ordem. Depois que o tempo do evento é ajustado com base na política de chegada tardia, você também pode optar por soltar ou ajustar automaticamente eventos que estão fora de ordem. Se você definir esta diretiva como 8 segundos, todos os eventos que chegam fora de ordem, mas dentro da janela de 8 segundos são reordenados pelo tempo do evento. Os eventos que chegarem mais tarde serão descartados ou ajustados ao valor máximo da política fora de ordem. A política padrão de saque é de 0 segundos. 

## <a name="adjust-or-drop-events"></a>Ajustar ou soltar eventos

Se os eventos chegarem atrasados ou fora de ordem com base nas políticas configuradas, você poderá descartar tais eventos (não processados pelo Stream Analytics) ou ter seu tempo de evento ajustado.

Vejamos um exemplo dessas políticas em ação.
<br> **Política de chegada tardia:** 15 segundos
<br> **Política fora de ordem:** 8 segundos

| Evento Nº. | Hora do Evento | Horário de chegada | System.Timestamp | Explicação |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | O evento chegou tarde e fora do nível de tolerância. Assim, o tempo do evento é ajustado para a tolerância máxima de chegada tardia.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | O evento chegou tarde, mas dentro do nível de tolerância. Assim, o tempo do evento não se ajusta.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | O evento chegou a tempo. Não é necessário nenhum ajuste.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | O evento chegou fora de ordem, mas dentro da tolerância de 8 segundos. Assim, o tempo do evento não se ajusta. Para fins de análise, este evento será considerado como o evento anterior número 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | O evento chegou fora de ordem e tolerância externa de 8 segundos. Assim, o tempo do evento é ajustado ao máximo de tolerância fora de ordem. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Essas configurações podem atrasar a saída do meu trabalho? 

Sim. Por padrão, a diretiva desordenada é definida como zero (00 minutos e 00 segundos). Se você alterar o padrão, a primeira saída do seu trabalho será adiada por este valor (ou maior). 

Se uma das partições de suas entradas não receber eventos, você deve esperar que sua saída seja adiada pelo valor da política de chegada tardia. Para saber o porquê, leia a seção de erro InputPartition abaixo. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Vejo mensagens do LateInputEvents no meu registro de atividades

Essas mensagens são mostradas para informar que os eventos chegaram atrasados e são descartados ou ajustados de acordo com sua configuração. Você pode ignorar essas mensagens se tiver configurado a política de chegada tardia apropriadamente. 

O exemplo desta mensagem é: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Eu vejo InputPartitionNotProgressing no meu registro de atividades

Sua fonte de entrada (Event Hub/IoT Hub) provavelmente tem várias partições. O Azure Stream Analytics produz saída para carimbo de tempo t1 somente depois de todas as partições combinadas serem pelo menos no tempo t1. Por exemplo, suponha que a consulta faça a leitura de uma partição do hub de eventos que tem duas partições. Uma das partições, P1, tem eventos até o tempo de t1. A outra partição, P2, tem eventos até o tempo t1 + x. Em seguida, a saída é produzida até o tempo t1. Mas, se houver uma cláusula Partition by PartitionId explícita, ambas as partições progredirão de maneira independente. 

Quando várias partições do mesmo fluxo de entrada são combinadas, a tolerância de chegada tardia é o tempo máximo que cada partição espera por novos dados. Se houver uma partição no seu Event Hub ou se o IoT Hub não receber entradas, a linha do tempo para essa partição não progredirá até atingir o limite de tolerância de chegada tardia. Isso atrasa sua saída pelo limite de tolerância de chegada tardia. Nesses casos, você pode ver a seguinte mensagem:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Esta mensagem para informá-lo de que pelo menos uma partição em sua entrada está vazia e atrasará sua saída pelo limite de chegada tardia. Para superar isso, recomenda-se que você também:  
1. Certifique-se de que todas as partições do seu Hub de Eventos/IoT Hub recebam entrada. 
2. Use partição por cláusula PartitionID em sua consulta. 

## <a name="next-steps"></a>Próximas etapas
* [Considerações sobre o uso do tempo](stream-analytics-time-handling.md)
* [Métricas disponíveis no Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
