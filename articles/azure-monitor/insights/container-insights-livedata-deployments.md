---
title: Ver monitor azure para implantações de contêineres (pré-visualização) | Microsoft Docs
description: Este artigo descreve a visão em tempo real das Implantações kubernetes sem usar kubectl no Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75404767"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Como visualizar implantações (visualização) em tempo real

Com o Azure Monitor para contêineres, o recurso View Deployments (preview) emula o acesso `kubeclt get deployments` `kubectl describe deployment {your deployment}` direto aos objetos de implantação do Kubernetes em tempo real, expondo os comandos e os comandos. 

>[!NOTE]
>Os clusters AKS habilitados como [clusters privados](https://azure.microsoft.com/updates/aks-private-cluster/) não são suportados com esse recurso. Esse recurso depende de acessar diretamente a API do Kubernetes através de um servidor proxy do seu navegador. Permitir a segurança de rede para bloquear a API do Kubernetes a partir deste proxy bloqueará esse tráfego. 

>[!NOTE]
>Este recurso está disponível em todas as regiões do Azure, incluindo o Azure China. Atualmente, não está disponível no Governo dos EUA do Azure.

Para saber mais, revise a documentação do Kubernetes sobre [Implantações.](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) 

## <a name="how-it-works"></a>Como ele funciona

O recurso Live Data (visualização) acessa diretamente a API do Kubernetes, e informações adicionais sobre o modelo de autenticação podem ser encontradas [aqui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

O recurso Implantações (visualização) executa uma carga única (atualizável) contra o ponto `/apis/apps/v1/deployments`final das implantações . Ele permite selecionar uma determinada implantação e carregar os detalhes descrever `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`para essa implantação específica no ponto final de implantação . 

Selecionar **Atualizar** no canto superior esquerdo da página atualiza a lista de implantação. Isso simula re-executar `kubectl` o comando. 

>[!IMPORTANT]
>Nenhum dado é armazenado permanentemente durante a operação deste recurso. Todas as informações capturadas durante a sessão são excluídas quando você fecha seu navegador ou navega para longe dele.  

>[!NOTE]
>Não é possível fixar dados de dados de live (Visualização) do console em um painel do Azure.

## <a name="deployments-describe"></a>Implantações descrevem

Para exibir descrever detalhes para uma `kubectl describe deployment`implantação, que é o equivalente a , executar as seguintes etapas.

1. No portal Azure, navegue até o grupo de recursos do cluster AKS e selecione seu recurso AKS.

2. No painel de cluster AKS, em **Monitoramento** no lado esquerdo, escolha **Insights**. 

3. Selecione a guia **Implantações (visualização).**

    ![Visualizações de implantações no portal Azure](./media/container-insights-livedata-deployments/deployment-view.png)

A exibição mostra uma lista de todas as implantações em execução, juntamente `kubectl get deployments –all-namespaces`com o namespace e outras informações detalhadas, emulando a execução do comando . Você pode classificar os resultados selecionando qualquer uma das colunas. 

![Implantações propriedades detalhes do painel](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Quando você seleciona uma implantação da lista, um painel de propriedade é exibido automaticamente no lado direito da página. Ele mostra informações relacionadas à implantação selecionada que `kubectl describe deployment {deploymentName}`você visualizaria se executasse o comando . Você deve ter notado que a informação descrita está faltando alguns detalhes. Mais notavelmente **o Modelo** está faltando. A seleção da guia **Raw** permite que você navegue até os detalhes não analisados descrever.  

![Implantações propriedades pane detalhes brutos](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Enquanto você analisa os detalhes da implantação, você pode ver registros de contêineres e eventos em tempo real. Selecione o **console Show live** e o painel de console Live Data (preview) aparecerá abaixo da grade de dados de implantações onde você pode visualizar dados de log ao vivo em uma transmissão contínua. Se o indicador de status de busca mostrar uma marca de seleção verde, que está no extremo direito do painel, significa que os dados podem ser recuperados e ele começa a transmitir para o seu console.

Você também pode filtrar por namespace ou eventos de nível de cluster. Para saber mais sobre os dados de visualização em tempo real no console, consulte [Exibir dados ao vivo (visualização) com o Azure Monitor para contêineres](container-insights-livedata-overview.md). 

![Implantações visualizam dados ao vivo no console](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Próximas etapas

- Para continuar aprendendo a usar o Azure Monitor e monitorar outros aspectos do cluster do AKS, confira [Exibir integridade do Serviço de Kubernetes do Azure](container-insights-analyze.md).

- Exibir [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos predefinidos para criar alertas, visualizações ou realizar uma análise adicional de seus clusters.
