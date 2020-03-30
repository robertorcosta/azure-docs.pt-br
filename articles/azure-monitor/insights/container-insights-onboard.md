---
title: Como ativar o Monitor Azure para contêineres | Microsoft Docs
description: Este artigo descreve como você habilita e configura o Azure Monitor para contêineres para que você possa entender como seu contêiner está se saindo e quais problemas relacionados ao desempenho foram identificados.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275302"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Como ativar o Monitor Azure para contêineres

Este artigo fornece uma visão geral das opções disponíveis para configurar o Azure Monitor para contêineres para monitorar o desempenho das cargas de trabalho que são implantadas nos ambientes Kubernetes e hospedadas em:

- [Serviço Azure Kubernetes](https://docs.microsoft.com/azure/aks/) (AKS)

- Clusters Kubernetes auto-gerenciados hospedados no Azure usando [o AKS Engine](https://github.com/Azure/aks-engine).

- Clusters Kubernetes auto-gerenciados hospedados no [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) ou no local usando o AKS Engine.

- [Red Hat OpenShift no Azure](../../openshift/intro-openshift.md)

O Monitor Azure para contêineres pode ser habilitado para novas ou mais implantações existentes de Kubernetes usando os seguintes métodos suportados:

- Do portal Azure, Azure PowerShell ou com a Cli azure

- Usando [Terraform e AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem o seguinte:

- **Um espaço de trabalho do Log Analytics.**

    O Azure Monitor para contêineres suporta um espaço de trabalho log analytics nas regiões listadas em Produtos Azure [por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    Você pode criar um espaço de trabalho ao ativar o monitoramento do novo cluster AKS ou permitir que a experiência de onboarding crie um espaço de trabalho padrão no grupo de recursos padrão da assinatura do cluster AKS. Se optar por criá-lo por conta própria, será possível criá-lo por meio do [Azure Resource Manager](../platform/template-workspace-configuration.md), do [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou no [portal do Azure](../learn/quick-create-workspace.md). Para obter uma lista dos pares de mapeamento suportados usados para o espaço de trabalho padrão, consulte [Mapeamento de região para o Monitor Azure para contêineres](container-insights-region-mapping.md).

- Você é um membro da função de contribuinte do Log Analytics para permitir o monitoramento de **contêineres.** Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, veja [Gerenciar espaços de trabalho](../platform/manage-access.md).

- Você é um membro da função **[Proprietário](../../role-based-access-control/built-in-roles.md#owner)** no recurso de cluster AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* As métricas prometheus não são coletadas por padrão. Antes [de configurar o agente](container-insights-prometheus-integration.md) para recolhê-los, é importante que você revise a [documentação](https://prometheus.io/) do Prometheus para entender o que pode ser raspado e os métodos suportados.

## <a name="supported-configurations"></a>Configurações com suporte

O seguinte é oficialmente suportado com o Azure Monitor para contêineres.

- Ambientes: Azure Red Hat OpenShift, Kubernetes no local e AKS Engine no Azure e Azure Stack. Para obter mais informações, consulte [AKS Engine no Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Versões do Kubernetes e política de suporte são as mesmas que versões do [AKS suportado](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Requisitos de firewall de rede

As informações na tabela a seguir listam as informações de configuração de proxy e firewall necessárias para que o agente contêiner se comunique com o Azure Monitor para contêineres. Todo o tráfego de rede do agente está indo para o Azure Monitor.

|Recurso de agente|Portas |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

As informações na tabela a seguir listam as informações de configuração de proxy e firewall para o Azure China.

|Recurso de agente|Portas |Descrição | 
|--------------|------|-------------|
| *.ods.opinsights.azure.cn | 443 | Ingestão de dados |
| *.oms.opinsights.azure.cn | 443 | Onboarding OMS |
| *.blob.core.windows.net | 443 | Usado para monitorar a conectividade de saída. |
| microsoft.com | 80 | Usado para conectividade de rede. Isso só é necessário se a versão da imagem do agente for ciprod09262019 ou anterior. |
| dc.services.visualstudio.com | 443 | Para telemetria de agentes usando a Azure Public Cloud Application Insights. |

As informações na tabela a seguir listam as informações de configuração de proxy e firewall para o Governo dos EUA do Azure.

|Recurso de agente|Portas |Descrição | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Ingestão de dados |
| *.oms.opinsights.azure.us | 443 | Onboarding OMS |
| *.blob.core.windows.net | 443 | Usado para monitorar a conectividade de saída. |
| microsoft.com | 80 | Usado para conectividade de rede. Isso só é necessário se a versão da imagem do agente for ciprod09262019 ou anterior. |
| dc.services.visualstudio.com | 443 | Para telemetria de agentes usando o Azure Public Cloud Application Insights. |

## <a name="components"></a>Componentes

Sua capacidade de monitorar o desempenho depende de um agente Log Analytics contêiner para Linux desenvolvido especificamente para o Azure Monitor para contêineres. Esse agente especializado coleta dados de desempenho e eventos de todos os nós no cluster e é implantado e registrado automaticamente com o espaço de trabalho do Log Analytics especificado durante a implantação. A versão do agente é microsoft/oms:ciprod04202018 ou posterior e é representada por uma data no seguinte formato: *mmddaaaa*.

>[!NOTE]
>Com a versão de visualização do suporte ao Windows Server para AKS, um cluster AKS com nós do Windows Server não tem um agente instalado para coletar dados e encaminhar para o Azure Monitor. Em vez disso, um nó Linux implantado automaticamente no cluster como parte da implantação padrão coleta e encaminha os dados para o Azure Monitor em nome de todos os nós do Windows no cluster.  
>

Quando uma nova versão do agente é lançada, ele é atualizado automaticamente nos clusters gerenciados do Kubernetes hospedados no AKS (Serviço de Kubernetes do Azure). Para seguir as versões lançadas, consulte [comunicados sobre lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Se você já implantou um cluster do AKS, habilite o monitoramento usando a CLI do Azure ou um modelo do Azure Resource Manager fornecido, conforme demonstrado posteriormente neste artigo. Não é possível usar `kubectl` para atualizar, excluir, implantar ou reimplantar o agente.
>O modelo precisa ser implantado no mesmo grupo de recursos que o cluster.

Você habilita o Monitor Azure para contêineres usando um dos seguintes métodos descritos na tabela a seguir.

| Estado de implantação | Método | Descrição |
|------------------|--------|-------------|
| Novo cluster AKS Kubernetes | [Criar cluster AKS usando a Cli do Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Você pode habilitar o monitoramento de um novo cluster AKS que você cria com o Azure CLI. |
| | [Criar cluster AKS usando terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Você pode habilitar o monitoramento de um novo cluster AKS que você cria usando a ferramenta de código aberto Terraform. |
| | [Crie o cluster OpenShift usando um modelo do Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Você pode habilitar o monitoramento de um novo cluster OpenShift que você cria com um modelo de Gerenciador de recursos Azure pré-configurado. |
| | [Crie cluster OpenShift usando o Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Você pode habilitar o monitoramento durante a implantação de um novo cluster OpenShift usando o Azure CLI. |
| Cluster AKS Kubernetes existente | [Habilitar para cluster AKS usando a Cli do Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Você pode habilitar o monitoramento de um cluster AKS já implantado usando o Azure CLI. |
| |[Habilitar para cluster AKS usando terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Você pode habilitar o monitoramento de um cluster AKS já implantado usando a ferramenta de código aberto Terraform. |
| | [Habilitar para cluster AKS do Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Você pode habilitar o monitoramento de um ou mais clusters AKS já implantados a partir da página de vários clusters no Azure Monitor. |
| | [Habilitar a partir do cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Você pode habilitar o monitoramento diretamente de um cluster AKS no portal Azure. |
| | [Habilitar para cluster AKS usando um modelo do Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Você pode habilitar o monitoramento de um cluster AKS com um modelo de Gerenciador de recursos azure pré-configurado. |
| | [Habilitar para cluster Kubernetes híbridos](container-insights-hybrid-setup.md) | Você pode habilitar o monitoramento de um motor AKS hospedado no Azure Stack ou para Kubernetes hospedados no local. |
| | [Habilitar para cluster OpenShift usando um modelo do Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Você pode habilitar o monitoramento de um cluster OpenShift existente com um modelo de Gerenciador de recursos Do Azure pré-configurado. |
| | [Habilitar para cluster OpenShift do Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Você pode habilitar o monitoramento de um ou mais clusters OpenShift já implantados a partir da página de vários clusters no Azure Monitor. |

## <a name="next-steps"></a>Próximas etapas

- Com o monitoramento ativado, você pode começar a analisar o desempenho de seus clusters Kubernetes hospedados no Azure Kubernetes Service (AKS), Azure Stack ou em outro ambiente. Para saber como usar o Azure Monitor para contêineres, consulte [Ver o desempenho do cluster Kubernetes](container-insights-analyze.md).
