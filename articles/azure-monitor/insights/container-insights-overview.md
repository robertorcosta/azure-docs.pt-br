---
title: Visão geral do Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve o Azure Monitor para contêineres que monitora as soluções de Insights do Contêiner AKS e o valor que ele oferece monitorando a integridade dos clusters AKS e de Instâncias de Contêiner no Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275224"
---
# <a name="azure-monitor-for-containers-overview"></a>Visão geral do Azure Monitor para contêineres

O Azure Monitor para contêineres é um recurso projetado para monitorar o desempenho das cargas de trabalho de contêineres implantadas para:

- Clusters Kubernetes gerenciados hospedados no [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md)
- Clusters Kubernetes auto-gerenciados hospedados no Azure usando [o AKS Engine](https://github.com/Azure/aks-engine)
- [Instâncias de Contêiner do Azure](../../container-instances/container-instances-overview.md)
- Clusters Kubernetes auto-gerenciados hospedados no [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) ou no local
- [Red Hat OpenShift no Azure](../../openshift/intro-openshift.md)

O Azure Monitor para contêineres suporta clusters que executam o sistema operacional Linux e Windows Server 2019. 

Monitorar os contêineres é fundamental, principalmente ao executar um cluster de produção em grande escala e com vários aplicativos.

O Azure Monitor para contêineres oferece visibilidade de desempenho coletando métricas de processador e memória de controladores, nós e contêineres disponíveis no Kubernetes por meio da API de Métricas. Os logs do contêiner também são coletados.  Depois de habilitar o monitoramento de clusters Kubernetes, métricas e logs são coletados automaticamente para você através de uma versão contêiner do agente Log Analytics para Linux. As métricas são escritas no armazenamento de métricas e os dados de registro são gravados no armazenamento de logs associado ao espaço de trabalho [do Log Analytics.](../log-query/log-query-overview.md) 

![Monitor Azure para arquitetura de contêineres](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>O que o Azure Monitor para contêineres fornece?

O Azure Monitor para contêineres oferece uma experiência de monitoramento abrangente usando diferentes recursos do Azure Monitor. Esses recursos permitem que você entenda o desempenho e a saúde do seu cluster Kubernetes executando o sistema operacional Linux e Windows Server 2019 e as cargas de trabalho de contêineres. Com o Monitor Azure para contêineres você pode:

* Identificar contêineres AKS que estão em execução no nó e sua utilização média de processador e memória. Esse conhecimento pode ajudá-lo a identificar gargalos de recursos.
* Identifique a utilização de processador e de memória de grupos de contêineres e seus contêineres hospedados em Instâncias de Contêiner do Azure.  
* Identificar em que local o contêiner reside em um controlador ou em um pod. Esse conhecimento pode ajudá-lo a exibir o desempenho geral do controlador ou do pod. 
* Examine a utilização de recursos de cargas de trabalho em execução no host não relacionadas aos processos padrão que dão suporte ao pod.
* Compreender o comportamento do cluster sob cargas mais pesadas e médias. Esse conhecimento pode ajudá-lo a identificar as necessidades de capacidade e determinar a carga máxima que o cluster pode sustentar. 
* Configure alertas para notificá-lo proativamente ou gravá-lo quando a utilização de CPU e memória em nomes ou recipientes exceder seus limites ou quando ocorrer uma alteração do estado de saúde no cluster na infra-estrutura ou nonos de saúde.
* Integre-se ao [Prometheus](https://prometheus.io/docs/introduction/overview/) para visualizar métricas de aplicativos e cargas de trabalho coletadas de nós e Kubernetes usando [consultas](container-insights-log-search.md) para criar alertas personalizados, dashboards e análises detalhadas.
* Monitore as cargas de trabalho dos contêineres implantadas no [AKS Engine](https://github.com/Azure/aks-engine) no local e [no AKS Engine no Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
* Monitore as cargas de trabalho dos contêineres [implantadas no Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >O suporte ao Azure Red Hat OpenShift é um recurso de pré-visualização pública neste momento.
    >

As principais diferenças no monitoramento de um cluster do Windows Server em comparação com um cluster Linux são as seguintes:

- A métrica RSS de memória não está disponível para nó do Windows e recipientes.
- As informações de capacidade de armazenamento em disco não estão disponíveis para os nós do Windows.
- Os registros de contêineres não estão disponíveis para contêineres em execução em nós do Windows.
- O suporte a recursos de Dados Vivos (visualização) está disponível, com exceção dos registros de contêineres do Windows.
- Apenas os ambientes de pod são monitorados, não ambientes Docker.
- Com a versão de pré-visualização, um máximo de 30 contêineres do Windows Server são suportados. Essa limitação não se aplica aos contêineres Linux. 

Confira o vídeo a seguir fornecendo um mergulho profundo de nível intermediário para ajudá-lo a aprender sobre como monitorar seu cluster AKS com o Monitor Azure para contêineres.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Como acessar esse recurso?

Acesse o Azure Monitor para contêineres de duas maneiras, pelo Azure Monitor ou diretamente no cluster AKS selecionado. A partir do Azure Monitor, você tem uma perspectiva global de todos os contêineres implantados, que são monitorados e que não são, permitindo que você pesquise e filtre através de suas assinaturas e grupos de recursos, e, em seguida, perfurar no Azure Monitor para contêineres a partir do contêiner selecionado.  Caso contrário, você pode acessar o recurso diretamente de um contêiner AKS selecionado a partir da página AKS.  

![Visão geral dos métodos para acessar o Azure Monitor para contêineres](./media/container-insights-overview/azmon-containers-experience.png)

Se você estiver interessado em monitorar e gerenciar seus hosts de contêiner Docker e Windows que estão funcionando fora do AKS para visualizar configuração, auditoria e utilização de recursos, consulte a [solução de monitoramento de contêineres](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Próximas etapas

Para começar a monitorar seu cluster Kubernetes, [revise como habilitar o Monitor Azure para que os contêineres](container-insights-onboard.md) entendam os requisitos e os métodos disponíveis para permitir o monitoramento. 
