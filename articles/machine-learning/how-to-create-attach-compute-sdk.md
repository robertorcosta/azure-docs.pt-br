---
title: Criar treinamento & implantar computações (Python)
titleSuffix: Azure Machine Learning
description: Use o SDK do Python Azure Machine Learning para criar recursos de computação de treinamento e implantação (destinos de computação) para o aprendizado de máquina
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: af912838e99e7b36cb29695758108f0a9efeb8ea
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561628"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>Criar destinos de computação para treinamento e implantação de modelo com o SDK do Python

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, use o SDK Azure Machine Learning Python para criar e gerenciar destinos de computação. Você também pode criar e gerenciar destinos de computação com:
* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md), 
* A [extensão da CLI](reference-azure-machine-learning-cli.md#resource-management) para Azure Machine Learning
* A [extensão de vs Code](how-to-manage-resources-vscode.md#compute-clusters) para Azure Machine Learning.


## <a name="prerequisites"></a>Pré-requisitos

* Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje
* O [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md)

## <a name="limitations"></a>Limitações

* Não **crie vários anexos simultâneos para a mesma computação** do seu espaço de trabalho. Por exemplo, anexar um cluster do serviço kubernetes do Azure a um espaço de trabalho usando dois nomes diferentes. Cada novo anexo interromperá os anexos existentes anteriores.

    Se você quiser anexar novamente um destino de computação, por exemplo, para alterar o TLS ou outra configuração de cluster, primeiro você deve remover o anexo existente.

* Alguns dos cenários listados neste documento são marcados como __Visualização__. A funcionalidade de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="whats-a-compute-target"></a>O que é um destino de computação?

Com o Azure Machine Learning, você pode treinar seu modelo em uma variedade de recursos ou ambientes, coletivamente denominados [__destinos de computação__](concept-azure-machine-learning-architecture.md#compute-targets). Um destino de computação pode ser um computador local ou um recurso de nuvem, como Computação do Azure Machine Learning, Azure HDInsight ou uma máquina virtual remota.  Você também pode criar destinos de computação para a implantação de modelo conforme descrito em ["em que local e como implantar seus modelos"](how-to-deploy-and-where.md).

## <a name="compute-targets-for-training"></a>Destinos de computação para treinamento

O Azure Machine Learning tem suporte variado nos diferentes destinos de computação. Um ciclo de vida de desenvolvimento de modelo típico começa com experimentação/desenvolvimento em uma pequena quantidade de dados. Nessa fase, é recomendável usar um ambiente local. Por exemplo, o computador local ou uma VM baseada em nuvem. Na medida em que você aumenta o treinamento em conjuntos de dados maiores ou realiza treinamento distribuído, é recomendável usar a Computação do Azure Machine Learning para criar um cluster de vários nós, ou de nó único, que dimensionará automaticamente sempre que você enviar uma execução. Também é possível anexar seu próprio recurso de computação, embora o suporte para vários cenários possa variar conforme detalhado abaixo:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Azure Machine Learning clusters de computação podem ser criados como um recurso persistente ou criados dinamicamente quando você solicita uma execução. A criação baseada em execução remove o destino de computação após a conclusão da execução de treinamento, portanto, não é possível reutilizar os destinos de computação criados dessa maneira.

Use as seções a seguir para configurar estes destinos de computação:

* [Computador local](#local)
* [Cluster de computação do Azure Machine Learning](#amlcompute)
* [Instância de computação do Azure Machine Learning](#instance)
* [Máquinas virtuais remotas](#vm)
* [Azure HDInsight](#hdinsight)

## <a name="compute-targets-for-inference"></a>Destinos de computação para inferência

Ao executar a inferência, Azure Machine Learning cria um contêiner do Docker que hospeda o modelo e os recursos associados necessários para usá-lo. Esse contêiner é usado em um dos seguintes cenários de implantação:

* Como um __serviço Web__ que é usado para inferência em tempo real. As implantações de serviço Web usam um dos seguintes destinos de computação:

    * [Computador local](#local)
    * [Instância de computação do Azure Machine Learning](#instance)
    * [Instâncias de Contêiner do Azure](#aci)
    * [Serviço de Kubernetes do Azure](how-to-create-attach-kubernetes.md)
    * Azure Functions (versão prévia). A implantação em Azure Functions se baseia apenas em Azure Machine Learning para criar o contêiner do Docker. A partir daí, ele é implantado usando Azure Functions. Para obter mais informações, consulte [implantar um modelo de aprendizado de máquina para Azure Functions (versão prévia)](how-to-deploy-functions.md).

* Como um ponto de extremidade de __inferência de lote__ que é usado para processar periodicamente lotes de dados. As inferências em lote usam [Azure Machine Learning cluster de computação](#amlcompute).

* Para um __dispositivo IOT__ (versão prévia). A implantação em um dispositivo IoT conta apenas com Azure Machine Learning para criar o contêiner do Docker. A partir daí, ele é implantado usando Azure IoT Edge. Para obter mais informações, consulte [implantar como um módulo IOT Edge (versão prévia)](/azure/iot-edge/tutorial-deploy-machine-learning).

## <a name="local-computer"></a><a id="local"></a>Computador local

Quando você usa seu computador local para **treinamento**, não é necessário criar um destino de computação.  Basta [enviar a execução de treinamento](how-to-set-up-training-targets.md) do computador local.

Ao usar seu computador local para **inferência**, você deve ter o Docker instalado. Para executar a implantação, use [LocalWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) para definir a porta que o serviço Web usará. Em seguida, use o processo de implantação normal, conforme descrito em [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning cluster de computação

Azure Machine Learning cluster de computação é uma infraestrutura de computação gerenciada que permite criar facilmente uma computação de vários nós ou um único nó. Ela é criada na sua região do workspace e é um recurso que pode ser compartilhado com outros usuários no workspace. A computação escala verticalmente e automaticamente quando um trabalho é enviado e pode ser colocada em uma Rede Virtual do Azure. A computação é executada em um ambiente em contêineres, empacotando as dependências do modelo em um [contêiner do Docker](https://www.docker.com/why-docker).

Você pode usar Azure Machine Learning computação para distribuir um processo de inferência de treinamento ou de lote em um cluster de nós de computação de CPU ou GPU na nuvem. Para obter mais informações sobre os tamanhos de máquina virtual que incluem GPUs, consulte os [Tamanhos de máquinas virtuais com GPU otimizadas](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

A Computação do Machine Learning do Azure tem limites padrão como o número de núcleos que podem ser alocados. Para obter mais informações, consulte [Gerenciar e solicitar cotas para recursos do Azure](how-to-manage-quotas.md).

> [!TIP]
> Os clusters geralmente podem ser escalados verticalmente para até 100 nós, desde que você tenha cota suficiente para o número de núcleos necessários. Por padrão, os clusters são configurados com a comunicação entre nós habilitada entre os nós do cluster para dar suporte a trabalhos de MPI, por exemplo. No entanto, você pode dimensionar seus clusters para milhares de nós simplesmente [gerando um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)e solicitando que o liste sua assinatura, ou espaço de trabalho, ou um cluster específico para desabilitar a comunicação entre nós. 

Uma Computação do Azure Machine Learning pode ser reutilizada entre execuções. A computação pode ser compartilhada com outros usuários no espaço de trabalho e é mantida entre as execuções, escalando ou reduzindo vertical e automaticamente os nós com base no número de execuções enviadas e o max_nodes definido no cluster. A configuração min_nodes controla os nós mínimos disponíveis.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **Criar e anexar**: Para criar um recurso persistente de Computação do Azure Machine Learning em Python, especifique as propriedades **vm_size** e **max_nodes**. O Azure Machine Learning usará padrões inteligentes para o restante das propriedades. A computação é reduzida automaticamente até zero nó quando não é usada.   VMs dedicadas são criadas para executar seus trabalhos conforme necessário.
    
    * **vm_size**: A família de VMs dos nós criados pela Computação do Machine Learning do Azure.
    * **max_nodes**: O número máximo de nós a serem dimensionados automaticamente durante a execução de um trabalho na Computação do Azure Machine Learning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Também é possível configurar várias propriedades avançadas ao criar a Computação do Azure Machine Learning. Essas propriedades permitem que criar um cluster persistente de tamanho fixo ou dentro de uma Rede Virtual do Azure existente na assinatura.  Veja [AmlCompute classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) para obter detalhes.

    Outra opção é criar e anexar a um recurso persistente de Computação do Azure Machine Learning no [estúdio do Azure Machine Learning](how-to-create-attach-compute-studio.md#portal-create).

Agora que você anexou a computação, a próxima etapa é enviar a [inferência](how-to-use-parallel-run-step.md) [de execução de treinamento](how-to-set-up-training-targets.md) ou execução de lote.

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Reduzir o custo do cluster de computação

Você também pode optar por usar [VMs de baixa prioridade](concept-plan-manage-cost.md#low-pri-vm) para executar algumas ou todas as suas cargas de trabalho. Essas VMs não têm disponibilidade garantida e podem ser substituídas enquanto estiverem em uso. Um trabalho admitido é reiniciado, não retomado. 

Use qualquer uma dessas maneiras para especificar uma VM de baixa prioridade:
    
* No estúdio, escolha **baixa prioridade** ao criar uma VM.
    
* Com o SDK do Python, defina o `vm_priority` atributo em sua configuração de provisionamento.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* Usando a CLI, defina `vm-priority` :
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Configurar identidade gerenciada

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Configure a identidade gerenciada na sua configuração de provisionamento:  
    
* Identidade gerenciada atribuída ao sistema:
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* Identidade gerenciada atribuída ao usuário: 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

Adicionar identidade gerenciada a um cluster de computação existente:  
    
* Identidade gerenciada atribuída ao sistema:

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* Identidade gerenciada atribuída ao usuário:

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>Uso de identidade gerenciada

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Instância de computação do Azure Machine Learning

[Azure Machine Learning instância de computação](concept-compute-instance.md) é uma infraestrutura de computação gerenciada que permite que você crie facilmente uma única VM. A computação é criada dentro de sua região de espaço de trabalho, mas ao contrário de um cluster de cálculo, uma instância não pode ser compartilhada com outros usuários em seu espaço de trabalho. Além disso, a instância não é reduzida automaticamente.  Você deve interromper o recurso para evitar encargos contínuos.

Uma instância de computação pode executar vários trabalhos em paralelo e tem uma fila de trabalhos. 

As instâncias de computação podem executar trabalhos com segurança em um [ambiente de rede virtual](how-to-enable-virtual-network.md#compute-instance), sem exigir que as empresas abram portas SSH. O trabalho é executado em um ambiente em contêiner e empacota suas dependências de modelo em um contêiner do Docker. 

1. **Criar e anexar**: 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

Agora que você anexou a computação e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](how-to-set-up-training-targets.md) ou [implantar um modelo para inferência](how-to-deploy-local-container-notebook-vm.md).

## <a name="azure-container-instance"></a><a id="aci"></a>Azure Container Instance

As ACI (instâncias de contêiner do Azure) são criadas dinamicamente quando você implanta um modelo. Não é possível criar ou anexar ACI ao seu espaço de trabalho de nenhuma outra maneira. Para obter mais informações, consulte [implantar um modelo para instâncias de contêiner do Azure](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

O AKS (serviço kubernetes do Azure) permite uma variedade de opções de configuração quando usadas com Azure Machine Learning. Para obter mais informações, consulte [como criar e anexar o serviço kubernetes do Azure](how-to-create-attach-kubernetes.md).

## <a name="remote-virtual-machines"></a><a id="vm"></a>Máquinas virtuais remotas

O Azure Machine Learning também dá suporte para que você traga seu próprio recurso de computação e anexe-o ao workspace. Um desses tipos de recursos é uma VM remota arbitrária, desde que seja acessível pelo Azure Machine Learning. O recurso pode ser uma VM do Azure ou um servidor remoto na organização ou no local. Especificamente, fornecido o endereço IP e as credenciais (nome de usuário e senha ou chave SSH), é possível usar qualquer VM acessível para execuções remotas.

Você pode usar um ambiente do Conda interno do sistema, um ambiente do Python já existente ou um contêiner do Docker. Para executar em um contêiner do Docker, você deve ter um mecanismo do Docker em execução na VM. Essa funcionalidade é especialmente útil quando você quer um ambiente de experimentação/desenvolvimento baseado em nuvem mais flexível que seu computador local.

Use o Máquina Virtual de Ciência de Dados do Azure (DSVM) como a VM do Azure escolhida para esse cenário. Essa VM é uma ciência de dados pré-configuradas e o ambiente de desenvolvimento de inteligência artificial do Azure. A VM oferece uma opção selecionada de ferramentas e estruturas para desenvolvimento do aprendizado de máquina de todo o ciclo de vida. Para saber mais sobre como usar a DSVM com o Azure Machine Learning, consulte [Configurar um ambiente de desenvolvimentopara o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Criar**: Crie uma DSVM antes de usá-la para treinar seu modelo. Para criar esse recurso, veja [Provisionar a Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning só dá suporte a máquinas virtuais que executam o **Ubuntu**. Para criar uma VM ou escolher uma VM existente, selecione uma VM que usa o Ubuntu.
    > 
    > Azure Machine Learning também exige que a máquina virtual tenha um __endereço IP público__.

1. **Anexar**: Para anexar uma máquina virtual existente como um destino de computação, você precisa fornecer a ID do recurso, o nome de usuário e a senha da máquina virtual. A ID de recurso da VM pode ser criada usando a ID da assinatura, o nome do grupo de recursos e o nome da VM com o seguinte formato de cadeia de caracteres: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Outra opção é anexar a DSVM ao seu workspace [usando o estúdio do Azure Machine Learning](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Não crie vários anexos simultâneos para o mesmo DSVM do seu espaço de trabalho. Cada novo anexo interromperá os anexos existentes anteriores.

1. **Configurar**: Crie uma configuração de execução para o destino de computação da DSVM. O Docker e o conda são usados para criar e configurar o ambiente de treinamento na DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](how-to-set-up-training-targets.md).

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

O Azure HDInsight é uma plataforma popular para análise de dados. A plataforma fornece o Apache Spark, que pode ser usado para o treinamento do seu modelo.

1. **Criar**:  Crie o cluster HDInsight antes de usá-lo para o treinamento do seu modelo. Para criar um Spark no cluster HDInsight, consulte [Criar um Cluster Spark no HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    > [!WARNING]
    > Azure Machine Learning requer que o cluster HDInsight tenha um __endereço IP público__.

    Ao criar o cluster, especifique um nome de usuário SSH e senha. Observe esses valores, conforme necessário, ao usar o HDInsight como um destino de computação.
    
    Depois que o cluster for criado, conecte-o com o hostname \<clustername> -SSH.azurehdinsight.net, em que \<clustername> é o nome que você forneceu para o cluster. 

1. **Anexar**: Para anexar a um cluster do HDInsight como um destino de computação, forneça a ID do recurso, o nome de usuário e a senha para o cluster HDInsight. A ID de recurso do cluster HDInsight pode ser criada usando a ID da assinatura, o nome do grupo de recursos e o nome do cluster HDInsight com o seguinte formato de cadeia de caracteres: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Outra opção é anexar o cluster HDInsight ao seu workspace [usando o estúdio do Azure Machine Learning](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Não crie vários anexos simultâneos para o mesmo HDInsight no seu espaço de trabalho. Cada novo anexo interromperá os anexos existentes anteriores.

1. **Configurar**: Crie uma configuração de execução para o destino de computação da HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Lote do Azure 

O Lote do Azure é usado para executar com eficiência aplicativos HPC (computação de alto desempenho) e paralelos em grande escala na nuvem. O AzureBatchStep pode ser usado em um Pipeline do Azure Machine Learning para enviar trabalhos a um pool de computadores do Lote do Azure.

Para anexar o Lote do Azure como um destino de computação, você deve usar o SDK do Azure Machine Learning e fornecer as seguintes informações:

-    **Nome de computação do Lote do Azure**: Um nome amigável a ser usado para a computação no espaço de trabalho
-    **Nome da conta do Lote do Azure**: O nome da conta do Lote do Azure
-    **Grupo de Recursos**: O grupo de recursos que contém o a conta do Lote do Azure.

O código a seguir demonstra como anexar o Lote do Azure como um destino de computação:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Não crie vários anexos simultâneos para o mesmo lote do Azure do seu espaço de trabalho. Cada novo anexo interromperá os anexos existentes anteriores.

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

As Azure Databricks são um ambiente baseado em Apache Spark na nuvem do Azure. Ele pode ser usado como um destino de computação com um pipeline do Azure Machine Learning.

Crie um workspace do Azure Databricks antes de usá-lo. Para criar um recurso de espaço de trabalho, consulte o documento [executar um trabalho do Spark no Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

Para anexar o Azure Databricks como um destino de computação, forneça as seguintes informações:

* __Nome de computação do databricks__: o nome que você deseja atribuir a esse recurso de computação.
* __Nome do espaço de trabalho do databricks__: o nome do espaço de trabalho Azure Databricks.
* __Token de acesso do databricks__: o token de acesso usado para autenticar para Azure Databricks. Para gerar um token de acesso, consulte o documento [Autenticação](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html).

O código a seguir demonstra como anexar Azure Databricks como um destino de computação com o SDK do Azure Machine Learning (__o espaço de trabalho do databricks precisa estar presente na mesma assinatura que o seu espaço de trabalho AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Para obter um exemplo mais detalhado, consulte um [exemplo de bloco de anotações](https://aka.ms/pl-databricks) no github.

> [!WARNING]
> Não crie vários anexos simultâneos para o mesmo Azure Databricks do seu espaço de trabalho. Cada novo anexo interromperá os anexos existentes anteriores.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Análise Azure Data Lake

O Azure Data Lake Analytics é uma plataforma de análise de big data na nuvem do Azure. Ele pode ser usado como um destino de computação com um pipeline do Azure Machine Learning.

Crie uma conta do Azure Data Lake Analytics antes de usá-lo. Para criar este recurso, consulte o documento [Introdução ao Google Analytics do Azure Data Lake](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Para anexar o Data Lake Analytics como um destino de computação, você deve usar o SDK do Aprendizado de Máquina do Azure e fornecer as seguintes informações:

* __Nome da computa__: o nome que você deseja atribuir a este recurso de computação.
* __Grupo de recursos__: o grupo de recursos que contém a conta de data Lake Analytics.
* __Nome da conta__: o nome da conta de data Lake Analytics.

O código a seguir demonstra como anexar o Data Lake Analytics como um destino de computação:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Para obter um exemplo mais detalhado, consulte um [exemplo de bloco de anotações](https://aka.ms/pl-adla) no github.

> [!WARNING]
> Não crie vários anexos simultâneos para o mesmo ADLA do seu espaço de trabalho. Cada novo anexo interromperá os anexos existentes anteriores.

> [!TIP]
> Os pipelines do Azure Machine Learning só podem funcionar com dados armazenados no armazenamento de dados padrão da conta do Data Lake Analytics. Se os dados com os quais você precisa trabalhar estiverem em um repositório não padrão, você poderá usar um [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py&preserve-view=true) para copiar os dados antes do treinamento.

## <a name="notebook-examples"></a>Exemplos de notebook

Veja estes notebooks para obter exemplos de treinamento com vários destinos de computação:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* Use o recurso de computação para [Enviar uma execução de treinamento](how-to-set-up-training-targets.md).
* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um destino de computação gerenciado para treinar um modelo.
* Saiba como [ajustar hiperparâmetros de modo eficiente](how-to-tune-hyperparameters.md) para criar modelos melhores.
* Quando você tiver um modelo treinado, aprenda [como e em que local implantar modelos](how-to-deploy-and-where.md).
* [Usar o Azure Machine Learning com Redes Virtuais do Azure](how-to-enable-virtual-network.md)
