---
title: Consultas de desempenho do lado do servidor
description: Como fazer consultas de desempenho no lado do servidor por meio de chamadas à API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682020"
---
# <a name="server-side-performance-queries"></a>Consultas de desempenho do lado do servidor

Um bom desempenho de renderização no servidor é essencial para taxas de quadros estáveis e uma boa experiência de usuário. É importante monitorar com cuidado as características de desempenho no servidor e otimizar onde necessário. Os dados de desempenho podem ser consultados por meio de funções de API dedicadas.

A maior parte do impacto do desempenho de renderização são os dados de entrada do modelo. Você pode ajustar os dados de entrada conforme descrito em [Configurando a conversão de modelo](../../how-tos/conversion/configure-model-conversion.md).

O desempenho do aplicativo no lado do cliente também pode ser um afunilamento. Para uma análise detalhada do desempenho do lado do cliente, é recomendável fazer um rastreamento de [desempenho](../../how-tos/performance-tracing.md).

## <a name="clientserver-timeline"></a>Linha do tempo do cliente/servidor

Antes de entrar em detalhes sobre os vários valores de latência, vale a pena observar os pontos de sincronização entre o cliente e o servidor na linha do tempo:

![Linha do tempo do pipeline](./media/server-client-timeline.png)

A ilustração mostra como:

* uma *estimativa de pose* é inicializada pelo cliente com taxa de quadros constante de 60-Hz (a cada 16,6 ms)
* o servidor, em seguida, inicia a renderização, com base na pose
* o servidor envia de volta a imagem de vídeo codificada
* o cliente decodifica a imagem, executa alguma CPU e GPU em cima dela e, em seguida, exibe a imagem

## <a name="frame-statistics-queries"></a>Consultas de estatísticas de quadro

As estatísticas de quadro fornecem algumas informações de alto nível para o último quadro, como latência. Os dados fornecidos na `FrameStatistics` estrutura são medidos no lado do cliente, portanto, a API é uma chamada síncrona:

````c#
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
````

O objeto `FrameStatistics` recuperado contém os seguintes membros:

| Membro | Explicação |
|:-|:-|
| latencyPoseToReceive | A latência da câmera representa a estimativa no dispositivo cliente até que um quadro de servidor para essa pose esteja totalmente disponível para o aplicativo cliente. Esse valor inclui o ida e volta da rede, tempo de processamento do servidor, decodificação de vídeo e compensação de tremulação. Consulte **o intervalo 1 na ilustração acima.**|
| latencyReceiveToPresent | Latência da disponibilidade de um quadro remoto recebido até que o aplicativo cliente chame PresentFrame na CPU. |
| latencyPresentToDisplay  | Latência da apresentação de um quadro na CPU até a exibição acender. Esse valor inclui a hora da GPU do cliente, qualquer buffer de quadro executado pelo sistema operacional, Reprojeção de hardware e tempo de varredura de exibição dependente de dispositivo. Consulte **o intervalo 2 na ilustração acima.**|
| timeSinceLastPresent | O tempo entre as chamadas subsequentes para PresentFrame na CPU. Valores maiores que a duração da exibição (por exemplo, 16,6 ms em um dispositivo cliente de 60 Hz) indicam problemas causados pelo aplicativo cliente que não conclui sua carga de trabalho de CPU no tempo. Consulte **o intervalo 3 na ilustração acima.**|
| videoFramesReceived | O número de quadros recebidos do servidor no último segundo. |
| videoFrameReusedCount | Número de quadros recebidos no último segundo que foram usados no dispositivo mais de uma vez. Valores diferentes de zero indicam que os quadros tinham que ser reutilizados e reprojetados devido à tremulação da rede ou ao tempo excessivo de renderização do servidor. |
| videoFramesSkipped | Número de quadros recebidos no último segundo que foram decodificados, mas não mostrados na exibição porque um quadro mais recente chegou. Valores diferentes de zero indicam que a tremulação da rede fez com que vários quadros sejam atrasados e, em seguida, cheguem ao dispositivo cliente em uma intermitência. |
| videoFramesDiscarded | Muito semelhante ao **videoFramesSkipped**, mas o motivo para ser descartado é que um quadro chegou tão tarde que ele não pode ser correlacionado a qualquer pose pendente. Se isso acontecer, há alguma contenção de rede severa.|
| videoFrameMinDelta | Quantidade mínima de tempo entre dois quadros consecutivos chegando durante o último segundo. Junto com o videoFrameMaxDelta, esse intervalo fornece uma indicação de tremulação causada pela rede ou pelo codec de vídeo. |
| videoFrameMaxDelta | Quantidade máxima de tempo entre dois quadros consecutivos chegando durante o último segundo. Junto com o videoFrameMinDelta, esse intervalo fornece uma indicação de tremulação causada pela rede ou pelo codec de vídeo. |

A soma de todos os valores de latência é normalmente muito maior do que o tempo de quadro disponível a 60 Hz. Isso está OK, pois vários quadros estão em trânsito em paralelo e novas solicitações de quadros são iniciadas na taxa de quadros desejada, conforme mostrado na ilustração. No entanto, se a latência se tornar muito grande, ela afetará a qualidade da [Reprojeção de estágio tardia](../../overview/features/late-stage-reprojection.md)e poderá comprometer a experiência geral.

`videoFramesReceived`, `videoFrameReusedCount`e `videoFramesDiscarded` podem ser usados para medir o desempenho da rede e do servidor. Se `videoFramesReceived` for baixo e `videoFrameReusedCount` for alto, isso poderá indicar o congestionamento da rede ou o mau desempenho do servidor. Um valor `videoFramesDiscarded` alto também indica o congestionamento da rede.

Por fim,`timeSinceLastPresent` `videoFrameMinDelta`,, e `videoFrameMaxDelta` dar uma ideia da variação de quadros de vídeo de entrada e de chamadas locais presentes. Alta variação significa taxa de quadros inestável.

Nenhum dos valores acima fornece uma indicação clara de latência de rede pura (as setas vermelhas na ilustração), pois o tempo exato que o servidor está ocupado processando precisa ser subtraído do valor `latencyPoseToReceive`de ida e volta. A parte do lado do servidor da latência geral são informações que não estão disponíveis para o cliente. No entanto, o próximo parágrafo explica como esse valor é aproximado por entrada adicional do servidor e exposto por `networkLatency` meio do valor.

## <a name="performance-assessment-queries"></a>Consultas de avaliação de desempenho

As *consultas de avaliação de desempenho* fornecem informações mais detalhadas sobre a carga de trabalho da CPU e da GPU no servidor. Como os dados são solicitados do servidor, a consulta de um instantâneo de desempenho segue o padrão assíncrono usual:

``` cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

Ao contrário do `FrameStatistics` objeto, o `PerformanceAssessment` objeto contém informações do servidor:

| Membro | Explicação |
|:-|:-|
| timeCPU | Tempo médio de CPU do servidor por quadro em milissegundos |
| timeGPU | Tempo médio de GPU do servidor por quadro em milissegundos |
| utilizationCPU | Utilização total da CPU do servidor em porcentagem |
| utilizationGPU | Utilização total da GPU do servidor em porcentagem |
| memoryCPU | Total de utilização de memória principal do servidor em porcentagem |
| memoryGPU | Utilização total da memória de vídeo dedicada em porcentagem da GPU do servidor |
| networkLatency | A latência de rede de ida e volta média aproximada em milissegundos. Na ilustração acima, isso corresponde à soma das setas vermelhas. O valor é calculado com a `latencyPoseToReceive` subtração do tempo de renderização do servidor real do `FrameStatistics`valor de. Embora essa aproximação não seja precisa, ela fornece alguma indicação da latência da rede, isolada dos valores de latência computados no cliente. |
| polygonsRendered | O número de triângulos renderizados em um quadro. Esse número também inclui os triângulos que são refigurados posteriormente durante a renderização. Isso significa que esse número não varia muito em posições de câmera diferentes, mas o desempenho pode variar drasticamente, dependendo da taxa de remoção do triângulo.|

Para ajudá-lo a avaliar os valores, cada parte vem com uma classificação de qualidade como **ótima**, **boa**, **mediana**ou **ruim**.
Essa métrica de avaliação fornece uma indicação aproximada da integridade do servidor, mas não deve ser vista como absoluta. Por exemplo, suponha que você veja uma pontuação ' mediana ' para a hora da GPU. Ele é considerado mediana porque fica perto do limite do orçamento de tempo geral do quadro. No entanto, no entanto, pode ser um bom valor, pois você está renderizando um modelo complexo.

## <a name="statistics-debug-output"></a>Saída de depuração de estatísticas

A classe `ARRServiceStats` envolve as consultas de avaliação de desempenho e estatísticas de quadro e fornece uma funcionalidade conveniente para retornar estatísticas como valores agregados ou como uma cadeia de caracteres pré-criada. O código a seguir é a maneira mais fácil de mostrar estatísticas do lado do servidor em seu aplicativo cliente.

``` cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

O código acima popula o rótulo de texto com o seguinte texto:

![Saída de cadeia de caracteres ArrServiceStats](./media/arr-service-stats.png)

A `GetStatsString` API formata uma cadeia de caracteres de todos os valores, mas cada valor único também pode ser consultado programaticamente da `ARRServiceStats` instância.

Também há variantes dos membros, que agregam os valores ao longo do tempo. Consulte membros com sufixo `*Avg`, `*Max`ou `*Total`. O membro `FramesUsedForAverage` indica quantos quadros foram usados para essa agregação.

## <a name="next-steps"></a>Próximas etapas

* [Criar rastreamentos de desempenho](../../how-tos/performance-tracing.md)
* [Configurando a conversão de modelo](../../how-tos/conversion/configure-model-conversion.md)
