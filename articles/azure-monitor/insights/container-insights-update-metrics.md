---
title: Como atualizar Azure Monitor para contêineres para métricas | Microsoft Docs
description: Este artigo descreve como você atualiza Azure Monitor para contêineres para habilitar o recurso de métricas personalizadas que dá suporte à exploração e aos alertas em métricas agregadas.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: d299fc5e6b0c41188fac1fa19bb66387263c12e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84298254"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>Como atualizar o Azure Monitor para contêineres para habilitar as métricas

Azure Monitor para contêineres está introduzindo o suporte para coletar métricas de nós de clusters dos AKS (serviços Kubernetess do Azure) e pods e gravá-los no repositório de métricas de Azure Monitor. Essa alteração destina-se a fornecer uma linha de tempo aprimorada ao apresentar cálculos agregados (AVG, Count, Max, min, Sum) em gráficos de desempenho, dar suporte à fixação de gráficos de desempenho em painéis de portal do Azure e dar suporte a alertas de métricas.

>[!NOTE]
>Atualmente, esse recurso não dá suporte a clusters do Azure Red Hat OpenShift.
>

As seguintes métricas são habilitadas como parte desse recurso:

| Namespace da métrica | Métrica | Descrição |
|------------------|--------|-------------|
| informações. contêiner/nós | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount | Essas são as métricas de *nó* e incluem *host* como uma dimensão e também incluem o<br> nome do nó como valor para a dimensão do *host* . |
| percepções. Container/pods | podCount | Essas são métricas de *Pod* e incluem as seguintes como dimensões-ControllerName, namespace kubernetes, nome, fase. |

A atualização do cluster para dar suporte a esses novos recursos pode ser executada no portal do Azure, Azure PowerShell ou com CLI do Azure. Com Azure PowerShell e CLI, você pode habilitar esse por cluster ou para todos os clusters em sua assinatura. As novas implantações do AKS incluirão automaticamente essa alteração de configuração e os recursos.

Qualquer processo atribui a função de **Editor de métricas de monitoramento** à entidade de serviço do cluster ou à MSI atribuída pelo usuário para o complemento de monitoramento para que os dados coletados pelo agente possam ser publicados no recurso de clusters. O monitoramento de métricas Publisher tem permissão apenas para enviar métricas por push para o recurso, ele não pode alterar nenhum estado, atualizar o recurso ou ler dados. Para obter mais informações sobre a função, consulte [monitorando métricas de função de editor](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme o seguinte:

* As métricas personalizadas só estão disponíveis em um subconjunto de regiões do Azure. Uma lista de regiões com suporte está documentada [aqui](../platform/metrics-custom-overview.md#supported-regions).
* Você é membro da função **[proprietário](../../role-based-access-control/built-in-roles.md#owner)** no recurso de cluster AKs para habilitar a coleta de métricas de desempenho personalizadas de nó e Pod. 

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.59 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Atualizar um cluster do portal do Azure

Para clusters AKS existentes monitorados por Azure Monitor para contêineres, depois de selecionar o cluster para exibir sua integridade do modo de exibição de vários clusters no Azure Monitor ou diretamente do cluster, selecionando **insights** no painel esquerdo, você verá uma faixa na parte superior do Portal.

![Atualizar a faixa do cluster AKS no portal do Azure](./media/container-insights-update-metrics/portal-banner-enable-01.png)

Clicar em **habilitar** iniciará o processo para atualizar o cluster. Esse processo pode levar vários segundos para ser finalizado e você pode acompanhar o progresso em Notificações no menu.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Atualizar todos os clusters usando o bash no Shell de comando do Azure

Execute as etapas a seguir para atualizar todos os clusters em sua assinatura usando o bash no Shell de comando do Azure.

1. Execute o comando a seguir usando o CLI do Azure.  Edite o valor de **SubscriptionId** usando o valor da página **visão geral do AKS** para o cluster AKs.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    A alteração de configuração pode levar alguns segundos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Atualizar por cluster usando CLI do Azure

Execute as etapas a seguir para atualizar um cluster específico em sua assinatura usando CLI do Azure.

1. Execute o comando a seguir usando o CLI do Azure. Edite os valores para **SubscriptionId**, **resourceGroupName**e **ClusterName** usando os valores na página **visão geral do AKS** para o cluster AKs.  Para obter o valor de **clientIdOfSPN**, ele é retornado quando você executa o comando, `az aks show` conforme mostrado no exemplo abaixo.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    Para obter o valor de **clientIdOfSPNOrMsi**, você pode executar o comando, `az aks show` conforme mostrado no exemplo abaixo. Se o objeto **servicePrincipalProfile** tiver um valor *ClientID* válido, você poderá usá-lo. Caso contrário, se estiver definido como *MSI*, você precisará passar o ClientID de `addonProfiles.omsagent.identity.clientId` .

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Atualizar todos os clusters usando Azure PowerShell

Execute as etapas a seguir para atualizar todos os clusters em sua assinatura usando Azure PowerShell.

1. [Baixe](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) o script de **mdm_onboarding_atscale.ps1** e salve-o em uma pasta local de nosso repositório github.
2. Execute o comando a seguir usando o Azure PowerShell.  Edite o valor de **SubscriptionId** usando o valor da página **visão geral do AKS** para o cluster AKs.

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    A alteração de configuração pode levar alguns segundos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Atualizar por cluster usando Azure PowerShell

Execute as etapas a seguir para atualizar um cluster específico usando Azure PowerShell.

1. [Baixe](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) o script de **mdm_onboarding.ps1** e salve-o em uma pasta local de nosso repositório github.

2. Execute o comando a seguir usando o Azure PowerShell. Edite os valores para **SubscriptionId**, **resourceGroupName**e **ClusterName** usando os valores na página **visão geral do AKS** para o cluster AKs.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    A alteração de configuração pode levar alguns segundos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Verificar atualização

Depois de iniciar a atualização usando um dos métodos descritos anteriormente, você pode usar Azure Monitor métricas Explorer e verificar no **namespace de métrica** que o **insights** está listado. Se for, isso indicará que você pode começar a configurar alertas de [métrica](../platform/alerts-metric.md) ou fixar seus gráficos nos [painéis](../../azure-portal/azure-portal-dashboards.md).  
