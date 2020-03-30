---
title: Veja métricas em tempo real com o Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve a visão em tempo real das métricas sem usar kubectl com o Monitor Azure para contêineres.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 4604635c985057ec0b7f49a0d1cca7111dfc8eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216583"
---
# <a name="how-to-view-metrics-in-real-time"></a>Como visualizar métricas em tempo real

O recurso Azure Monitor for containers Live Data (preview) permite visualizar métricas sobre o estado do nó e do pod em um cluster em tempo real. Ele emula o `kubectl top nodes`acesso `kubectl get pods –all-namespaces`direto `kubectl get nodes` ao , e manda para chamar, analisar e visualizar os dados em gráficos de desempenho que estão incluídos neste Insight. 

Este artigo fornece uma visão geral detalhada e ajuda você a entender como usar esse recurso.  

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não são suportados com esse recurso. Esse recurso depende de acessar diretamente a API do Kubernetes através de um servidor proxy do seu navegador. Permitir a segurança de rede para bloquear a API do Kubernetes a partir deste proxy bloqueará esse tráfego. 

>[!NOTE]
>Este recurso está disponível em todas as regiões do Azure, incluindo o Azure China. Atualmente, não está disponível no Governo dos EUA do Azure.

Para obter ajuda para configurar ou solucionar problemas no recurso Dados vivos (visualização), revise nosso [guia de configuração](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Como funciona 

O recurso Live Data (visualização) acessa diretamente a API do Kubernetes, e informações adicionais sobre o modelo de autenticação podem ser encontradas [aqui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Este recurso realiza uma operação de votação `/api/v1/nodes` `/apis/metrics.k8s.io/v1beta1/nodes`contra `/api/v1/pods`os pontos finais das métricas (incluindo , e ), que é a cada cinco segundos por padrão. Esses dados são armazenados em cache no seu navegador e mapeados nos quatro gráficos de desempenho incluídos no Azure Monitor para contêineres na guia **Cluster,** selecionando **Go Live (visualização)**. Cada pesquisa subseqüente é mapeada em uma janela de visualização de cinco minutos. 

![Opção Ir ao vivo na exibição Cluster](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

O intervalo de votação é configurado a partir da parada de **intervalo de conjunto,** permitindo que você defina a votação para novos dados a cada 1, 5, 15 e 30 segundos. 

![Intervalo de votação drop-down do Go Live](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>Recomendamos definir o intervalo de votação para um segundo enquanto soluciona um problema por um curto período de tempo. Essas solicitações podem afetar a disponibilidade e o estrangulamento da API Kubernetes em seu cluster. Depois, reconfigure-se para um intervalo de votação mais longo. 

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante a operação deste recurso. Todas as informações capturadas durante esta sessão são imediatamente excluídas quando você fecha seu navegador ou navega para longe do recurso. Os dados permanecem presentes apenas para visualização dentro da janela de cinco minutos; quaisquer métricas com mais de cinco minutos também são excluídas permanentemente.

Esses gráficos não podem ser fixados no último painel Azure que você visualizou no modo ao vivo.

## <a name="metrics-captured"></a>Métricas capturadas

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Utilização da CPU do nó % / utilização da memória do nó % 

Esses dois gráficos de desempenho `kubectl top nodes` mapeiam o equivalente a invocar e capturar os resultados das colunas **CPU%** e **MEMORY%** para o respectivo gráfico. 

![Kubectl top nodes exemplo resultados](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Gráfico percentual de utilização da CPU de nodes](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Gráfico percentual de utilização da memória do nó](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

Os cálculos por cento funcionarão em clusters maiores para ajudar a identificar nódulos mais estranhos em seu cluster. Por exemplo, para entender se os nós são subutilizados para fins de rebaixação de escala. Utilizando a agregação **Min,** você pode ver quais nódulos têm baixa utilização no cluster. Para investigar melhor, selecione a guia **''''Nodes'** e classifique a grade por utilização de CPU ou memória.

Isso também ajuda você a entender quais nós estão sendo empurrados para seus limites e se a escala pode ser necessária. Utilizando as agregações **Max** e **P95** pode ajudá-lo a ver se há nós no cluster com alta utilização de recursos. Para uma investigação mais aprofundada, você mudaria novamente para a guia **Nodes.**

### <a name="node-count"></a>Contagem de nodos

Este gráfico de desempenho mapeia o equivalente a invocar `kubectl get nodes` e mapear a coluna **STATUS** para um gráfico agrupado por tipos de status.

![Kubectl obter resultados exemplo de nódulos](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Gráfico de contagem de nódulos](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Os nós são relatados em um estado **pronto** ou **não pronto.** Eles são contados (e uma contagem total é criada), e os resultados dessas duas agregações são mapeados.
Por exemplo, para entender se seus nós estão caindo em estados falidos. Utilizando a agregação **Not Ready,** você pode ver rapidamente o número de nódulos em seu cluster atualmente no estado **Not Ready.**

### <a name="active-pod-count"></a>Contagem de pods ativos

Este gráfico de desempenho mapeia um equivalente a invocar `kubectl get pods –all-namespaces` e mapear a coluna **STATUS,** o gráfico agrupado por tipos de status.

![Kubectl obter pods exemplo resultados](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Gráfico de contagem de nodes pod](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Os nomes de `kubectl` status interpretados podem não corresponder exatamente no gráfico. 

## <a name="next-steps"></a>Próximas etapas

Exibir [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos predefinidos para criar alertas, visualizações ou realizar uma análise adicional de seus clusters.
