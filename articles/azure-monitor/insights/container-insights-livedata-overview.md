---
title: Exibir dados ao vivo (visualização) com o Monitor Azure para contêineres | Microsoft Docs
description: Este artigo descreve a visão em tempo real de registros, eventos e métricas de pod do Kubernetes sem usar kubectl no Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9e7c7a7b7bf276b3451cee1d289b8b07ac0f40ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216566"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Como visualizar registros, eventos e métricas de pod do Kubernetes em tempo real

O Azure Monitor para contêineres inclui o recurso Live Data (preview), que é um recurso avançado de diagnóstico que permite acesso direto aos registros de contêineres do Azure Kubernetes Service (AKS) (stdout/stderror), eventos e métricas de pod. Expõe o acesso `kubectl logs -c`direto `kubectl get` a `kubectl top pods`eventos e . Um painel de console mostra os registros, eventos e métricas gerados pelo mecanismo de contêiner para ajudar ainda mais na solução de problemas em tempo real.

Este artigo fornece uma visão geral detalhada e ajuda você a entender como usar esse recurso. 

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não são suportados com esse recurso. Esse recurso depende de acessar diretamente a API do Kubernetes através de um servidor proxy do seu navegador. Permitir a segurança de rede para bloquear a API do Kubernetes a partir deste proxy bloqueará esse tráfego. 

>[!NOTE]
>Este recurso está disponível em todas as regiões do Azure, incluindo o Azure China. Atualmente, não está disponível no Governo dos EUA do Azure.

Para ajudar a configurar ou solucionar problemas no recurso Dados vivos (visualização), revise nosso [guia de configuração](container-insights-livedata-setup.md). Este recurso acessa diretamente a API kubernetes, e informações adicionais sobre o modelo de autenticação podem ser encontradas [aqui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

## <a name="live-data-preview-functionality-overview"></a>Visão geral da funcionalidade de Dados ao vivo (visualização)

### <a name="search"></a>Search

![Exemplo do filtro do painel do console Live Data](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

O recurso Dados vivos (visualização) inclui a funcionalidade de pesquisa. No campo **Pesquisar,** você pode filtrar resultados digitando uma palavra ou termo-chave e quaisquer resultados correspondentes são destacados para permitir uma revisão rápida. Ao visualizar eventos, você pode limitar adicionalmente os resultados usando a pílula **Filter** encontrada à direita da barra de pesquisa. Dependendo do recurso selecionado, a pílula lista um Pod, Namespace ou cluster para escolher.  

### <a name="scroll-lock-and-pause"></a>Bloqueio e pausa de rolagem 

Para suspender o autoscroll e controlar o comportamento do painel, permitindo que você role manualmente através da nova leitura de dados, você pode usar a opção **Rolar.** Para reativar o autoscroll, basta selecionar novamente a opção **Rolar.** Você também pode pausar a recuperação de dados de log ou eventos selecionando a opção **Pausa** e, quando estiver pronto para ser retomado, basta selecionar **Reproduzir**.  

![Painel do console Dados ao vivo pausa exibição ao vivo](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>Recomendamos apenas suspender ou pausar o autopergaminho por um curto período de tempo enquanto soluciona um problema. Essas solicitações podem afetar a disponibilidade e o estrangulamento da API Kubernetes em seu cluster. 

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante a operação deste recurso. Todas as informações capturadas durante a sessão são excluídas quando você fecha seu navegador ou navega para longe dele. Os dados permanecem presentes apenas para visualização dentro da janela de cinco minutos do recurso de métricas; quaisquer métricas com mais de cinco minutos também são excluídas. As consultas de buffer Live Data (preview) dentro de limites razoáveis de uso de memória (precisam ser mais específicas aqui, o que é razoável?). 

## <a name="view-logs"></a>Exibir logs

Você pode visualizar dados de registro em tempo real à medida que são gerados pelo mecanismo de contêiner a partir da exibição **Denós,** **Controladores**e **Contêineres.** Para visualizar dados de log, execute as seguintes etapas.

1. No portal Azure, navegue até o grupo de recursos do cluster AKS e selecione seu recurso AKS.

2. No painel de cluster AKS, em **Monitoramento** no lado esquerdo, escolha **Insights**. 

3. Selecione a guia **'Não'** **ou Controladores**ou **Contêineres.**

4. Selecione um objeto na grade de desempenho e, no painel propriedades encontradas no lado direito, **selecione Exibir dados ao vivo (visualização).** Se o cluster AKS estiver configurado com um único login usando o Azure AD, você será solicitado a autenticar no primeiro uso durante essa sessão do navegador. Selecione sua conta e conclua a autenticação com o Azure.  

    >[!NOTE]
    >Ao visualizar os dados do espaço de trabalho do Log Analytics selecionando a opção **Exibir no painel** de análise do painel propriedades, os resultados de pesquisa de log mostrarão potencialmente **Nodes, Conjuntos** **de Daemon,** **Conjuntos de Réplicas,** **Empregos,** **Cron Jobs,** **Pods**e **Containers** que podem não existir mais. A tentativa de pesquisar registros de um contêiner `kubectl` que não está disponível também falhará aqui. Revise o [recurso Exibir em analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre a visualização de registros históricos, eventos e métricas.  

Depois de autenticar com sucesso, o painel do console Live Data (preview) aparecerá abaixo da grade de dados de desempenho onde você pode visualizar dados de log em um fluxo contínuo. Se o indicador de status de busca mostrar uma marca de seleção verde, que está no extremo direito do painel, significa que os dados podem ser recuperados e ele começa a transmitir para o seu console.  

![Opção de dados de exibição de propriedades de nó](./media/container-insights-livedata-overview/node-properties-pane.png)  

O título do painel mostra o nome da cápsula com a quais o recipiente está agrupado.

## <a name="view-events"></a>Exibir eventos

Você pode visualizar dados de eventos em tempo real à medida que são gerados pelo mecanismo de contêiner a partir dos **Nós,** **Controladores,** **Contêineres**e **Implantações (visualização)** quando um contêiner, pod, nó, ReplicaSet, DaemonSet, trabalho, CronJob ou Deployment é selecionado. Para visualizar eventos, execute as seguintes etapas.

1. No portal Azure, navegue até o grupo de recursos do cluster AKS e selecione seu recurso AKS.

2. No painel de cluster AKS, em **Monitoramento** no lado esquerdo, escolha **Insights**. 

3. Selecione a guia **'Não'** **controladores,** **contêineres**ou **implantações (visualização].**

4. Selecione um objeto na grade de desempenho e, no painel propriedades encontradas no lado direito, **selecione Exibir dados ao vivo (visualização).** Se o cluster AKS estiver configurado com um único login usando o Azure AD, você será solicitado a autenticar no primeiro uso durante essa sessão do navegador. Selecione sua conta e conclua a autenticação com o Azure.  

    >[!NOTE]
    >Ao visualizar os dados do espaço de trabalho do Log Analytics selecionando a opção **Exibir no painel** de análise do painel propriedades, os resultados de pesquisa de log mostrarão potencialmente **Nodes, Conjuntos** **de Daemon,** **Conjuntos de Réplicas,** **Empregos,** **Cron Jobs,** **Pods**e **Containers** que podem não existir mais. A tentativa de pesquisar registros de um contêiner `kubectl` que não está disponível também falhará aqui. Revise o [recurso Exibir em analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre a visualização de registros históricos, eventos e métricas.  

Depois de autenticar com sucesso, o painel do console Live Data (preview) aparecerá abaixo da grade de dados de desempenho. Se o indicador de status de busca mostrar uma marca de seleção verde, que está no extremo direito do painel, significa que os dados podem ser recuperados e ele começa a transmitir para o seu console. 
    
Se o objeto selecionado for um contêiner, selecione a opção **Eventos** no painel. Se você selecionou um Nó, Pod ou controlador, a exibição de eventos será automaticamente selecionada. 

![Eventos de exibição de painéis de propriedades do controlador](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

O título do painel mostra o nome do Pod com o que o contêiner está agrupado.

### <a name="filter-events"></a>Filtrar eventos 

Ao visualizar eventos, você pode limitar adicionalmente os resultados usando a pílula **Filter** encontrada à direita da barra de pesquisa. Dependendo do recurso selecionado, a pílula lista um Pod, Namespace ou cluster para escolher.  

## <a name="view-metrics"></a>Métricas de exibição 

Você pode visualizar dados métricos em tempo real, pois eles são **gerados** pelo mecanismo de contêiner a partir da exibição De nós ou **controladores** somente quando um **Pod** é selecionado. Para visualizar métricas, execute as seguintes etapas.

1. No portal Azure, navegue até o grupo de recursos do cluster AKS e selecione seu recurso AKS.

2. No painel de cluster AKS, em **Monitoramento** no lado esquerdo, escolha **Insights**. 

3. Selecione a **guia 'Não'** ou **Controladores.**

4. Selecione um objeto **Pod** na grade de desempenho e, no painel propriedades encontradano lado direito, **selecione Exibir dados ao vivo (visualização).** Se o cluster AKS estiver configurado com um único login usando o Azure AD, você será solicitado a autenticar no primeiro uso durante essa sessão do navegador. Selecione sua conta e conclua a autenticação com o Azure.  

    >[!NOTE]
    >Ao visualizar os dados do espaço de trabalho do Log Analytics selecionando a opção **Exibir no painel** de análise do painel propriedades, os resultados de pesquisa de log mostrarão potencialmente **Nodes, Conjuntos** **de Daemon,** **Conjuntos de Réplicas,** **Empregos,** **Cron Jobs,** **Pods**e **Containers** que podem não existir mais. A tentativa de pesquisar registros de um contêiner `kubectl` que não está disponível também falhará aqui. Revise o [recurso Exibir em analytics](container-insights-log-search.md#search-logs-to-analyze-data) para saber mais sobre a visualização de registros históricos, eventos e métricas.  

Depois de autenticar com sucesso, o painel do console Live Data (preview) aparecerá abaixo da grade de dados de desempenho. Os dados métricos são recuperados e começam a transmitir para o seu console para apresentação nos dois gráficos. O título do painel mostra o nome da cápsula com a quais o recipiente está agrupado.

![Ver exemplo de métricas do Pod](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>Próximas etapas

- Para continuar aprendendo a usar o Azure Monitor e monitorar outros aspectos do cluster do AKS, confira [Exibir integridade do Serviço de Kubernetes do Azure](container-insights-analyze.md).

- Exibir [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos predefinidos para criar alertas, visualizações ou realizar uma análise adicional de seus clusters.
