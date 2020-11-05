---
title: Configurar uma porta de sinal para gravação de vídeo baseada em evento-Azure
description: Este artigo fornece orientação sobre como configurar uma entrada de sinal em um grafo de mídia.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: 4204a43915f9c79cae7dfe82b37e741fee89fb4a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380108"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Configurar uma porta de sinal para gravação de vídeo baseada em evento

Em um grafo de mídia, um [nó de processador de porta de sinal](media-graph-concept.md#signal-gate-processor) permite que você encaminhe a mídia de um nó para outro, quando a porta é disparada por um evento. Quando disparado, o portão é aberto e permite o fluxo de mídia por uma duração especificada. Na ausência de eventos para disparar a porta, a porta será fechada e a mídia deixará de fluir. O processador da porta do sinal é aplicável à gravação de vídeo baseada em evento.
Neste artigo, você aprenderá os detalhes sobre como configurar um processador de porta de sinal.

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida
-   [Grafo de mídia](media-graph-concept.md)
-   [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)


## <a name="problem"></a>Problema
O usuário pode querer iniciar a gravação de um momento específico antes ou depois que o portão foi disparado por um evento. O usuário conhece a latência aceitável em seu sistema, portanto, o usuário deseja especificar a latência do processador da porta do sinal. O usuário deseja especificar o mais curto e mais longo que a duração de sua gravação pode ser, independentemente de quantos novos eventos são recebidos.
 
### <a name="use-case-scenario"></a>Cenário do caso de uso
Suponha que você deseja gravar o vídeo sempre que a porta da frente do seu prédio for aberta. Você quer os **X** segundos antes da abertura da porta incluída na gravação. Você quer a gravação para o último, no mínimo, **Y** segundos, se a porta não for aberta novamente. Você deseja que a gravação seja a última no máximo **Z** segundos, se a porta for aberta repetidamente. Você sabe que o sensor de porta tem uma latência de **K** segundos e deseja diminuir a chance de os eventos serem desconsiderados ("entradas tardias"), portanto, você deseja permitir pelo menos **K** segundos para que os eventos cheguem.


## <a name="solution"></a>Solução

**_Modificando parâmetros do processador da porta do sinal_* _

Um processador de porta de sinal é configurado por 4 parâmetros:
- _ *janela de avaliação de ativação**
- **deslocamento do sinal de ativação**
- **janela de ativação mínima**
- **janela de ativação máxima**. 

Quando o processador da porta do sinal for disparado, ele permanecerá aberto durante o tempo mínimo de ativação. O evento de ativação começa no carimbo de data/hora do evento mais antigo, além do deslocamento do sinal de ativação. Se o processador da porta do sinal for disparado novamente, enquanto estiver aberto, o temporizador será redefinido e a porta permanecerá aberta por pelo menos o tempo mínimo de ativação. O processador da porta do sinal nunca permanecerá aberto por mais tempo do que o limite máximo de ativação. Um evento **(evento 1)** que ocorre antes de outro evento **(evento 2)** , com base em carimbos de data/hora de mídia, está sujeito a ser desconsiderado, se o sistema for lento e o **evento 1** chegar ao processador da porta do sinal após o **evento 2**. Se o **evento 1** não chegar entre a chegada do **evento 2** e a **janela de avaliação de ativação** , o **evento 1** será desconsiderado e não passará pelo processador da porta do sinal. As IDs de correlação são definidas para cada evento. Essas IDs são definidas a partir do evento inicial e são sequenciais para cada evento a seguir.

> [!IMPORTANT]
> O tempo de mídia é baseado no carimbo de data/hora da mídia de quando um evento ocorreu na mídia. A sequência de eventos que chega à porta do sinal pode não refletir a sequência de eventos que chegam no tempo de mídia.


### <a name="parameters-based-on-when-events-arrive-in-physical-time-to-the-signal-gate"></a>Parâmetros: (com base em quando os eventos chegam em tempo físico à porta do sinal)

* **minimumActivationTime (menor duração possível de uma gravação)** = o número mínimo de segundos que o processador da porta do sinal permanecerá aberto depois de ser disparado para receber novos eventos, a menos que seja interrompido pelo **maximumActivationTime**
* **maximumActivationTime (duração mais longa possível de uma gravação)** = o número máximo de segundos do evento inicial que o processador da porta do sinal permanecerá aberto depois de ser disparado para receber novos eventos, independentemente de quais eventos são recebidos
* **activationSignalOffset** = o número de segundos entre a ativação do processador da porta de sinal e quando a gravação de vídeo começa, normalmente esse valor é negativo, para iniciar a gravação antes do evento de disparo
* **activationEvaluationWindow** = o número de segundos a partir do evento de disparo inicial, no qual um evento ocorrido antes do evento inicial, em tempo de mídia, deve chegar ao processador da porta do sinal antes de ser desconsiderado e considerado uma "chegada tardia"

> [!NOTE]
> Uma chegada tardia é qualquer evento que chega após a aprovação da janela de avaliação de ativação, mas esse evento chegou antes do evento inicial em tempo de mídia.

### <a name="limits-of-parameters"></a>Limites de parâmetros

* **activationEvaluationWindow: 0 segundos a 10 segundos**

* **activationSignalOffset:-1 minuto a 1 minuto**

* **minimumActivationTime: 1 segundo a 1 hora**

* **maximumActivationTime: 1 segundo a 1 hora**


Com base no caso de uso, os parâmetros seriam definidos da seguinte maneira:

* **activationEvaluationWindow = K s**
* **activationSignalOffset =-X s**
* **minimumActivationWindow = Y s**
* **maximumActivationWindow = Z s**


Aqui está um exemplo do que a seção do nó do processador da porta de sinal deseja em uma topologia de gráfico de mídia para os seguintes valores de parâmetro:
* **activationEvaluationWindow = 1 segundo**
* **activationSignalOffset =-5 segundos**
* **minimumActivationTime = 20 segundos**
* **maximumActivationTime = 40 segundos**

> [!IMPORTANT]
> O [formato de duração ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations
) é esperado para cada valor de parâmetro. 
**Ex.: PT1S = 1 segundo**


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Vamos considerar como essa configuração do processador da porta de sinal se comportará em diferentes cenários de gravação.


**1 evento de 1 origem ( *ativação normal* )**

Um processador de porta de sinal que recebe um evento resultaria em uma gravação que inicia "deslocamento de sinal de ativação" (5) segundos antes de o evento chegar à porta. O restante da gravação é "tempo mínimo de ativação" (20) segundos de duração, pois nenhum outro evento chega antes que o tempo mínimo de ativação seja concluído para disparar novamente o portão.

Diagrama de exemplo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Ativação normal":::

* Duração da gravação =-offset + minimumActivationTime = [E1 + deslocamento, E1 + minimumActivationTime]


**2 eventos de 1 origem ( *ativação reacionada* )**

Um processador de porta de sinal que recebe dois eventos resultaria em uma gravação que inicia "deslocamento de sinal de ativação" (cinco) segundos antes do primeiro evento chegar à porta. O segundo evento chega cinco segundos após o primeiro evento, que é antes do "tempo mínimo de ativação" (20) segundos desde o primeiro evento ser concluído, portanto, o portão é disparado para permanecer aberto. O restante da gravação é "tempo mínimo de ativação" (20) segundos de duração, pois nenhum outro evento chega antes que o tempo mínimo de ativação do segundo evento seja concluído para disparar novamente a porta.

Diagrama de exemplo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Ativação reacionada":::

* Duração da gravação =-offset + (chegada da segunda entrada de evento do primeiro evento) + minimumActivationTime


**N eventos de 1 origem ( *ativação máxima* )**

Um processador de porta de sinal que recebe N eventos resultaria em uma gravação que inicia "deslocamento de sinal de ativação" (5) segundos antes do primeiro evento chegar à porta. À medida que cada evento chega antes da conclusão do "tempo mínimo de ativação" (20) segundos do evento anterior, o portão será disparado continuamente e permanecerá aberto até o "tempo de ativação máximo" (40) segundos após o primeiro evento, no qual a porta seria fechada e não aceitará mais nenhum evento novo.

Diagrama de exemplo:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Ativação máxima":::
 
* Duração da gravação =-offset + maximumActivationTime

> [!IMPORTANT]
> Os diagramas assumem que cada evento chega na mesma instância em tempo físico e de mídia. (Nenhuma chegada tardia)

## <a name="next-steps"></a>Próximas etapas

### <a name="try-it-out"></a>Experimente

[Tutorial de gravação de vídeo baseado em evento](event-based-video-recording-tutorial.md)

Usando o tutorial de gravação de vídeo baseado em evento, edite o [topology.jsem](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), modifique os parâmetros para o nó signalgateProcessor e, em seguida, siga o restante do tutorial. Examine as gravações de vídeo para analisar o efeito dos parâmetros.



