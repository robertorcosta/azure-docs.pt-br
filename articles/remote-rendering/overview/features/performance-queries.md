---
title: Consultas de desempenho do lado do servidor
description: Como fazer consultas de desempenho no lado do servidor por meio de chamadas à API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 3207ffca6fd0fbc943f4a2873b8b6c9029d565af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022787"
---
# <a name="server-side-performance-queries"></a>Consultas de desempenho do lado do servidor

Um bom desempenho de renderização no servidor é essencial para taxas de quadros estáveis e uma boa experiência de usuário. É importante monitorar com cuidado as características de desempenho no servidor e otimizar quando necessário. Os dados de desempenho podem ser consultados por meio de funções de API dedicadas.

A maior parte do impacto do desempenho de renderização são os dados de entrada do modelo. É possível ajustar os dados de entrada conforme descrito em [Configurar o modelo de conversão](../../how-tos/conversion/configure-model-conversion.md).

O desempenho do aplicativo no lado do cliente também pode ser um gargalo. Para uma análise detalhada do desempenho do lado do cliente, é recomendável pegar um [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) .

## <a name="clientserver-timeline"></a>Linha do tempo do cliente/servidor

Antes de entrar em detalhes sobre os diversos valores de latência, vale a pena observar os pontos de sincronização entre o cliente e o servidor na linha do tempo:

![Linha do tempo do pipeline](./media/server-client-timeline.png)

A ilustração mostra o processo:

* uma *Estimativa de pose* é inicializada pelo cliente com uma taxa de quadros constante de 60-Hz (a cada 16,6 ms)
* o servidor, em seguida, inicia a renderização, com base na pose
* o servidor envia de volta a imagem do vídeo codificada
* o cliente decodifica a imagem, executa processamento de CPU e GPU nela e, em seguida, exibe a imagem

## <a name="frame-statistics-queries"></a>Consultas de estatísticas de quadro

As estatísticas de quadro apresentam algumas informações gerais sobre o último quadro, como latência. Os dados fornecidos na estrutura `FrameStatistics` são medidos no lado do cliente, portanto, a API é uma chamada síncrona:

```cs
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<AzureSession> session)
{
    FrameStatistics frameStatistics;
    if (*session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

O objeto `FrameStatistics` recuperado contém os seguintes membros:

| Membro | Explicação |
|:-|:-|
| latencyPoseToReceive | A latência da câmera representa a estimativa no dispositivo cliente até que um quadro de servidor para essa pose esteja totalmente disponível para o aplicativo cliente. Esse valor inclui viagem de ida e volta da rede, tempo de renderização do servidor, decodificação de vídeo e compensação de tremulação. Veja o **intervalo 1 na ilustração acima.**|
| latencyReceiveToPresent | Latência da disponibilidade de um quadro remoto recebido até que o aplicativo cliente chame PresentFrame na CPU. |
| latencyPresentToDisplay  | Latência da apresentação de um quadro na CPU até a tela acender. Esse valor inclui a hora da GPU do cliente, qualquer buffer de quadro executado pelo sistema operacional, reprojeção de hardware e tempo de varredura de exibição dependente do dispositivo. Veja o **intervalo 2 na ilustração acima.**|
| timeSinceLastPresent | O tempo entre as chamadas subsequentes para PresentFrame na CPU. Valores maiores que a duração da exibição (por exemplo, 16,6 ms em um dispositivo cliente de 60 Hz) indicam problemas causados pelo aplicativo cliente que não conclui sua carga de trabalho de CPU no tempo desejado. Veja o **intervalo 3 na ilustração acima.**|
| videoFramesReceived | O número de quadros recebidos do servidor no último segundo. |
| videoFrameReusedCount | Número de quadros recebidos no último segundo que foram usados no dispositivo mais de uma vez. Valores diferentes de zero indicam que os quadros tinham que ser reutilizados e reprojetados devido à tremulação da rede ou ao tempo excessivo de renderização do servidor. |
| videoFramesSkipped | Número de quadros recebidos no último segundo que foram decodificados, mas não mostrados na exibição porque um quadro mais recente chegou. Valores diferentes de zero indicam que a tremulação da rede fez com que vários quadros ficassem atrasados e chegassem ao dispositivo cliente de modo intermitente. |
| videoFramesDiscarded | Muito semelhante a **videoFramesSkipped**, mas o motivo para ser descartado é que um quadro chegou tão tarde que ele não pode nem ser correlacionado com qualquer pose pendente. Se isso acontecer, há alguma contenção de rede grave.|
| videoFrameMinDelta | Quantidade mínima de tempo entre dois quadros consecutivos chegando no último segundo. Com o videoFrameMaxDelta, esse intervalo fornece uma indicação de tremulação causada pela rede ou pelo codec de vídeo. |
| videoFrameMaxDelta | Quantidade máxima de tempo entre dois quadros consecutivos chegando no último segundo. Com o videoFrameMinDelta, esse intervalo fornece uma indicação de tremulação causada pela rede ou pelo codec de vídeo. |

A soma de todos os valores de latência é normalmente muito maior do que o tempo de quadro disponível a 60 Hz. Não há problema nisso, pois vários quadros estão em trânsito em paralelo e novas solicitações de quadros são iniciadas na taxa de quadros desejada, conforme mostrado na ilustração. No entanto, se a latência se tornar muito grande, ela afetará a qualidade da [reprojeção de fase tardia](../../overview/features/late-stage-reprojection.md) e poderá comprometer a experiência geral.

`videoFramesReceived`, `videoFrameReusedCount` e `videoFramesDiscarded` podem ser usados para medir o desempenho da rede e do servidor. Se `videoFramesReceived` for baixa e `videoFrameReusedCount` for alta, isso poderá indicar o congestionamento da rede ou o mau desempenho do servidor. Um valor alto de `videoFramesDiscarded` também indica o congestionamento da rede.

Por fim, `timeSinceLastPresent`, `videoFrameMinDelta` e `videoFrameMaxDelta` dão uma ideia da variação de quadros de vídeo recebidos e de chamadas locais presentes. Uma variação alta significa uma taxa de quadros instável.

Nenhum dos valores acima fornece uma indicação clara de latência pura de rede (as setas vermelhas na ilustração), pois o tempo exato que o servidor está ocupado processando precisa ser subtraído do valor de ida e volta `latencyPoseToReceive`. A parte da latência geral do lado do servidor são informações que não estão disponíveis para o cliente. No entanto, o próximo parágrafo explica como esse valor é aproximado pela entrada adicional do servidor e exposto por meio do valor de `networkLatency`.

## <a name="performance-assessment-queries"></a>Consultas de avaliação de desempenho

*As consultas de avaliação de desempenho* apresentam informações mais detalhadas sobre a carga de trabalho da CPU e da GPU no servidor. Como os dados são solicitados para o servidor, a consulta de um instantâneo de desempenho segue o padrão assíncrono usual:

```cs
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

```cpp
void QueryPerformanceAssessment(ApiHandle<AzureSession> session)
{
    ApiHandle<PerformanceAssessmentAsync> assessmentQuery = *session->Actions()->QueryServerPerformanceAssessmentAsync();
    assessmentQuery->Completed([] (ApiHandle<PerformanceAssessmentAsync> res)
    {
        // do something with the result:
        PerformanceAssessment result = *res->Result();
        // ...

    });
}
```

Ao contrário do objeto `FrameStatistics`, o objeto `PerformanceAssessment` contém informações do servidor:

| Membro | Explicação |
|:-|:-|
| timeCPU | Tempo médio de CPU do servidor por quadro em milissegundos |
| timeGPU | Tempo médio de GPU do servidor por quadro em milissegundos |
| utilizationCPU | Utilização total da CPU do servidor em porcentagem |
| utilizationGPU | Utilização total da GPU do servidor em porcentagem |
| memoryCPU | Total de utilização de memória principal do servidor em porcentagem |
| memoryGPU | Utilização total da memória de vídeo dedicada em porcentagem da GPU do servidor |
| networkLatency | A latência de rede de ida e volta média aproximada em milissegundos. Na ilustração acima, isso corresponde à soma das setas vermelhas. O valor é calculado com a subtração do tempo de renderização do servidor real do valor `latencyPoseToReceive` de `FrameStatistics`. Embora essa aproximação não seja precisa, ela fornece alguma indicação da latência da rede, isolada dos valores de latência computados no cliente. |
| polygonsRendered | O número de triângulos renderizados em um quadro. Esse número também inclui os triângulos que são refigurados posteriormente durante a renderização. Isso significa que esse número não varia muito em posições de câmera diferentes, mas o desempenho pode variar drasticamente, dependendo da taxa de remoção do triângulo.|

Para ajudar a avaliar os valores, cada parte vem com uma classificação de qualidade, como **excelente**, **bom**, **mediana**ou **ruim**.
Essa métrica de avaliação fornece uma indicação aproximada da integridade do servidor, mas não deve ser vista como absoluta. Por exemplo, vamos supor que você veja uma pontuação "mediana" para a hora da GPU. Ela é considerado mediana porque fica perto do limite do orçamento do tempo geral do quadro. No entanto, no seu caso pode ser um bom valor, pois você está renderizando um modelo complexo.

## <a name="statistics-debug-output"></a>Saída de depuração de estatísticas

A classe `ARRServiceStats` é uma classe C# que encapsula as consultas de avaliação de desempenho e as estatísticas de quadro, e apresenta uma funcionalidade conveniente para retornar estatísticas como valores agregados ou uma cadeia de caracteres criada previamente. O código a seguir é a maneira mais fácil de mostrar estatísticas no lado do servidor em seu aplicativo cliente.

```cs
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

![ArrServiceStats string output](./media/arr-service-stats.png)

A API de `GetStatsString` formata uma cadeia de caracteres de todos os valores, mas cada valor também pode ser consultado programaticamente a partir da instância de `ARRServiceStats`.

Também há variantes dos membros, que agregam os valores ao longo do tempo. Veja membros com o sufixo `*Avg`, `*Max` ou `*Total`. O membro `FramesUsedForAverage` indica quantos quadros foram usados para essa agregação.

## <a name="next-steps"></a>Próximas etapas

* [Criar rastreamentos de desempenho](../../how-tos/performance-tracing.md)
* [Configurar a conversão de modelo](../../how-tos/conversion/configure-model-conversion.md)
