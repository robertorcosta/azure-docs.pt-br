---
title: Experiências e inferências seguras na rede virtual
titleSuffix: Azure Machine Learning
description: Saiba como proteger trabalhos de experimentação/treinamento e trabalhos de inferência/pontuação no Azure Machine Learning em uma rede virtual do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 04/17/2020
ms.openlocfilehash: 6cf89790ee125d8d09d9bdead2f6e34dcb73e8f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188116"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Proteger trabalhos de experimentação e de inferência do Azure ML em uma rede virtual do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a proteger trabalhos de experimentação/treinamento e trabalhos de inferência/pontuação no Azure Machine Learning em uma vnet (rede virtual) do Azure.

Uma **rede virtual** age como um limite de segurança, isolando os recursos do Azure da Internet pública. Além disso, é possível ingressar em uma rede virtual do Azure na rede local. Ao ingressar em redes, você pode treinar seus modelos com segurança e acessar seus modelos implantados para inferência.

Azure Machine Learning se baseia em outros serviços do Azure para recursos de computação. Os recursos de computação ou os [destinos de computação](concept-compute-target.md)são usados para treinar e implantar modelos. Os destinos podem ser criados em uma rede virtual. Por exemplo, você pode usar o Microsoft Máquina Virtual de Ciência de Dados para treinar um modelo e, em seguida, implantar o modelo no AKS (serviço kubernetes do Azure). Para obter mais informações sobre redes virtuais, consulte [visão geral da rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Este artigo também fornece informações detalhadas sobre *configurações de segurança avançadas*, informações que não são necessárias para casos de uso básico ou experimental. Determinadas seções deste artigo fornecem informações de configuração para uma variedade de cenários. Você não precisa concluir as instruções em ordem ou em sua totalidade.

> [!TIP]
> A menos que especificamente chamado, o uso de recursos como contas de armazenamento ou destinos de computação dentro de uma rede virtual funcionará com pipelines de aprendizado de máquina e fluxos de trabalho sem pipeline, como execuções de script.

> [!WARNING]
> A Microsoft não oferece suporte ao uso dos recursos de Azure Machine Learning Studio como ML, conjuntos de data, datarotulamento, designer e blocos de anotações automatizados se o armazenamento subjacente tiver uma rede virtual habilitada.

## <a name="prerequisites"></a>Pré-requisitos

+ Um [espaço de trabalho](how-to-manage-workspace.md)Azure Machine Learning.

+ Conhecimento geral de trabalho do [serviço de rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e da [rede IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Uma rede virtual e sub-rede pré-existentes para usar com seus recursos de computação.

## <a name="use-a-storage-account-for-your-workspace"></a>Usar uma conta de armazenamento para seu espaço de trabalho

> [!WARNING]
> Se você tiver cientistas de dados que usam o designer de Azure Machine Learning, eles receberão um erro ao Visualizar dados de uma conta de armazenamento dentro de uma rede virtual. O texto a seguir é o erro que recebe:
>
> __Erro: não é possível criar o perfil deste DataSet. Isso pode ocorrer porque seus dados são armazenados atrás de uma rede virtual ou seus dados não dão suporte ao perfil.__

Para usar uma conta de armazenamento do Azure para o espaço de trabalho em uma rede virtual, use as seguintes etapas:

1. Crie um recurso de computação (por exemplo, um Machine Learning cluster ou instância de computação) atrás de uma rede virtual ou anexe um recurso de computação ao espaço de trabalho (por exemplo, um cluster HDInsight, uma máquina virtual ou um cluster do serviço kubernetes do Azure). O recurso de computação pode ser para a experimentação ou implantação de modelo.

   Para obter mais informações, consulte as seções [usar uma Machine Learning computação](#amlcompute), [usar uma máquina virtual ou um cluster HDInsight](#vmorhdi)e [usar o serviço kubernetes do Azure](#aksvnet) neste artigo.

1. No portal do Azure, vá para o armazenamento que está anexado ao seu espaço de trabalho.

   [![O armazenamento que está anexado ao espaço de trabalho Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na página **armazenamento do Azure** , selecione __firewalls e redes virtuais__.

   ![A área "firewalls e redes virtuais" na página de armazenamento do Azure no portal do Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na página __firewalls e redes virtuais__ , execute as seguintes ações:
    - Selecione __Redes selecionadas__.
    - Em __redes virtuais__, selecione o link __Adicionar rede virtual existente__ . Essa ação adiciona a rede virtual onde sua computação reside (consulte a etapa 1).

        > [!IMPORTANT]
        > A conta de armazenamento deve estar na mesma rede virtual e sub-rede que as instâncias de computação ou clusters usados para treinamento ou inferência.

    - Marque a caixa de seleção __permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento__ .

    > [!IMPORTANT]
    > Ao trabalhar com o SDK do Azure Machine Learning, seu ambiente de desenvolvimento deve ser capaz de se conectar à conta de armazenamento do Azure. Quando a conta de armazenamento está dentro de uma rede virtual, o firewall deve permitir o acesso do endereço IP do ambiente de desenvolvimento.
    >
    > Para habilitar o acesso à conta de armazenamento, visite __firewalls e redes virtuais__ para a conta de armazenamento em *um navegador da Web no cliente de desenvolvimento*. Em seguida, use a caixa de seleção __Adicionar seu endereço IP do cliente__ para adicionar o endereço IP do cliente ao __intervalo de endereços__. Você também pode usar o campo __intervalo de endereços__ para inserir manualmente o endereço IP do ambiente de desenvolvimento. Depois que o endereço IP do cliente tiver sido adicionado, ele poderá acessar a conta de armazenamento usando o SDK.

   [![O painel "firewalls e redes virtuais" no portal do Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Você pode posicionar a _conta de armazenamento padrão_ para Azure Machine Learning ou _contas de armazenamento não padrão_ em uma rede virtual.
>
> A conta de armazenamento padrão é provisionada automaticamente quando você cria um espaço de trabalho.
>
> Para contas de armazenamento não padrão, o `storage_account` parâmetro na [ `Workspace.create()` função](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) permite que você especifique uma conta de armazenamento personalizada pela ID de recurso do Azure.

## <a name="use-azure-data-lake-storage-gen-2"></a>Usar Azure Data Lake Storage Gen 2

O Azure Data Lake Storage Gen 2 é um conjunto de recursos para Big Data Analytics, criado com base no armazenamento de BLOBs do Azure. Ele pode ser usado para armazenar dados usados para treinar modelos com Azure Machine Learning. 

Para usar Data Lake Storage Gen 2 dentro da rede virtual do seu espaço de trabalho Azure Machine Learning, use as seguintes etapas:

1. Crie uma conta Azure Data Lake Storage Gen 2. Para obter mais informações, consulte [criar uma conta de armazenamento Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Use as etapas 2-4 na seção anterior, [use uma conta de armazenamento para seu espaço de trabalho](#use-a-storage-account-for-your-workspace), para colocar a conta na rede virtual.

Ao usar Azure Machine Learning com Data Lake Storage Gen 2 dentro de uma rede virtual, use as seguintes diretrizes:

* Se você usar o __SDK para criar um conjunto de um__e o sistema que executa o código __não estiver na rede virtual__, use `validate=False` o parâmetro. Esse parâmetro ignora a validação, o que falhará se o sistema não estiver na mesma rede virtual que a conta de armazenamento. Para obter mais informações, consulte o método [from_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) .

* Ao usar Azure Machine Learning instância de computação ou cluster de computação para treinar um modelo usando o conjunto de um, ele deve estar na mesma rede virtual que a conta de armazenamento.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Usar uma instância do Key Vault com seu espaço de trabalho

A instância do cofre de chaves associada ao espaço de trabalho é usada pelo Azure Machine Learning para armazenar as seguintes credenciais:
* A cadeia de conexão da conta de armazenamento associada
* Senhas para instâncias do repositório de contêiner do Azure
* Cadeias de conexão para armazenamentos de dados

Para usar Azure Machine Learning recursos de experimentação com Azure Key Vault por trás de uma rede virtual, use as seguintes etapas:

1. Vá para o cofre de chaves associado ao espaço de trabalho.

   [![O cofre de chaves associado ao espaço de trabalho Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Na página **Key Vault** , no painel esquerdo, selecione __firewalls e redes virtuais__.

   ![A seção "firewalls e redes virtuais" no painel de Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na página __firewalls e redes virtuais__ , execute as seguintes ações:
    - Em __Permitir acesso de__, clique em __Redes selecionadas__.
    - Em __redes virtuais__, selecione __Adicionar redes virtuais existentes__ para adicionar a rede virtual em que a computação de experimentação reside.
    - Em __permitir que os serviços confiáveis da Microsoft ignorem esse firewall__, selecione __Sim__.

   [![A seção "firewalls e redes virtuais" no painel de Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="use-a-machine-learning-compute"></a><a name="compute-instance"></a>Usar um Computação do Machine Learning

Para usar uma instância de computação ou cluster de computação Azure Machine Learning em uma rede virtual, os requisitos de rede a seguir devem ser atendidos:

> [!div class="checklist"]
> * A rede virtual deve estar na mesma assinatura e região que o espaço de trabalho Azure Machine Learning.
> * A sub-rede especificada para a instância ou cluster de computação deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs que são direcionadas. Se a sub-rede não tiver endereços IP não atribuídos suficientes, um cluster de computação será parcialmente alocado.
> * Verifique se as políticas de segurança ou os bloqueios na assinatura ou no grupo de recursos da rede virtual restringem as permissões para gerenciar a rede virtual. Se você planeja proteger a rede virtual restringindo o tráfego, deixe algumas portas abertas para o serviço de computação. Para obter mais informações, consulte a seção [portas necessárias](#mlcports) .
> * Se você pretende colocar várias instâncias de computação ou clusters em uma rede virtual, talvez seja necessário solicitar um aumento de cota para um ou mais dos seus recursos.
> * Se as contas de armazenamento do Azure para o espaço de trabalho também estiverem protegidas em uma rede virtual, elas deverão estar na mesma rede virtual que a instância ou cluster de computação Azure Machine Learning. 

> [!TIP]
> A instância ou cluster de computação Machine Learning aloca automaticamente recursos de rede adicionais __no grupo de recursos que contém a rede virtual__. Para cada instância ou cluster de computação, o serviço aloca os seguintes recursos:
> 
> * Um grupo de segurança de rede
> * Um endereço IP público
> * Um balanceador de carga
> 
> No caso de clusters, esses recursos são excluídos (e recriados) sempre que o cluster é reduzido para 0 nós, no entanto, para uma instância, os recursos são mantidos até que a instância seja completamente excluída (a interrupção não remove os recursos). 
> Esses recursos são limitados pelas [cotas de recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) da assinatura.


### <a name="required-ports"></a><a id="mlcports"></a> Portas obrigatórias

Atualmente, a Computação do Machine Learning usa o serviço de Lote do Azure para provisionar VMs na rede virtual especificada. A sub-rede deve permitir a comunicação de entrada do serviço Lote. Você usa essa comunicação para agendar execuções nos nós de Computação do Machine Learning e para se comunicar com o armazenamento do Azure e outros recursos. O serviço de lote adiciona NSGs (grupos de segurança de rede) no nível de adaptadores de rede (NICs) anexados às VMs. Esses NSGs configuraram automaticamente as regras de entrada e saída para permitir o tráfego a seguir:

- Tráfego TCP de entrada nas portas 29876 e 29877 de uma __marca de serviço__ de __BatchNodeManagement__.

    ![Uma regra de entrada que usa a marca de serviço BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Adicional Tráfego TCP de entrada na porta 22 para permitir o acesso remoto. Use essa porta somente se você quiser se conectar usando SSH no IP público.

- Tráfego de saída em qualquer porta para a rede virtual.

- Tráfego de saída em qualquer porta para a internet.

- Para o tráfego TCP de entrada da instância de computação na porta 44224 de uma __marca de serviço__ de __AzureMachineLearning__.

Tenha cuidado se você modificar ou adicionar regras de entrada ou saída nos NSGs configurados em Lote. Se um NSG bloquear a comunicação com os nós de computação, o serviço de computação definirá o estado dos nós de computação para inutilizável.

Você não precisa especificar NSGs no nível de sub-rede, pois o serviço de lote do Azure configura seu próprio NSGs. No entanto, se a sub-rede especificada tiver NSGs associado ou um firewall, configure as regras de segurança de entrada e saída, conforme mencionado anteriormente.

A configuração da regra NSG na portal do Azure é mostrada nas seguintes imagens:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="As regras de NSG de entrada para Computação do Machine Learning" border="true":::



![As regras NSG de saída para Computação do Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>Limitar a conectividade de saída da rede virtual

Se você não quiser usar as regras de saída padrão e quiser limitar o acesso de saída de sua rede virtual, use as seguintes etapas:

- Negue a conexão de Internet de saída usando as regras NSG.

- Para uma __instância de computação__ ou um __cluster de computação__, limite o tráfego de saída para os seguintes itens:
   - Armazenamento do Azure, usando a __marca de serviço__ de __Storage. RegionName__. Em `{RegionName}` que é o nome de uma região do Azure.
   - Registro de contêiner do Azure, usando a __marca de serviço__ de __AzureContainerRegistry. RegionName__. Em `{RegionName}` que é o nome de uma região do Azure.
   - Azure Machine Learning, usando a __marca de serviço__ de __AzureMachineLearning__
   - Azure Resource Manager, usando a __marca de serviço__ de __AzureResourceManager__
   - Azure Active Directory, usando a __marca de serviço__ de __AzureActiveDirectory__

A configuração da regra NSG na portal do Azure é mostrada na imagem a seguir:

[![As regras NSG de saída para Computação do Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Se você planeja usar imagens padrão do Docker fornecidas pela Microsoft e habilitando dependências gerenciadas pelo usuário, você também deve usar uma __marca de serviço__ de __MicrosoftContainerRegistry. Region_Name__ (por exemplo, MicrosoftContainerRegistry. eastus).
>
> Essa configuração é necessária quando você tem um código semelhante aos trechos a seguir como parte de seus scripts de treinamento:
>
> __Treinamento de RunConfig__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Treinamento de estimador__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Rotas definidas pelo usuário para o túnel forçado

Se você estiver usando o túnel forçado com o Computação do Machine Learning, adicione [rotas definidas pelo usuário (UDRs)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) à sub-rede que contém o recurso de computação.

* Estabeleça um UDR para cada endereço IP usado pelo serviço de lote do Azure na região onde os recursos existem. Esses UDRs permitem que o serviço de lote se comunique com nós de computação para o agendamento de tarefas. Além disso, adicione o endereço IP para o serviço de Azure Machine Learning em que os recursos existem, pois isso é necessário para acessar instâncias de computação. Para obter uma lista de endereços IP do serviço de lote e do serviço de Azure Machine Learning, use um dos seguintes métodos:

    * Baixe os [intervalos de IP e as marcas de serviço do Azure](https://www.microsoft.com/download/details.aspx?id=56519) e `BatchNodeManagement.<region>` pesquise `AzureMachineLearning.<region>`o arquivo `<region>` e, em que é sua região do Azure.

    * Use o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para baixar as informações. O exemplo a seguir baixa as informações de endereço IP e filtra as informações para a região leste dos EUA 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

* O tráfego de saída para o armazenamento do Azure não deve ser bloqueado pelo dispositivo de rede local. Especificamente, as URLs estão no formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`e. `<account>.blob.core.windows.net`

Ao adicionar o UDRs, defina a rota para cada prefixo de endereço IP do lote relacionado e defina o __tipo do próximo salto__ como __Internet__. A imagem a seguir mostra um exemplo desse UDR no portal do Azure:

![Exemplo de um UDR para um prefixo de endereço](./media/how-to-enable-virtual-network/user-defined-route.png)

Para obter mais informações, consulte [criar um pool do lote do Azure em uma rede virtual](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Criar um cluster de computação em uma rede virtual

Para criar um cluster Computação do Machine Learning, use as seguintes etapas:

1. Entre no [Azure Machine Learning Studio](https://ml.azure.com/)e, em seguida, selecione sua assinatura e o espaço de trabalho.

1. Selecione __Computação__ à esquerda.

1. Selecione __clusters de treinamento__ no centro e selecione __+__.

1. Na caixa de diálogo __novo cluster de treinamento__ , expanda a seção __Configurações avançadas__ .

1. Para configurar esse recurso de computação para usar uma rede virtual, execute as seguintes ações na seção __Configurar rede virtual__ :

    1. Na lista suspensa __grupo de recursos__ , selecione o grupo de recursos que contém a rede virtual.
    1. Na lista suspensa __rede virtual__ , selecione a rede virtual que contém a sub-rede.
    1. Na lista suspensa __sub-rede__ , selecione a sub-rede a ser usada.

   ![As configurações de rede virtual para Computação do Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Também é possível criar um cluster de Computação do Machine Learning, usando o SDK do Azure Machine Learning. O código a seguir cria um novo cluster de Computação do Machine Learning na sub-rede `default` de uma rede virtual chamada `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Quando o processo de criação for concluído, você treinará seu modelo usando o cluster em um experimento. Para obter mais informações, consulte [Selecionar e usar um destino de computação para treinamento](how-to-set-up-training-targets.md).

## <a name="use-azure-databricks"></a>Usar Azure Databricks

Para usar Azure Databricks em uma rede virtual com seu espaço de trabalho, os seguintes requisitos devem ser atendidos:

> [!div class="checklist"]
> * A rede virtual deve estar na mesma assinatura e região que o espaço de trabalho Azure Machine Learning.
> * Se as contas de armazenamento do Azure para o espaço de trabalho também estiverem protegidas em uma rede virtual, elas deverão estar na mesma rede virtual que o cluster de Azure Databricks.
> * Além do __databricks – Private__ e __databricks-sub-redes públicas__ usadas pelo Azure Databricks, a sub-rede __padrão__ criada para a rede virtual também é necessária.

Para obter informações específicas sobre como usar Azure Databricks com uma rede virtual, consulte [implantar Azure Databricks em sua rede virtual do Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Usar uma máquina virtual ou um cluster HDInsight

> [!IMPORTANT]
> Azure Machine Learning dá suporte apenas a máquinas virtuais que executam o Ubuntu.

Para usar uma máquina virtual ou um cluster do Azure HDInsight em uma rede virtual com seu espaço de trabalho, use as seguintes etapas:

1. Crie uma VM ou um cluster HDInsight usando o portal do Azure ou o CLI do Azure e coloque o cluster em uma rede virtual do Azure. Para obter mais informações, consulte os seguintes artigos:
    * [Criar e gerenciar redes virtuais do Azure para VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estender HDInsight usando uma rede virtual do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Para permitir que Azure Machine Learning se comunique com a porta SSH na VM ou cluster, configure uma entrada de origem para o grupo de segurança de rede. A porta SSH geralmente é a porta 22. Para permitir o tráfego dessa fonte, execute as seguintes ações:

    * Na lista suspensa __origem__ , selecione __marca de serviço__.

    * Na lista suspensa __marca de serviço de origem__ , selecione __AzureMachineLearning__.

    * Na lista suspensa __intervalos de portas de origem__ , selecione. __*__

    * Na lista suspensa __destino__ , selecione __qualquer__.

    * Na lista suspensa __intervalos de portas de destino__ , selecione __22__.

    * Em __protocolo__, selecione __qualquer__.

    * Em __ação__, selecione __permitir__.

   ![Regras de entrada para fazer experimentações em uma VM ou cluster HDInsight em uma rede virtual](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Mantenha as regras de saída padrão para o grupo de segurança de rede. Para obter mais informações, consulte as regras de segurança padrão em [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Se você não quiser usar as regras de saída padrão e quiser limitar o acesso de saída de sua rede virtual, consulte a seção [limitar a conectividade de saída da rede virtual](#limiting-outbound-from-vnet) .

1. Anexe a VM ou o cluster HDInsight ao seu espaço de trabalho Azure Machine Learning. Para obter mais informações, consulte [Configurar destinos de computação para treinamento do modelo](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Usar o Serviço de Kubernetes do Azure (AKS)

Para adicionar o AKS em uma rede virtual ao seu espaço de trabalho, use as seguintes etapas:

> [!IMPORTANT]
> Antes de começar o procedimento a seguir, siga os pré-requisitos no como [Configurar a rede avançada no AKs (serviço kubernetes do Azure)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) e planejar o endereçamento IP para o cluster.
>
> A instância AKS e a rede virtual do Azure devem estar na mesma região. Se você proteger as contas de armazenamento do Azure usadas pelo espaço de trabalho em uma rede virtual, elas deverão estar na mesma rede virtual que a instância AKS.

> [!WARNING]
> Azure Machine Learning não dá suporte ao uso de um serviço kubernetes do Azure que tenha o link privado habilitado.

1. Entre no [Azure Machine Learning Studio](https://ml.azure.com/)e, em seguida, selecione sua assinatura e o espaço de trabalho.

1. Selecione __Computação__ à esquerda.

1. Selecione __clusters de inferência__ no centro e, em __+__ seguida, selecione.

1. Na caixa de diálogo __novo cluster de inferência__ , selecione __avançado__ em __configuração de rede__.

1. Para configurar esse recurso de computação para usar uma rede virtual, execute as seguintes ações:

    1. Na lista suspensa __grupo de recursos__ , selecione o grupo de recursos que contém a rede virtual.
    1. Na lista suspensa __rede virtual__ , selecione a rede virtual que contém a sub-rede.
    1. Na lista suspensa __sub-rede__ , selecione a sub-rede.
    1. Na caixa __intervalo de endereços de serviço do kubernetes__ , insira o intervalo de endereços do serviço kubernetes. Esse intervalo de endereços usa um intervalo de IP de notação CIDR (roteamento entre domínios sem classificação) para definir os endereços IP que estão disponíveis para o cluster. Ele não deve se sobrepor a intervalos de IP de sub-rede (por exemplo, 10.0.0.0/16).
    1. Na caixa __endereço IP do serviço DNS do kubernetes__ , insira o endereço IP do serviço DNS do kubernetes. Esse endereço IP é atribuído ao serviço DNS do Kubernetes. Ele deve estar dentro do intervalo de endereços do serviço kubernetes (por exemplo, 10.0.0.10).
    1. Na caixa __endereço do Docker Bridge__ , insira o endereço da ponte do Docker. Esse endereço IP é atribuído à ponte Docker. Ele não deve estar em nenhum intervalo de IP de sub-rede ou no kubernetes de endereço de serviço (por exemplo, 172.17.0.1/16).

   ![Azure Machine Learning: Computação do Machine Learning configurações de rede virtual](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Verifique se o grupo NSG que controla a rede virtual tem uma regra de segurança de entrada habilitada para o ponto de extremidade de Pontuação para que ela possa ser chamada de fora da rede virtual.
   > [!IMPORTANT]
   > Mantenha as regras de saída padrão para o NSG. Para obter mais informações, consulte as regras de segurança padrão em [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Uma regra de segurança de entrada](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Também é possível usar o SDK do Azure Machine Learning para adicionar o Serviço de Kubernetes do Azure a uma rede virtual. Se você já tiver um cluster AKS em uma rede virtual, anexe-o ao espaço de trabalho conforme descrito em [como implantar no AKs](how-to-deploy-and-where.md). O código a seguir cria uma nova instância AKS na `default` sub-rede de uma rede virtual chamada `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Quando o processo de criação for concluído, você poderá executar a inferência ou a pontuação de modelo em um cluster AKS por trás de uma rede virtual. Para obter mais informações, consulte [Como implantar no AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Usar IPs privados com o serviço kubernetes do Azure

Por padrão, um endereço IP público é atribuído a implantações do AKS. Ao usar o AKS dentro de uma rede virtual, você pode usar um endereço IP privado em vez disso. Os endereços IP privados só podem ser acessados de dentro da rede virtual ou de redes Unidas.

Um endereço IP privado é habilitado Configurando AKS para usar um _balanceador de carga interno_. 

> [!IMPORTANT]
> Você não pode habilitar o IP privado ao criar o cluster do serviço kubernetes do Azure. Ele deve ser habilitado como uma atualização para um cluster existente.

O trecho de código a seguir demonstra como **criar um novo cluster AKs**e, em seguida, atualizá-lo para usar um balanceador de carga interno/IP privado:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__CLI do Azure__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

O conteúdo do `body.json` arquivo referenciado pelo comando é semelhante ao seguinte documento JSON:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> No momento, não é possível configurar o balanceador de carga ao executar uma operação de __anexação__ em um cluster existente. Primeiro, você deve anexar o cluster e, em seguida, executar uma operação de atualização para alterar o balanceador de carga.

Para obter mais informações sobre como usar o balanceador de carga interno com o AKS, consulte [usar o balanceador de carga interno com o serviço kubernetes do Azure](/azure/aks/internal-lb).

## <a name="use-azure-container-instances-aci"></a>Usar ACI (instâncias de contêiner do Azure)

As instâncias de contêiner do Azure são criadas dinamicamente durante a implantação de um modelo. Para habilitar a Azure Machine Learning para criar ACI dentro da rede virtual, você deve habilitar a __delegação de sub-rede__ para a sub-rede usada pela implantação.

Para usar o ACI em uma rede virtual para seu espaço de trabalho, use as seguintes etapas:

1. Para habilitar a delegação de sub-rede em sua rede virtual, use as informações no artigo [Adicionar ou remover uma delegação de sub-rede](../virtual-network/manage-subnet-delegation.md) . Você pode habilitar a delegação ao criar uma rede virtual ou adicioná-la a uma rede existente.

    > [!IMPORTANT]
    > Ao habilitar a delegação, `Microsoft.ContainerInstance/containerGroups` use como o valor da __sub-rede delegar para o serviço__ .

2. Implante o modelo usando [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), use os `vnet_name` parâmetros `subnet_name` e. Defina esses parâmetros como o nome da rede virtual e a sub-rede em que você habilitou a delegação.



## <a name="use-azure-firewall"></a>Usar o Firewall do Azure

Para obter informações sobre como usar Azure Machine Learning com o Firewall do Azure, consulte [usar o espaço de trabalho Azure Machine Learning por trás do firewall do Azure](how-to-access-azureml-behind-firewall.md).

## <a name="use-azure-container-registry"></a>Usar o Registro de Contêiner do Azure

> [!IMPORTANT]
> O ACR (registro de contêiner do Azure) pode ser colocado dentro de uma rede virtual, no entanto, você deve atender aos seguintes pré-requisitos:
>
> * Seu espaço de trabalho do Azure Machine Learning deve ser Enterprise Edition. Para obter informações sobre como atualizar, consulte [atualizar para o Enterprise Edition](how-to-manage-workspace.md#upgrade).
> * O registro de contêiner do Azure deve ser uma versão Premium. Para obter mais informações sobre como atualizar, consulte [alterando SKUs](/azure/container-registry/container-registry-skus#changing-skus).
> * O registro de contêiner do Azure deve estar na mesma rede virtual e sub-rede que a conta de armazenamento e os destinos de computação usados para treinamento ou inferência.
> * Seu espaço de trabalho do Azure Machine Learning deve conter um [cluster de computação Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute).
>
>     Quando o ACR está atrás de uma rede virtual, Azure Machine Learning não pode usá-lo para criar diretamente imagens do Docker. Em vez disso, o cluster de computação é usado para criar as imagens.

1. Para localizar o nome do registro de contêiner do Azure para seu espaço de trabalho, use um dos seguintes métodos:

    __Azure portal__

    Na seção visão geral do seu espaço de trabalho, o valor __do registro__ é vinculado ao registro de contêiner do Azure.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Registro de contêiner do Azure para o espaço de trabalho" border="true":::

    __CLI do Azure__

    Se você [instalou a extensão de Machine Learning para CLI do Azure](reference-azure-machine-learning-cli.md), poderá usar o `az ml workspace show` comando para mostrar as informações do espaço de trabalho.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Esse comando retorna um valor semelhante a `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. A última parte da cadeia de caracteres é o nome do registro de contêiner do Azure para o espaço de trabalho.

1. Para limitar o acesso à sua rede virtual, use as etapas em [Configurar o acesso à rede para o registro](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Ao adicionar a rede virtual, selecione a rede virtual e a sub-rede para seus recursos de Azure Machine Learning.

1. Use o SDK do Python Azure Machine Learning para configurar um cluster de cálculo para criar imagens do Docker. O trecho de código a seguir demonstra como fazer isso:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Sua conta de armazenamento, cluster de computação e registro de contêiner do Azure devem estar na mesma sub-rede da rede virtual.
    
    Para obter mais informações, consulte a referência do método [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) .

1. Se você estiver usando o link privado para seu espaço de trabalho Azure Machine Learning e colocar o registro de contêiner do Azure para seu espaço de trabalho em uma rede virtual, também deverá aplicar o seguinte modelo de Azure Resource Manager. Este modelo permite que seu espaço de trabalho se comunique com o ACR por meio do link privado.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="next-steps"></a>Próximas etapas

* [Configurar ambientes de treinamento](how-to-set-up-training-targets.md)
* [Onde implantar modelos](how-to-deploy-and-where.md)
* [Usar o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)
