---
title: Configurar o Azure Red Hat OpenShift v4. x com Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como configurar o monitoramento de um cluster kubernetes com Azure Monitor hospedado no Azure Red Hat OpenShift versão 4 e superior.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 4b827524845874dabaabe535163d99c408f77a60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196290"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Configurar o Azure Red Hat OpenShift v4. x com Azure Monitor para contêineres

Azure Monitor para contêineres fornece experiência de monitoramento avançada para os clusters do AKS (serviço kubernetes do Azure) e do mecanismo do AKS. Este artigo descreve como habilitar o monitoramento de clusters kubernetes hospedados no [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versão 4. x para obter uma experiência de monitoramento semelhante.

>[!NOTE]
>O suporte para o Azure Red Hat OpenShift é um recurso em visualização pública no momento.
>

Azure Monitor para contêineres podem ser habilitados para uma ou mais implantações existentes do Azure Red Hat OpenShift v4. x usando os seguintes métodos com suporte:

- Para um cluster existente usando o script bash fornecido e em execução no [CLI do Azure](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Recursos com e sem suporte

Azure Monitor para contêineres dá suporte ao monitoramento do Azure Red Hat OpenShift v4. x conforme descrito no artigo de [visão geral](container-insights-overview.md) , com exceção dos seguintes recursos:

- Dados dinâmicos (visualização)
- [Coletar métricas](container-insights-update-metrics.md) de nós de cluster e pods e armazená-los no banco de dados de métricas de Azure monitor

## <a name="prerequisites"></a>Pré-requisitos

- CLI do Azure versão 2.0.72 ou posterior

- [Helm 3](https://helm.sh/docs/intro/install/) Ferramenta CLI

- [Versão 4 do bash](https://www.gnu.org/software/bash/)

- Ferramenta de linha de comando [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Para habilitar e acessar os recursos no Azure Monitor para contêineres, no mínimo você precisa ser um membro da função *colaborador* do Azure na assinatura do Azure e um membro da [*log Analytics função colaborador*](../platform/manage-access.md#manage-access-using-azure-permissions) do espaço de trabalho log Analytics configurado com Azure monitor para contêineres.

- Para exibir os dados de monitoramento, você é um membro da permissão da função [*leitor de log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) com o espaço de trabalho log Analytics configurado com Azure monitor para contêineres.

## <a name="enable-for-an-existing-cluster"></a>Habilitar para um cluster existente

Execute as etapas a seguir para habilitar o monitoramento de um cluster do Azure Red Hat OpenShift versão 4 e superior implantado no Azure usando o script bash fornecido.

1. Entrar no Azure

    ```azurecli
    az login
    ```

2. Baixe e salve o script em uma pasta local que configura o cluster com o complemento de monitoramento usando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. Para identificar o **Kube** do seu cluster, após o êxito `oc login` em seu cluster, execute o comando `kubectl config current-context` e copie o valor.

### <a name="integrate-with-an-existing-workspace"></a>Integrar com um espaço de trabalho existente

A etapa a seguir habilita o monitoramento do cluster usando o script bash baixado anteriormente. Para integrar com um espaço de trabalho Log Analytics existente, execute as etapas a seguir para primeiro identificar a ID de recurso completo do espaço de `workspaceResourceId` trabalho log Analytics necessário para o parâmetro e, em seguida, execute o comando para habilitar o complemento de monitoramento no espaço de trabalho especificado. Se você não tiver um espaço de trabalho para especificar, poderá pular para a etapa 5 e deixar que o script crie um novo espaço de trabalho para você.

1. Liste todas as assinaturas às quais você tem acesso usando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída será semelhante à seguinte:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Copie o valor de **SubscriptionId**.

2. Alterne para a assinatura que hospeda o espaço de trabalho Log Analytics usando o seguinte comando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. O exemplo a seguir exibe a lista de espaços de trabalho em suas assinaturas no formato JSON padrão.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, localize o nome do espaço de trabalho e copie a ID de recurso completo do espaço de trabalho Log Analytics sob a **ID**do campo.

4. Execute o comando a seguir para habilitar o monitoramento, substituindo o `workspaceResourceId` valor do parâmetro: 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <LogAnayticsWorkspaceResourceId>`

    Exemplo:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4  /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster.

### <a name="integrate-with-default-workspace"></a>Integrar com o espaço de trabalho padrão

A etapa a seguir habilita o monitoramento do seu cluster do Azure Red Hat OpenShift v4. x usando o script bash que você baixou. Neste exemplo, não é necessário criar ou especificar um workspace existente. Esse comando simplifica o processo para você criando um espaço de trabalho padrão no grupo de recursos padrão da assinatura do cluster, caso ainda não exista uma na região. O workspace padrão criado é semelhante ao formato do *DefaultWorkspace-\<GUID>-\<Region>*.  

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

    For example:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster.

### <a name="from-the-azure-portal"></a>No portal do Azure

A exibição de vários clusters no Azure Monitor para contêineres realça os clusters do Azure Red Hat OpenShift que não têm o monitoramento habilitado na guia **clusters não monitorados** . A opção **habilitar** ao lado de seu cluster não inicia a integração do monitoramento no Portal. Você será redirecionado para este artigo para habilitar o monitoramento manualmente seguindo as etapas anteriores neste artigo.

1. Entre no [portal do Azure](https://portal.azure.com).

2. No menu portal do Azure ou na home page do, selecione **Azure monitor**. Na seção **Insights**, selecione **Contêineres**.

3. Na página **Monitor – contêineres**, selecione **Clusters não monitorados**.

4. Na lista de clusters não monitorados, localize o cluster na lista e clique em **habilitar**. Você pode identificar os resultados na lista procurando o valor **toa** sob o **tipo de cluster**de coluna. Depois de clicar em **habilitar**, você será redirecionado para este artigo.

## <a name="next-steps"></a>Próximas etapas

- Com o monitoramento habilitado para coletar a utilização de recursos e de integridade do cluster do RedHat OpenShift versão 4. x e das cargas de trabalho em execução neles, saiba [como usar](container-insights-analyze.md) Azure monitor para contêineres.

- Por padrão, o agente em contêiner coleta os logs de contêiner stdout/stderr de todos os contêineres em execução em todos os namespaces, exceto Kube-System. Para configurar a coleta de log de contêiner específica para namespaces ou namespaces específicos, examine [configuração do agente do insights de contêiner](container-insights-agent-config.md) para definir as configurações de coleta de dados desejadas para o arquivo de configurações do ConfigMap.

- Para revisar e analisar as métricas de Prometheus do seu cluster, examine [Configurar a recorte de métricas do Prometheus](container-insights-prometheus-integration.md)

- Para saber como parar de monitorar o cluster com Azure Monitor para contêineres, consulte [como parar de monitorar o cluster do Azure Red Hat OpenShift](container-insights-optout-openshift.md).
