---
title: Integração do Azure Monitor para o Azure Red Hat OpenShift 4,3
description: Saiba como habilitar Azure Monitor em seu cluster Microsoft Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: 1a372ef12ef7b0afb06ed3c540daaee5e38c3374
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899159"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Integração do Azure Monitor para o Azure Red Hat OpenShift 4,3

> [!IMPORTANT]
> O Azure Red Hat OpenShift 4,3 é oferecido em versão prévia. Os recursos de visualização são autoatendimento e são fornecidos como estão e disponíveis e são excluídos do SLA (contrato de nível de serviço) e da garantia limitada. Portanto, os recursos não são destinados ao uso em produção. 

Este artigo descreve como habilitar a visualização privada de Azure Monitor para contêineres para clusters OpenShift 4,3 hospedados no local ou em qualquer ambiente de nuvem. As mesmas instruções também se aplicam para habilitar o monitoramento de clusters do Azure Red Hat OpenShift (toa) 4,3.  

## <a name="prerequisites"></a>Prerequisites

- [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3](https://helm.sh/docs/intro/install/)
- Acesso ao kubeconfig do cluster kubernetes
- Acesso a uma assinatura do Azure
- Acesso ao cluster OpenShift 4,3 para instalar o Azure Monitor para contêineres Helm gráfico
- Permissão de função de RBAC do colaborador mínimo na assinatura do Azure  
- O agente de monitoramento requer as seguintes portas e domínios de saída para enviar os dados de monitoramento para o back-end de Azure Monitor (se bloqueado pelo proxy/firewall):
  - *. ods.opinsights.azure.com 443
  - *. oms.opinsights.azure.com 443
  - *. blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Integração

> [!TIP]
> O script usa os recursos do bash 4, portanto, verifique se seu Bash está atualizado. Você pode verificar sua versão atual com `bash --version`.

### <a name="download-the-onboarding-script"></a>Baixar o script de integração

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Execute o script a seguir com azureSubscriptionId, região do espaço de trabalho, ClusterName e contexto do cluster kubernetes.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Por exemplo:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Configurar coleta de dados do agente

Por padrão, o agente de monitoramento coleta os logs de contêiner {stdout; stderr} de todos os contêineres em execução em todos os namespaces, exceto Kube-System.  Se você quiser configurar a coleção de log de contêiner específica para namespace ou namespaces específicos, você poderá consultar a [configuração do agente de informações de contêiner](../azure-monitor/insights/container-insights-agent-config.md). Aqui, você pode configurar o agente de monitoramento com as configurações de coleta de dados desejadas usando o mapa de configuração.

## <a name="configure-scraping-of-prometheus-metrics"></a>Configurar a recorte de métricas de Prometheus

Azure Monitor para contêineres recorta as métricas Prometheus e ingestão para o back-end Azure Monitor. Consulte [configuração do contêiner insights Prometheus](../azure-monitor/insights/container-insights-prometheus-integration.md) para obter instruções sobre como configurar a recorte de Prometheus.

Após a integração bem-sucedida, navegue até [monitoramento híbrido](https://aka.ms/azmon-containers-hybrid) e selecione ambiente como **"todos"** para exibir seu cluster OpenShift v4 recentemente integrado.

## <a name="disable-monitoring"></a>Desabilitar o monitoramento

Se você quiser desabilitar o monitoramento, poderá excluir Azure Monitor o gráfico de Helm para contêineres usando o comando a seguir para parar de coletar e ingerir dados de monitoramento para Azure Monitor para o back-end de contêineres.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Monitoramento de atualização

Execute novamente o script de integração conforme descrito na seção [integração](#onboarding) com o mesmo parâmetro para atualizar para o gráfico Helm mais recente.

## <a name="after-successful-onboarding"></a>Após a integração bem-sucedida

Navegue até [monitoramento híbrido](https://aka.ms/azmon-containers-hybrid)e você pode ver seu cluster OPENSHIFT/aro v4 habilitado recentemente com status de integridade na guia **clusters monitorados** . Lá, você pode obter informações mais aprofundadas, como métricas, inventário e logs, clicando na coluna **cluster** .

## <a name="supported-features"></a>Recursos compatíveis

Para obter mais informações sobre os recursos e a funcionalidade com suporte, consulte [visão geral do contêiner insights](../azure-monitor/insights/container-insights-overview.md).

Entre em contato conosco por meio de askcoin@microsoft.com para obter comentários e dúvidas.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre monitoramento, consulte:
- [Visão geral do contêiner de informações](../azure-monitor/insights/container-insights-overview.md)

- [Visão geral da consulta de log](../azure-monitor/log-query/log-query-overview.md)
