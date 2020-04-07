---
title: Consultas de desempenho em parte do servidor
description: Como fazer consultas de desempenho do lado do servidor através de chamadas de API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682020"
---
# <a name="server-side-performance-queries"></a>Consultas de desempenho em parte do servidor

O bom desempenho de renderização no servidor é fundamental para taxas de quadros estáveis e uma boa experiência do usuário. É importante monitorar cuidadosamente as características de desempenho do servidor e otimizar quando necessário. Os dados de desempenho podem ser consultados através de funções de API dedicadas.

O mais impactante para o desempenho de renderização são os dados de entrada do modelo. Você pode ajustar os dados de entrada conforme descrito na [Configuração da conversão do modelo](../../how-tos/conversion/configure-model-conversion.md).

O desempenho do aplicativo do lado do cliente também pode ser um gargalo. Para uma análise aprofundada do desempenho do lado do cliente, recomenda-se fazer um [rastreamento de desempenho](../../how-tos/performance-tracing.md).

## <a name="clientserver-timeline"></a>Cronograma do cliente/servidor

Antes de entrar em detalhes sobre os vários valores de latência, vale a pena dar uma olhada nos pontos de sincronização entre cliente e servidor na linha do tempo:

![Cronograma do gasoduto](./media/server-client-timeline.png)

A ilustração mostra como:

* uma *estimativa pose* é iniciada pelo cliente a uma taxa constante de quadros de 60 Hz (a cada 16,6 ms)
* o servidor então começa a renderizar, com base na pose
* o servidor envia de volta a imagem de vídeo codificada
* o cliente decodifica a imagem, executa alguns trabalhos de CPU e GPU em cima dela e, em seguida, exibe a imagem

## <a name="frame-statistics-queries"></a>Consultas de estatísticas de quadros

As estatísticas de quadros fornecem algumas informações de alto nível para o último quadro, como latência. Os dados fornecidos `FrameStatistics` na estrutura são medidos no lado do cliente, de modo que a API é uma chamada síncrona:

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
| laticiênciaPoseToReceive | A latência da estimativa de pose da câmera no dispositivo cliente até que um quadro de servidor para esta pose esteja totalmente disponível para o aplicativo cliente. Esse valor inclui ida e volta de rede, tempo de renderização do servidor, decodificação de vídeo e compensação de jitter. Veja **o intervalo 1 na ilustração acima.**|
| latênciaReceiveToPresent | Latência da disponibilidade de um quadro remoto recebido até que o aplicativo cliente chame PresentFrame na CPU. |
| latênciaPresenteExibiDisplay  | Latência de apresentar um quadro na CPU até que o display se acenda. Esse valor inclui o tempo de GPU do cliente, qualquer buffer de quadro realizado pelo Sistema Operacional, reprojeção de hardware e tempo de varredura de exibição dependente do dispositivo. Veja **o intervalo 2 na ilustração acima.**|
| timeSinceLastPresent | O tempo entre as chamadas subseqüentes para PresentFrame na CPU. Valores maiores que a duração do display (por exemplo, 16,6 ms em um dispositivo cliente de 60 Hz) indicam problemas causados pelo aplicativo cliente não terminar sua carga de trabalho da CPU a tempo. Veja **o intervalo 3 na ilustração acima.**|
| videoFramesrecebido | O número de quadros recebidos do servidor no último segundo. |
| videoFrameReusedCount | Número de quadros recebidos no último segundo que foram usados no dispositivo mais de uma vez. Valores não-zero indicam que os quadros tiveram que ser reutilizados e reprojetados devido ao nervosismo da rede ou ao tempo excessivo de renderização do servidor. |
| videoFramesSkipped | Número de quadros recebidos no último segundo que foram decodificados, mas não mostrados em exibição porque um quadro mais novo chegou. Valores não-zero indicam que o nervosismo da rede fez com que vários quadros fossem adiados e, em seguida, chegassem ao dispositivo cliente juntos em uma explosão. |
| videoFramesdescartado | Muito semelhante ao **videoFramesSkipped,** mas a razão para ser descartado é que um quadro chegou tão tarde que não pode nem ser correlacionado com qualquer pose pendente mais. Se isso acontecer, há uma séria disputa de rede.|
| vídeoFrameMinDelta | Quantidade mínima de tempo entre dois quadros consecutivos que chegam durante o último segundo. Juntamente com o videoFrameMaxDelta, esta gama dá uma indicação de nervosismo causado pela rede ou pelo codec de vídeo. |
| vídeoFrameMaxDelta | Quantidade máxima de tempo entre dois quadros consecutivos que chegam durante o último segundo. Juntamente com o videoFrameMinDelta, esta faixa dá uma indicação de nervosismo causado pela rede ou pelo codec de vídeo. |

A soma de todos os valores de latência é tipicamente muito maior do que o tempo de quadro disponível em 60 Hz. Isso é OK, porque vários quadros estão em vôo em paralelo, e novas solicitações de quadros são iniciadas na taxa de quadros desejada, como mostrado na ilustração. No entanto, se a latência se tornar muito grande, ela afeta a qualidade da [reprojeção tardia](../../overview/features/late-stage-reprojection.md)do estágio final, podendo comprometer a experiência geral.

`videoFramesReceived`, `videoFrameReusedCount`e `videoFramesDiscarded` pode ser usado para medir o desempenho da rede e do servidor. Se `videoFramesReceived` for `videoFrameReusedCount` baixo e for alto, isso pode indicar congestionamento da rede ou desempenho ruim do servidor. Um `videoFramesDiscarded` alto valor também indica congestionamento da rede.

Por`timeSinceLastPresent`último, `videoFrameMinDelta`e `videoFrameMaxDelta` dar uma ideia da variância de quadros de vídeo de entrada e chamadas locais presentes. Alta variância significa taxa de quadros instável.

Nenhum dos valores acima dá clara indicação de latência pura da rede (as setas vermelhas na ilustração), pois `latencyPoseToReceive`o tempo exato em que o servidor está ocupado renderizando precisa ser subtraído do valor da ida e volta . A parte do lado do servidor da latência geral é uma informação que não está disponível para o cliente. No entanto, o próximo parágrafo explica como esse valor é aproximado através `networkLatency` de entrada adicional do servidor e exposto através do valor.

## <a name="performance-assessment-queries"></a>Consultas de avaliação de desempenho

*As consultas de avaliação* de desempenho fornecem informações mais detalhadas sobre a carga de trabalho da CPU e da GPU no servidor. Uma vez que os dados são solicitados no servidor, consultar um instantâneo de desempenho segue o padrão assincronico usual:

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

Ao contrário `FrameStatistics` do `PerformanceAssessment` objeto, o objeto contém informações do lado do servidor:

| Membro | Explicação |
|:-|:-|
| cpu de tempo | Tempo médio da CPU do servidor por período em milissegundos |
| timeGPU | Tempo médio de GPU do servidor por período em milissegundos |
| CPU de utilização | Utilização total da CPU do servidor em porcentagem |
| utilizaçãoGPU | Utilização total da GPU do servidor em porcentagem |
| CPU de memória | Utilização total da memória principal do servidor em porcentagem |
| memoryGPU | Total de utilização de memória de vídeo dedicada em porcentagem da GPU do servidor |
| NetworkLatency | A latência média da rede de ida e volta aproximada em milissegundos. Na ilustração acima, isso corresponde à soma das setas vermelhas. O valor é calculado subtraindo o tempo `latencyPoseToReceive` real `FrameStatistics`de renderização do servidor do valor de . Embora essa aproximação não seja precisa, dá alguma indicação da latência da rede, isolada dos valores de latência computados no cliente. |
| polígonosRenderizados | O número de triângulos renderizados em um quadro. Este número também inclui os triângulos que são abatidos mais tarde durante a renderização. Isso significa que esse número não varia muito entre diferentes posições da câmera, mas o desempenho pode variar drasticamente, dependendo da taxa de abate do triângulo.|

Para ajudá-lo a avaliar os valores, cada porção vem com uma classificação de qualidade como **Ótimo**, **Bom,** **Medíocre**ou **Ruim**.
Essa métrica de avaliação fornece uma indicação aproximada da saúde do servidor, mas não deve ser vista como absoluta. Por exemplo, suponha que você veja uma pontuação 'medíocre' para o tempo de GPU. É considerado medíocre porque se aproxima do limite para o orçamento geral do prazo. No seu caso, no entanto, pode ser um bom valor, no entanto, porque você está tornando um modelo complexo.

## <a name="statistics-debug-output"></a>Produção de depuração de estatísticas

A `ARRServiceStats` classe envolve as estatísticas de quadros e as consultas de avaliação de desempenho e fornece funcionalidade conveniente para retornar estatísticas como valores agregados ou como uma seqüência pré-construída. O código a seguir é a maneira mais fácil de mostrar estatísticas do lado do servidor no seu aplicativo cliente.

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

O código acima preenche o rótulo de texto com o seguinte texto:

![Saída da seqüência ArrServiceStats](./media/arr-service-stats.png)

A `GetStatsString` API formata uma seqüência de todos os valores, mas cada `ARRServiceStats` valor também pode ser consultado programáticamente a partir da instância.

Há também variantes dos membros, que agregam os valores ao longo do tempo. Consulte os membros `*Avg`com `*Max`sufixo, ou `*Total`. O `FramesUsedForAverage` membro indica quantos quadros foram usados para esta agregação.

## <a name="next-steps"></a>Próximas etapas

* [Criar traços de desempenho](../../how-tos/performance-tracing.md)
* [Configuração da conversão do modelo](../../how-tos/conversion/configure-model-conversion.md)
