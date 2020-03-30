---
title: Integração do Monitor Azure para O Chapéu Vermelho Azure OpenShift 4.3
description: Saiba como ativar o Azure Monitor no seu cluster Microsoft Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082839"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Integração do Monitor Azure para O Chapéu Vermelho Azure OpenShift 4.3

> [!IMPORTANT] 
> Observe que o Azure Red Hat OpenShift 4.3 está atualmente disponível apenas em pré-visualização privada no Leste dos EUA. A aceitação de pré-visualização privada é apenas por convite. Certifique-se de registrar sua assinatura antes de tentar habilitar este recurso: [Azure Red Hat OpenShift Private Preview Registration](https://aka.ms/aro-preview-register)

> [!NOTE]
> Os recursos de visualização são de autoatendimento e são fornecidos como está e disponível e são excluídos do contrato de nível de serviço (SLA) e garantia limitada. Portanto, os recursos não são feitos para uso de produção.

Este artigo descreve como ativar a visualização privada do Azure Monitor para contêineres para clusters OpenShift 4.3 hospedados no prem ou em qualquer ambiente de nuvem. As mesmas instruções também se aplicam para habilitar o monitoramento para os clusters ArO (Azure Red Hat OpenShift) 4.3.  

## <a name="prerequisites"></a>Pré-requisitos

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Leme 3](https://helm.sh/docs/intro/install/)
- Acesso ao kubeconfig do cluster kubernetes
- Acesso a uma assinatura do Azure
- Acesso ao cluster OpenShift 4.3 para instalar o gráfico Azure Monitor for Containers Helm
- Permissão de função RBAC contribuinte mínimo na assinatura do Azure  
- O Agente de monitoramento requer as seguintes portas de saída - e os domínios para enviar os dados de monitoramento para o backend do Azure Monitor (Se bloqueado por proxy/firewall):
  - *.ods.opinsights.azure.com 443
  - *.oms.opinsights.azure.com 443
  - *.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Integração

> [!TIP]
> O script usa recursos do Bash 4, então certifique-se de que sua festa esteja atualizada. Você pode verificar sua `bash --version`versão atual com .

### <a name="download-the-onboarding-script"></a>Baixe o script de onboarding

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Execute o seguinte script com azureSubscriptionId, região do espaço de trabalho, clusterName e contexto do cluster Kubernetes.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Por exemplo: 

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Configurar a coleta de dados do agente

Por padrão, o Agente de Monitoramento coleta os registros de contêiner {stdout; stderr} de todos os contêineres em execução em todos os namespaces, exceto kube-system.  Se você quiser configurar a coleção de registros de contêineres específicas para espaços de nome ou namespaces específicos, você pode consultar a [configuração do agente Container Insights](../azure-monitor/insights/container-insights-agent-config.md). Aqui, você pode configurar o agente de monitoramento com as configurações desejadas de coleta de dados usando o mapa de configuração.

## <a name="configure-scraping-of-prometheus-metrics"></a>Configurar a raspagem das métricas de Prometeu

O Monitor Azure para contêineres raspa as métricas do Prometheus e ingere para o backend do Monitor Azure. Consulte a [configuração do Container Insights Prometheus](../azure-monitor/insights/container-insights-prometheus-integration.md) para obter as instruções de como configurar a raspagem do Prometeu.

Após o onboarding bem-sucedido, navegue até [o Monitoria Híbrido](https://aka.ms/azmon-containers-hybrid) e selecione o Ambiente como **"Todos"** para visualizar seu recém-instalado cluster OpenShift v4.

## <a name="disable-monitoring"></a>Desabilitar o monitoramento

Se você quiser desativar o monitoramento, você pode excluir o gráfico Azure Monitor for Containers Helm usando o seguinte comando para parar de coletar e ingerir dados de monitoramento para o Azure Monitor para back-end de contêineres.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Monitoramento de atualização

Reexecute o script de onboarding conforme descrito na seção [Onboarding](#onboarding) com o mesmo parâmetro para atualizar para o gráfico mais recente de Helm.

## <a name="after-successful-onboarding"></a>Depois de um onboarding bem sucedido

Navegue até [o Monitoria Híbrida](https://aka.ms/azmon-containers-hybrid)e você poderá ver o cluster OpenShift/ARO v4 recém-ativado com status de saúde na guia **Clusters monitorados.** Lá, você pode obter insights mais profundos, como métricas, inventário e logs clicando na coluna **Cluster.**

## <a name="supported-features"></a>Recursos compatíveis

Para obter mais informações sobre os recursos e funcionalidades suportados, consulte [visão geral do Container Insights](../azure-monitor/insights/container-insights-overview.md).

Entre em askcoin@microsoft.com contato conosco através de comentários e perguntas.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre monitoramento, consulte:
- [Visão geral do Container Insights](../azure-monitor/insights/container-insights-overview.md)

- [Visão geral do log query](../azure-monitor/log-query/log-query-overview.md)
