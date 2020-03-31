---
title: Configure clusters Azure Red Hat OpenShift com monitor azure para contêineres | Microsoft Docs
description: Este artigo descreve como configurar o monitoramento de um cluster Kubernetes com o Azure Monitor hospedado no Azure Red Hat OpenShift.
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: c2fd3568be2c51296bb1377e91031ebfb7ca6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275510"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Configure clusters Azure Red Hat OpenShift com monitor azure para contêineres

O Azure Monitor para contêineres oferece uma rica experiência de monitoramento para os clusters Azure Kubernetes Service (AKS) e AKS Engine. Este artigo descreve como permitir o monitoramento de clusters Kubernetes hospedados no [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) para obter uma experiência de monitoramento semelhante.

>[!NOTE]
>O suporte ao Azure Red Hat OpenShift é um recurso de pré-visualização pública neste momento.
>

O Monitor Azure para contêineres pode ser habilitado para novas ou mais implantações existentes do Azure Red Hat OpenShift usando os seguintes métodos suportados:

- Para um cluster existente no portal Azure ou usando o modelo do Azure Resource Manager.
- Para um novo cluster usando o modelo do Azure Resource Manager ou enquanto cria um novo cluster usando o [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Recursos suportados e sem suporte

O Azure Monitor para contêineres suporta o monitoramento do Azure Red Hat OpenShift conforme descrito no artigo [Visão Geral,](container-insights-overview.md) exceto para os seguintes recursos:

- Dados ao vivo (visualização)
- [Coletar métricas](container-insights-update-metrics.md) de nós de cluster e pods e armazená-las no banco de dados de métricas do Azure Monitor

## <a name="prerequisites"></a>Pré-requisitos

- Para habilitar e acessar os recursos do Azure Monitor para contêineres, no mínimo você precisa ser um membro da função *Contribuinte* Do Azure na assinatura do Azure e um membro da função De contribuinte do [*Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) do espaço de trabalho Log Analytics configurado com o Monitor Do Azure para contêineres.

- Para visualizar os dados de monitoramento, você é um membro da permissão de função do [*leitor do Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) com o espaço de trabalho Log Analytics configurado com o Azure Monitor para contêineres.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Habilitar para um novo cluster usando um modelo do Azure Resource Manager

Execute as seguintes etapas para implantar um cluster Azure Red Hat OpenShift com o monitoramento ativado. Antes de prosseguir, revise o tutorial [Crie um cluster Azure Red Hat OpenShift](../../openshift/tutorial-create-cluster.md#prerequisites) para entender as dependências que você precisa configurar para que seu ambiente seja configurado corretamente.

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para implantar o cluster com o monitoramento ativado e o outro contém valores de parâmetro que você configura para especificar o seguinte:

- O ID de recurso do cluster Azure Red Hat OpenShift.

- O grupo de recursos em que o cluster é implantado.

- [ID do inquilino do Azure Active Directory](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) anotado após a execução das etapas para criar uma ou uma já criada.

- [ID do aplicativo cliente do Azure Active Directory](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) anotado após a execução das etapas para criar uma ou uma já criada.

- [O segredo do Azure Active Directory Client](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) foi observado após a execução das etapas para criar uma ou uma já criada.

- [O grupo de segurança Azure AD](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) observou após a execução das etapas para criar uma ou uma já criada.

- ID de recursos de um espaço de trabalho do Log Analytics existente.

- O número de nós mestres para criar no cluster.

- O número de nódulos computacionais no perfil do pool de agentes.

- O número de nódulos de infra-estrutura no perfil do pool de agentes.

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:

- [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a versão 2.0.65 do Azure CLI ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

O espaço de trabalho do Log Analytics deve ser criado antes de ativar o monitoramento usando o Azure PowerShell ou o CLI. Para criar o workspace, você pode configurá-lo por meio do [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), por meio do [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou no [portal do Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Baixe e salve em uma pasta local, o modelo e o arquivo parâmetro do Azure Resource Manager, para criar um cluster com o complemento de monitoramento usando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Entrar no Azure

    ```azurecli
    az login    
    ```

    Caso tenha acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.

3. Crie um grupo de recursos para o seu cluster se você ainda não tiver um. Para obter uma lista de regiões do Azure que suporta o OpenShift no Azure, consulte [Regiões suportadas](../../openshift/supported-resources.md#azure-regions).

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Edite o arquivo de parâmetro JSON **newClusterWithMonitoringParam.json** e atualize os seguintes valores:

    - *Localização*
    - *clusterNome*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. A etapa seguinte implanta o cluster com monitoramento ativado usando o Cli do Azure.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    A saída é semelhante ao seguinte:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Habilitar para um cluster existente

Execute as seguintes etapas para habilitar o monitoramento de um cluster Azure Red Hat OpenShift implantado no Azure. Você pode fazer isso a partir do portal Azure ou usando os modelos fornecidos.

### <a name="from-the-azure-portal"></a>No portal do Azure

1. Faça login no [portal Azure](https://portal.azure.com).

2. No menu do portal Azure ou na página inicial, selecione **Azure Monitor**. Na seção **Insights**, selecione **Contêineres**.

3. Na página **Monitor – contêineres**, selecione **Clusters não monitorados**.

4. Na lista de clusters não monitorados, encontre o cluster na lista e clique em **Habilitar**. Você pode identificar os resultados na lista procurando o valor **ARO** a coluna **TIPO CLUSTER**.

5. Na página **Integração do Azure Monitor para contêineres**, se você tiver um espaço de trabalho do Log Analytics existente na mesma assinatura do cluster, selecione-o na lista suspensa.  
    A lista pré-seleciona o espaço de trabalho padrão e o local para o que o cluster está implantado na assinatura.

    ![Habilite o monitoramento de clusters não monitorados](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se você quiser criar um novo espaço de trabalho do Log Analytics para armazenar os dados de monitoramento do cluster, siga as instruções em [Criar um espaço de trabalho do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Certifique-se de criar o espaço de trabalho na mesma assinatura para a qual o cluster RedHat OpenShift está implantado.

Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster.

### <a name="enable-using-an-azure-resource-manager-template"></a>Habilite o uso de um modelo do Azure Resource Manager

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para habilitar o monitoramento e o outro contém valores de parâmetro que você configura para especificar o seguinte:

- O ID de recurso do cluster Azure RedHat OpenShift.

- O grupo de recursos em que o cluster é implantado.

- Um espaço de trabalho do Log Analytics.

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:

- [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a versão 2.0.65 do Azure CLI ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

O espaço de trabalho do Log Analytics deve ser criado antes de ativar o monitoramento usando o Azure PowerShell ou o CLI. Para criar o workspace, você pode configurá-lo por meio do [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), por meio do [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou no [portal do Azure](../../azure-monitor/learn/quick-create-workspace.md).

1. Baixe o arquivo de modelo e parâmetro para atualizar seu cluster com o complemento de monitoramento usando os seguintes comandos:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Entrar no Azure

    ```azurecli
    az login    
    ```

    Caso tenha acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.

3. Especifique a assinatura do cluster Azure RedHat OpenShift.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Execute o seguinte comando para identificar o local do cluster e o ID de recurso:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Editar o arquivo de parâmetro JSON **existenteClusterParam.json** e atualizar os valores *araResourceId* e *araResoruceLocation*. O valor para **workspaceResourceId** é a ID do recurso completa do espaço de trabalho do Log Analytics, que inclui o nome do espaço de trabalho.

6. Para implantar com o Azure CLI, execute os seguintes comandos:

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    A saída é semelhante ao seguinte:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Próximas etapas

- Com o monitoramento habilitado para coletar a utilização de recursos e saúde do cluster RedHat OpenShift e cargas de trabalho em execução neles, aprenda [a usar o](container-insights-analyze.md) Monitor Do Azure para contêineres.

- Para saber como parar de monitorar seu cluster com o Azure Monitor para contêineres, consulte [Como parar de monitorar o cluster Azure Red Hat OpenShift](container-insights-optout-openshift.md).
