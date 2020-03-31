---
title: Monitore a saúde do cluster Kubernetes com o Monitor Azure para contêineres | Microsoft Docs
description: Este artigo descreve como você pode visualizar e analisar a saúde de seus clusters AKS e não-AKS com o Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843983"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Entenda a saúde do cluster Kubernetes com o Azure Monitor para contêineres

Com o Azure Monitor para contêineres, ele monitora e informa o estado de saúde dos componentes de infra-estrutura gerenciados e todos os nós em execução em qualquer cluster Kubernetes suportado pelo Azure Monitor para contêineres. Essa experiência vai além do estado de saúde do cluster calculado e relatado na [exibição de vários clusters,](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)onde agora você pode entender se um ou mais nós no cluster são limitados por recursos, ou um nó ou pod não está disponível que poderia impactar um aplicativo em execução no cluster com base em métricas curadas.

>[!NOTE]
>O recurso Saúde está em pré-visualização pública neste momento.
>

Para obter informações sobre como ativar o Monitor Azure para contêineres, consulte [Onboard Azure Monitor para contêineres](container-insights-onboard.md).

>[!NOTE]
>Para dar suporte aos clusters do AKS Engine, verifique se ele atende ao seguinte:
>- Ele está usando a versão mais recente do [cliente HELM](https://helm.sh/docs/using_helm/).
>- A versão do agente contêiner é *microsoft/oms:ciprod11012019*. Para atualizar o agente, consulte [o agente de atualização no cluster Kubernetes](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster).
>

## <a name="overview"></a>Visão geral

No Azure Monitor para contêineres, o recurso Health (preview) fornece monitoramento proativo de saúde do seu cluster Kubernetes para ajudá-lo a identificar e diagnosticar problemas. Isso lhe dá a capacidade de ver problemas significativos detectados. Monitora a avaliação da saúde do seu cluster executado no agente contêiner em seu cluster, e os dados de saúde são gravados na tabela **KubeHealth** em seu espaço de trabalho Log Analytics. 

A saúde do cluster kubernetes baseia-se em uma série de cenários de monitoramento organizados pelos seguintes objetos e abstrações kubernetes:

- Infra-estrutura Kubernetes - fornece um rollup do servidor API do Kubernetes, ReplicaSets e DaemonSets em execução em nós implantados em seu cluster, avaliando a utilização da CPU e da memória e uma disponibilidade de Pods

    ![Kubernetes infra-estrutura visão de rollup de saúde](./media/container-insights-health/health-view-kube-infra-01.png)

- Nodes - fornece um rollup das piscinas de nó e estado dos nodos individuais em cada pool, avaliando a utilização da CPU e da memória, e o status de um Nó como relatado pela Kubernetes.

    ![Visão de rollup de saúde dos nódulos](./media/container-insights-health/health-view-nodes-01.png)

Atualmente, apenas o status de um kubelet virtual é suportado. O estado de saúde para CPU e utilização de memória de nódulos kublet virtuais é relatado como **Desconhecido,** uma vez que um sinal não é recebido deles.

Todos os monitores são mostrados em um layout hierárquico no painel da Hierarquia da Saúde, onde um monitor agregado representando o objeto kubernetes ou abstração (isto é, infra-estrutura Kubernetes ou Nós) são os monitores mais altos refletindo a saúde combinada de todos monitores de crianças dependentes. Os principais cenários de monitoramento utilizados para derivar a saúde são:

* Avalie a utilização da CPU a partir do nó e do recipiente.
* Avalie a utilização da memória do nó e do recipiente.
* Status de Pods e Nodes com base no cálculo de seu estado pronto relatado por Kubernetes.

Os ícones usados para indicar o estado são os seguintes:

|ícone|Significado|  
|--------|-----------|  
|![Ícone de verificação Verde indica íntegro](./media/container-insights-health/healthyicon.png)|Êxito, integridade OK (verde)|  
|![Triângulo amarelo e ponto de exclamação indica aviso](./media/container-insights-health/warningicon.png)|Aviso (amarelo)|  
|![Botão vermelho com X branco indica estado crítico](./media/container-insights-health/criticalicon.png)|Crítico (vermelho)|  
|![Ícone cinza-out](./media/container-insights-health/grayicon.png)|Desconhecido (cinza)|  

## <a name="monitor-configuration"></a>Configuração do monitor

Para entender o comportamento e a configuração de cada monitor que suporta o Monitor Azure para contêineres Recurso de saúde, consulte O guia de [configuração do monitor de saúde](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Faça login no [portal Azure](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Ver a saúde de um cluster AKS ou não-AKS

O acesso ao recurso Azure Monitor for containers Health (preview) está disponível diretamente em um cluster AKS, selecionando **Insights** do painel esquerdo no portal Azure. Na seção **Insights**, selecione **Contêineres**. 

Para visualizar a saúde de um cluster não-AKS, que é um cluster AKS Engine hospedado no local ou no Azure Stack, selecione **O Monitor Azure** do painel esquerdo no portal Azure. Na seção **Insights**, selecione **Contêineres**.  Na página multi-cluster, selecione o cluster não-AKS da lista.

No Monitor Azure para contêineres, a partir da página **Cluster,** selecione **Saúde**.

![Exemplo de painel de saúde de cluster](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Revisar a saúde do cluster

Quando a página Saúde é aberta, por padrão, a **Kubernetes Infrastructure** é selecionada na grade **aspecto da saúde.**  A grade resume o estado atual de rollup de saúde da infra-estrutura kubernetes e dos nódulos de cluster. A seleção de qualquer aspecto da saúde atualiza os resultados no painel de Hierarquia da Saúde (ou seja, o painel médio) e mostra todos os monitores infantis em um layout hierárquico, exibindo seu estado de saúde atual. Para exibir mais informações sobre qualquer monitor dependente, você pode selecionar um e um painel de propriedade exibido automaticamente no lado direito da página. 

![Painel de propriedade de saúde de cluster](./media/container-insights-health/health-view-property-pane.png)

No painel da propriedade, você aprende o seguinte:

- Na **guia Visão Geral,** ele mostra o estado atual do monitor selecionado, quando o monitor foi calculado pela última vez e quando ocorreu a última alteração de estado. Informações adicionais são mostradas dependendo do tipo de monitor selecionado na hierarquia.

    Se você selecionar um monitor agregado no painel Hierarquia da Saúde, a guia **Visão Geral** no painel da propriedade, ele mostrará um acúmulo do número total de monitores de crianças na hierarquia e quantos monitores agregados estão em um estado crítico, de advertência e de saúde. 

    ![Guia visão geral do painel de propriedade de saúde para monitor agregado](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Se você selecionar um monitor de unidade no painel hierarquia da saúde, ele também mostrará em **Último estado alterar** as amostras anteriores calculadas e relatadas pelo agente contêiner nas últimas quatro horas. Isso se baseia no cálculo dos monitores unitários para comparar vários valores consecutivos para determinar seu estado. Por exemplo, se você selecionou o monitor da unidade *estadual pronto para pod,* ele mostrará as duas últimas amostras controladas pelo parâmetro *ConsecutiveSamplesForStateTransition*. Para obter mais informações, consulte a descrição detalhada dos [monitores](container-insights-health-monitors-config.md#unit-monitors)da unidade .
    
    ![Guia visão geral do painel de propriedade de saúde](./media/container-insights-health/health-overview-unit-monitor.png)

    Se o tempo relatado pela **última mudança de estado** for um dia ou mais antigo, é o resultado de nenhuma alteração de estado para o monitor. No entanto, se a última amostra recebida para um monitor de unidade tiver mais de quatro horas de idade, isso provavelmente indica que o agente contêiner não tem enviado dados. Se o agente souber que existe um recurso específico, por exemplo, um Nó, mas não tiver recebido dados dos monitores de utilização da CPU ou da memória do Nó (por exemplo), então o estado de saúde do monitor é definido como **Desconhecido**.  

- Na guia**Config,** ele mostra as configurações padrão do parâmetro de configuração (apenas para monitores unitários, não monitores agregados) e seus valores.
- Na aba **Conhecimento,** contém informações que explicam o comportamento do monitor e como ele avalia para a condição insalubre.

Os dados de monitoramento nesta página não são atualizados automaticamente e você precisa selecionar **Atualizar** na parte superior da página para ver o estado de saúde mais recente recebido do cluster.

## <a name="next-steps"></a>Próximas etapas

Exibir [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos predefinidos para avaliar ou personalizar para alertar, visualizar ou analisar seus clusters.
