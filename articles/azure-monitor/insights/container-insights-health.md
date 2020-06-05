---
title: Monitorar a integridade do cluster Kubernetes com o Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode exibir e analisar a integridade de seus clusters AKS e não AKS com o Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 94fdb6388134eae326ed5a8e46fbd3d13f8da2d5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649583"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Compreender a integridade do cluster Kubernetes com o Azure Monitor para contêineres

Com o Azure Monitor para contêineres, ele monitora e relata o status de integridade dos componentes de infraestrutura gerenciada e de todos os nós em execução em qualquer cluster Kubernetes com suporte do Azure Monitor para contêineres. Essa experiência se estende além do status de integridade do cluster calculado e relatado na [exibição de vários clusters](container-insights-analyze.md#multi-cluster-view-from-azure-monitor), onde agora você pode entender se um ou mais nós no cluster estão com recursos restritos ou se um nó ou pod está indisponível, o que poderia impactar um aplicativo em execução no cluster com base em métricas selecionadas.

>[!NOTE]
>O recurso Integridade fará a transição para uma versão prévia privada no final de junho de 2020. Para obter informações adicionais, verifique o [Anúncio sobre atualizações do Azure](https://azure.microsoft.com/updates/ci-health-limited-preview/) a seguir.
>

Para saber mais sobre como habilitar o Azure Monitor para contêineres, confira [Integrar o Azure Monitor para contêineres](container-insights-onboard.md).

>[!NOTE]
>Para oferecer suporte a clusters do mecanismo AKS, verifique se ele atende ao seguinte:
>- Ele está usando a versão mais recente do [cliente do HELM](https://helm.sh/docs/using_helm/).
>- A versão do agente em contêiner é *Microsoft/OMS: ciprod11012019*. Para atualizar o agente, confira [Agente de atualização no cluster Kubernetes](container-insights-manage-agent.md#how-to-upgrade-the-azure-monitor-for-containers-agent).
>

## <a name="overview"></a>Visão geral

No Azure Monitor para contêineres, o recurso Integridade (versão prévia) fornece monitoramento proativo da integridade do cluster Kubernetes para ajudar a identificar e diagnosticar problemas. Ele permite exibir problemas significativos detectados. Monitora a avaliação da integridade do seu cluster executada no agente em contêiner do cluster e os dados da integridade são gravados na tabela **KubeHealth** no workspace do Log Analytics. 

A integridade do cluster Kubernetes é baseada em vários cenários de monitoramento organizados pelos seguintes objetos e abstrações do Kubernetes:

- Infraestrutura do Kubernetes - fornece um pacote cumulativo de atualizações do servidor da API Kubernetes, ReplicaSets e DaemonSets em execução nos nós implantados no cluster, avaliando a utilização da CPU e da memória e a disponibilidade dos Pods

    ![Exibição de pacote cumulativo de integridade da infraestrutura do Kubernetes](./media/container-insights-health/health-view-kube-infra-01.png)

- Nós – fornece um conjunto de conjuntos de nós e o estado de nós individuais em cada conjunto, avaliando a utilização da CPU e da memória e o status de um nó, conforme relatado pelo Kubernetes.

    ![Exibição de acúmulo de integridade dos nós](./media/container-insights-health/health-view-nodes-01.png)

Atualmente, há suporte apenas para o status de um kubelet virtual. O estado de integridade da utilização da CPU e da memória dos nós virtuais do kubelet é relatado como **Desconhecido**, pois não é recebido um sinal deles.

Todos os monitores são mostrados em um layout hierárquico no painel Hierarquia de Integridade, em que um monitor agregado que representa o objeto ou abstração do Kubernetes (ou seja, infraestrutura ou nós do Kubernetes) é o monitor mais alto, refletindo a integridade combinada de todos os monitores filhos dependentes. Os principais cenários de monitoramento usados para obter a integridade são:

* Avaliar a utilização da CPU do nó e contêiner.
* Avaliar a utilização da memória do nó e do contêiner.
* Status de pods e nós com base no cálculo do estado de prontidão relatado pelo Kubernetes.

Os ícones usados para indicar o estado são os seguintes:

|ícone|Significado|  
|--------|-----------|  
|![Ícone de verificação verde indica íntegro](./media/container-insights-health/healthyicon.png)|Êxito, integridade está OK (verde)|  
|![Triângulo amarelo e ponto de exclamação indica aviso](./media/container-insights-health/warningicon.png)|Aviso (amarelo)|  
|![Botão vermelho com X branco indica estado crítico](./media/container-insights-health/criticalicon.png)|Crítico (vermelho)|  
|![Ícone em cinza](./media/container-insights-health/grayicon.png)|Desconhecido (cinza)|  

## <a name="monitor-configuration"></a>Configuração do Monitor

Para compreender o comportamento e a configuração de cada monitor que oferece suporte ao recurso Integridade do Azure Monitor para contêineres, confira [Guia de configuração do monitor de integridade](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Exibir a integridade de um cluster AKS ou não AKS

O acesso ao recurso Integridade (versão prévia) do Azure Monitor para contêineres está disponível diretamente em um cluster da AKS, selecionando **Insights** no painel esquerdo do portal do Azure. Na seção **Insights**, selecione **Contêineres**. 

Para exibir a integridade de um cluster não AKS, que é um cluster do Mecanismo AKS hospedado no local ou no Azure Stack, selecione **Azure Monitor** no painel esquerdo do portal do Azure. Na seção **Insights**, selecione **Contêineres**.  Na página de vários clusters, selecione o cluster não AKS na lista.

No Azure Monitor para contêineres, na página **Cluster**, selecione **Integridade**.

![Exemplo de painel de integridade do cluster](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Verificar integridade do cluster

Quando a página Integridade é aberta, por padrão,  **Infraestrutura Kubernetes** é selecionada na grade **Aspecto da Integridade**.  A grade resume o estado atual do acúmulo de integridade da infraestrutura do Kubernetes e nós do cluster. A seleção de qualquer aspecto de integridade atualiza os resultados no painel Hierarquia de integridade (ou seja, o painel central) e mostra todos os monitores filhos em um layout hierárquico, exibindo seu estado de integridade atual. Para exibir mais informações sobre qualquer monitor dependente, você pode selecionar um e um painel de propriedades é exibido automaticamente no lado direito da página. 

![Painel de propriedades de integridade do cluster](./media/container-insights-health/health-view-property-pane.png)

No painel de propriedades, você aprende o seguinte:

- Na guia **Visão gera** , é mostrado o estado atual do monitor selecionado, quando o monitor foi calculado pela última vez e quando ocorreu a última alteração de estado. Informações adicionais são mostradas dependendo do tipo de monitor selecionado na hierarquia.

    Se você selecionar um monitor agregado no painel Hierarquia de Integridade, na guia **Visão Geral** no painel de propriedades, ele mostrará um acúmulo do número total de monitores filhos na hierarquia e quantos monitores agregados estão em uma situação crítica, aviso e estado íntegro. 

    ![Guia Visão geral do painel de propriedades de integridade para o monitor agregado](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Se você selecionar um monitor de unidade no painel Hierarquia de Integridade, ele também mostrará em **Última alteração de estado** as amostras anteriores calculadas e relatadas pelo agente em contêiner nas últimas quatro horas. Isso se baseia no cálculo dos monitores da unidade para comparar vários valores consecutivos para determinar seu estado. Por exemplo, se você selecionou o monitor da unidade *Estado pronto do pod*, ele mostra as duas últimas amostras controladas pelo parâmetro *ConsecutiveSamplesForStateTransition*. Para saber mais, confira a descrição detalhada de [monitores de unidade](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Guia Visão geral do painel de propriedades de integridade](./media/container-insights-health/health-overview-unit-monitor.png)

    Se o horário relatado por **Última alteração de estado** for de um dia ou mais, será o resultado de nenhuma alteração no estado do monitor. No entanto, se a última amostra recebida para um monitor de unidade tiver mais de quatro horas, isso provavelmente indica que o agente em contêiner não está enviando dados. Se o agente souber que existe um recurso específico, por exemplo, um Nó, mas não recebeu dados da CPU ou dos monitores de utilização de memória do Nó (como exemplo), o estado de integridade do monitor é definido como **Desconhecido**.  

- Na guia **Configuração**, são mostradas as configurações padrão dos parâmetros de configuração (apenas para monitores de unidade, não monitores agregados) e seus valores.
- Na guia **Conhecimento**, há informações que explicam o comportamento do monitor e como ele avalia a condição não íntegra.

Os dados de monitoramento nesta página não são atualizados automaticamente e é preciso selecionar **Atualizar** na parte superior da página para ver o estado de integridade mais recente recebido do cluster.

## <a name="next-steps"></a>Próximas etapas

Visualize [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos predefinidos para avaliar ou personalizar para alertar, visualizar ou analisar seus clusters.
