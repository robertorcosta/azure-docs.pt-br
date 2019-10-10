---
title: Criar e usar destinos de computação para treinamento de modelo
titleSuffix: Azure Machine Learning
description: Configure os ambientes de treinamento (destinos de computação) para treinamento de modelo de machine learning. É possível alternar facilmente os ambientes de treinamento. Inicie o treinamento localmente. Se precisar expandir, passe para um destino de computação em nuvem.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/12/2019
ms.custom: seodec18
ms.openlocfilehash: bce04a14a13d5b3615963f298f35af0d2fc480bb
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244429"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Configurar e usar destinos de computação para treinamento de modelo 

Com o Azure Machine Learning, você pode treinar seu modelo em uma variedade de recursos ou ambientes, coletivamente chamados de [__destinos de computação__](concept-azure-machine-learning-architecture.md#compute-targets). Um destino de computação pode ser um computador local ou um recurso de nuvem, como Computação do Azure Machine Learning, Azure HDInsight ou uma máquina virtual remota.  Você também pode criar destinos de computação para a implantação de modelo conforme descrito em ["em que local e como implantar seus modelos"](how-to-deploy-and-where.md).

Você pode criar e gerenciar um destino de computação usando o SDK do Azure Machine Learning, portal do Azure, sua página de aterrissagem do espaço de trabalho (versão prévia), CLI do Azure ou Azure Machine Learning VS Code extensão. Se você tiver destinos de computação criados por meio de outro serviço (por exemplo, um cluster HDInsight), poderá usá-los anexando-os ao seu espaço de trabalho do Azure Machine Learning.
 
Neste artigo, você aprende a usar vários destinos de computação para treinamento do modelo.  As etapas para todos os destinos de computação seguem o mesmo fluxo de trabalho:
1. Escolha __Criar__ um destino de computação se você ainda não tiver um.
2. Escolha __Anexar__ o destino de computação a seu workspace.
3. Escolha __Configurar__ o destino de computação para que ele contenha as dependências de pacote e ambiente Python exigidas pelo seu script.


>[!NOTE]
> O código deste artigo foi testado com a versão 1.0.39 do SDK do Azure Machine Learning.

## <a name="compute-targets-for-training"></a>Destinos de computação para treinamento

Azure Machine Learning tem suporte variado em diferentes destinos de computação. Um ciclo de vida de desenvolvimento de modelo típico começa com experimentação/desenvolvimento em uma pequena quantidade de dados. Nessa fase, é recomendável usar um ambiente local. Por exemplo, o computador local ou uma VM baseada em nuvem. Na medida em que você aumenta o treinamento em conjuntos de dados maiores ou faz treinamento distribuído, é recomendável usar a Computação do Azure Machine Learning para criar um cluster de vários nós, ou de nó único, que dimensionará automaticamente sempre que você enviar uma execução. Também é possível anexar seu próprio recurso de computação, embora o suporte para vários cenários possa variar conforme detalhado abaixo:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


> [!NOTE]
> A Computação do Azure Machine Learning pode ser criada como um recurso persistente ou criada dinamicamente quando você solicita uma execução. A criação baseada em execução remove o destino de computação após a conclusão da execução de treinamento, portanto, não é possível reutilizar os destinos de computação criados dessa maneira.

## <a name="whats-a-run-configuration"></a>O que é uma configuração de execução?

Durante o treinamento, é comum iniciar em seu computador local e depois executar esse script de treinamento em um destino de computação diferentes. Com o Azure Machine Learning, você pode executar o script em vários destinos de computação sem precisar alterar o script.

Tudo o que você precisa fazer é definir o ambiente para cada destino de computação em uma **configuração de execução**.  Em seguida, quando você quiser executar o teste de treinamento em um destino de computação diferente, especifique a configuração de execução para esse tipo de computação. Para obter detalhes de como especificar um ambiente e associá-lo para executar a configuração, consulte [criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md).

Saiba mais sobre [enviar experimentos](#submit) no final deste artigo.

## <a name="whats-an-estimator"></a>O que é um estimador?

Para facilitar o treinamento de modelo usando estruturas populares, o SDK do Python Azure Machine Learning fornece uma abstração alternativa de nível superior, a classe do estimador. Essa classe permite que você construa facilmente configurações de execução. Você pode criar e usar um [estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para enviar scripts de treinamento que usam qualquer estrutura de aprendizado que você escolher (como scikit-learn).

Para as tarefas PyTorch, TensorFlow e Chainer, Azure Machine Learning também fornece os respectivos estimadores [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar o uso dessas estruturas.

Para obter mais informações, consulte [treinar modelos de ml com estimadores](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>O que é um pipeline de ML?

Com pipelines de ML, você pode otimizar seu fluxo de trabalho com simplicidade, velocidade, portabilidade e reutilização. Ao criar pipelines com Azure Machine Learning, você pode se concentrar em sua experiência, aprendizado de máquina, em vez de infraestrutura e automação.

Os pipelines ML são construídos a partir de várias **etapas**, que são unidades computacionais distintas no pipeline. Cada etapa pode ser executada de forma independente e usar recursos de computação isolados. Isso permite que vários cientistas de dados funcionem no mesmo pipeline ao mesmo tempo sem sobrecarregar recursos de computação, além de facilitar o uso de diferentes tipos/tamanhos de computação para cada etapa.

> [!TIP]
> Os pipelines ML podem usar a configuração de execução ou os estimadores ao treinar modelos.

Embora os pipelines ML possam treinar modelos, eles também podem preparar dados antes de treinar e implantar modelos após o treinamento. Um dos principais casos de uso para pipelines é a pontuação em lote. Para obter mais informações, consulte [Pipelines: Otimize os fluxos de trabalho do Machine Learning @ no__t-0.

## <a name="set-up-in-python"></a>Configurar no Python

Use as seções a seguir para configurar estes destinos de computação:

* [Computador local](#local)
* [Computação do Azure Machine Learning](#amlcompute)
* [Máquinas virtuais remotas](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>Computador local

1. **Criar e anexar**: Não é necessário criar nem anexar um destino de computação para usar seu computador local como o ambiente de treinamento.  

1. **Configurar**:  Quando você usa seu computador local como um destino de computação, o código de treinamento é executado em seu [ambiente de desenvolvimento](how-to-configure-environment.md).  Se esse ambiente já tiver os pacotes do Python de que você precisa, use o ambiente gerenciado pelo usuário.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).

### <a id="amlcompute"></a>Computação do Azure Machine Learning

A Computação do Machine Learning do Azure é uma infraestrutura de computação gerenciada que permite ao usuário criar facilmente computação de único nó a vários nós. Ela é criada na sua região do workspace e é um recurso que pode ser compartilhado com outros usuários no workspace. A computação escala verticalmente e automaticamente quando um trabalho é enviado e pode ser colocada em uma Rede Virtual do Azure. A computação é executada em um ambiente em contêineres, empacotando as dependências do modelo em um [contêiner do Docker](https://www.docker.com/why-docker).

É possível usar a Computação do Azure Machine Learning para distribuir o processo de treinamento em um cluster de nós de computação de CPU ou GPU na nuvem. Para obter mais informações sobre os tamanhos de máquina virtual que incluem GPUs, consulte os [Tamanhos de máquinas virtuais com GPU otimizadas](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

A Computação do Machine Learning do Azure tem limites padrão como o número de núcleos que podem ser alocados. Para obter mais informações, consulte [Gerenciar e solicitar cotas para recursos do Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


É possível criar o ambiente de computação do Azure Machine Learning sob demanda ao programar uma execução, ou como um recurso persistente.

#### <a name="run-based-creation"></a>Criação baseada em execução

É possível criar a Computação do Azure Machine Learning como um destino de computação em tempo de execução. A computação é criada automaticamente para a sua execução. A computação é excluída automaticamente depois de a execução ser concluída. 

> [!NOTE]
> Para especificar o número máximo de nós a serem usados, normalmente você definirá `node_count` como o número de nós. Atualmente, há (04/04/2019) um bug que impede que isso funcione. Como alternativa, use a propriedade `amlcompute._cluster_max_node_count` da configuração de execução. Por exemplo: `run_config.amlcompute._cluster_max_node_count = 5`.

> [!IMPORTANT]
> A criação baseada em execução da computação do Azure Machine Learning atualmente está em Versão Prévia. Não use a criação baseada em execução se você estiver usando o ajuste de hiperparâmetro ou o aprendizado de máquina automatizado. Para usar o ajuste de hiperparâmetro ou aprendizado de máquina automatizados, crie um destino de [computação persistente](#persistent) em vez disso.

1.  **Criar, anexar e configurar**: a criação com base em execução gera todas as etapas necessárias para criar, anexar e configurar o destino de computação com a configuração de execução.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).

#### <a id="persistent"></a>Computação persistente

Uma Computação do Azure Machine Learning pode ser reutilizada em trabalhos. Ele pode ser compartilhado com outros usuários no workspace e é mantido entre trabalhos.

1. **Criar e anexar**: Para criar um recurso persistente de Computação do Azure Machine Learning em Python, especifique as propriedades **vm_size** e **max_nodes**. O Azure Machine Learning usará padrões inteligentes para o restante das propriedades. A computação é reduzida automaticamente até zero nó quando não é usada.   VMs dedicadas são criadas para executar seus trabalhos conforme necessário.
    
    * **vm_size**: A família de VMs dos nós criados pela Computação do Machine Learning do Azure.
    * **max_nodes**: O número máximo de nós a serem dimensionados automaticamente durante a execução de um trabalho na Computação do Azure Machine Learning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Também é possível configurar várias propriedades avançadas ao criar a Computação do Azure Machine Learning. Essas propriedades permitem que criar um cluster persistente de tamanho fixo ou dentro de uma Rede Virtual do Azure existente na assinatura.  Veja [AmlCompute classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) para obter detalhes.
    
   Outra opção é criar e anexar a um recurso persistente de Computação do Azure Machine Learning [no portal do Azure](#portal-create).

1. **Configurar**: Crie uma configuração de execução para o destino de computação persistente.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).


### <a id="vm"></a>Máquinas virtuais remotas

O Azure Machine Learning também dá suporte para que você traga seu próprio recurso de computação e anexe-o ao workspace. Um desses tipos de recurso é uma VM remota arbitrária, desde que seja acessível a partir de Azure Machine Learning. O recurso pode ser uma VM do Azure ou um servidor remoto na organização ou no local. Especificamente, fornecido o endereço IP e as credenciais (nome de usuário e senha ou chave SSH), é possível usar qualquer VM acessível para execuções remotas.

Você pode usar um ambiente do Conda interno do sistema, um ambiente do Python já existente ou um contêiner do Docker. Para executar em um contêiner do Docker, você deve ter um mecanismo do Docker em execução na VM. Essa funcionalidade é especialmente útil quando você quer um ambiente de experimentação/desenvolvimento baseado em nuvem mais flexível que seu computador local.

Use a DSVM (Máquina Virtual de Ciência de Dados) do Azure como a VM do Azure de escolha para esse cenário. Essa VM é uma ciência de dados pré-configuradas e o ambiente de desenvolvimento de inteligência artificial do Azure. A VM oferece uma opção selecionada de ferramentas e estruturas para desenvolvimento do aprendizado de máquina de todo o ciclo de vida. Para saber mais sobre como usar a DSVM com o Azure Machine Learning, consulte [Configurar um ambiente de desenvolvimentopara o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Criar**: Crie uma DSVM antes de usá-la para treinar seu modelo. Para criar esse recurso, veja [Provisionar a Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > O Azure Machine Learning dá suporte apenas a máquinas virtuais que executam o Ubuntu. Para criar uma VM ou escolher uma VM existente, selecione uma VM que usa o Ubuntu.

1. **Anexar**: Para anexar uma máquina virtual existente como um destino de computação, você precisa fornecer o nome de domínio totalmente qualificado (FQDN), o nome de logon e a senha da máquina virtual. No exemplo, substitua \<fqdn> com o FQDN público do VM ou o endereço IP público. Substitua \<nome de usuário> e \<senha > por um novo nome de usuário do SSH e senha.

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Outra opção é anexar a DSVM ao seu workspace [usando o portal do Azure](#portal-reuse).

1. **Configurar**: Crie uma configuração de execução para o destino de computação da DSVM. O Docker e o conda são usados para criar e configurar o ambiente de treinamento na DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).

### <a id="hdinsight"></a>Azure HDInsight 

O Azure HDInsight é uma plataforma popular para análise de dados. A plataforma fornece o Apache Spark, que pode ser usado para o treinamento do seu modelo.

1. **Criar**:  Crie o cluster HDInsight antes de usá-lo para o treinamento do seu modelo. Para criar um Spark no cluster HDInsight, consulte [Criar um Cluster Spark no HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Ao criar o cluster, especifique um nome de usuário SSH e senha. Observe esses valores, conforme necessário, ao usar o HDInsight como um destino de computação.
    
    Depois que o cluster é criado, conecte-o com o nome do host \<clustername >-ssh.azurehdinsight.net, em que \<clustername> é o nome que você forneceu para o cluster. 

1. **Anexar**: Para anexar a um cluster do HDInsight como um destino de computação, forneça o nome do host, o nome de usuário e a senha para o cluster HDInsight. O exemplo a seguir usa o SDK para anexar um cluster ao seu workspace. No exemplo, substitua \<clustername> pelo nome do seu cluster. Substitua \<nome de usuário> e \<senha > por um novo nome de usuário do SSH e senha do cluster.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azurehdinsight.net', 
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

   Outra opção é anexar o cluster HDInsight ao workspace [usando o portal do Azure](#portal-reuse).

1. **Configurar**: Crie uma configuração de execução para o destino de computação da HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Agora que você anexou um computador e configurou sua execução, a próxima etapa é [enviar a execução de treinamento](#submit).


### <a id="azbatch"></a>Lote do Azure 

O lote do Azure é usado para executar aplicativos de HPC (computação de alto desempenho) e paralelos em grande escala com eficiência na nuvem. AzureBatchStep pode ser usado em um pipeline Azure Machine Learning para enviar trabalhos para um pool de computadores do lote do Azure.

Para anexar o lote do Azure como um destino de computação, você deve usar o SDK do Azure Machine Learning e fornecer as seguintes informações:

-   **Nome de computação do lote do Azure**: Um nome amigável a ser usado para a computação no espaço de trabalho
-   **Nome da conta do lote do Azure**: O nome da conta do lote do Azure
-   **Grupo de Recursos**: O grupo de recursos que contém a conta do lote do Azure.

O código a seguir demonstra como anexar o lote do Azure como um destino de computação:

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

## <a name="set-up-in-azure-portal"></a>Configurar no portal do Azure

Você pode acessar quais destinos de computação são associados com seu workspace no portal do Azure.  Você pode usar o portal para:

* [Exibir destinos de computação](#portal-view) anexado ao seu workspace
* [Criar um destino de computação](#portal-create) no workspace
* [Anexar um destino de computação](#portal-reuse) que foi criado fora do workspace


Depois que um destino é criado e anexado ao seu workspace, você o usará em sua configuração de execução com um objeto `ComputeTarget`: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Exibir destinos de computação


Para ver os destinos de computação do seu workspace, use as seguintes etapas:

1. Visite o [portal do Azure](https://portal.azure.com) e navegue até o seu workspace. Você também pode acessar essas mesmas etapas na [página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com), embora as imagens abaixo mostrem o portal do Azure.
 
1. Em __Aplicativos__, selecione __Computação__.

    [Guia de computação ![View](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a id="portal-create"></a>Criar um destino de computação

Siga as etapas anteriores para exibir a lista de destinos de computação. Em seguida, use estas etapas para criar um destino de computação: 

1. Selecione no sinal de adição (+) para adicionar um destino de computação.

    ![Adicionar um destino de computação](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Insira um nome para o destino de computação. 

1. Selecione **Computação do Machine Learning** como o tipo da computação a ser usada para __Treinamento__. 

    >[!NOTE]
    >A Computação do Azure Machine Learning é o único recurso de computação gerenciado que você pode criar no portal do Azure.  Todos os outros recursos de computação podem ser anexados depois de serem criados.

1. Preencha o formulário. Forneça valores para as propriedades necessárias, especialmente **Família de VMs**e o **máximo de nós** utilizado para acelerar a computação.  

1. Selecione __Criar__.


1. Exiba o status da operação de criação, selecionando o destino de computação na lista:

    ![Selecione um destino de computação para exibir o status da operação criar](./media/how-to-set-up-training-targets/View_list.png)

1. Em seguida, visualize os detalhes do destino de computação: 

    ![Exibir os detalhes do destino de computação](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a id="portal-reuse"></a>Anexar destinos de computação

Para usar os destinos de computação criados fora do espaço de trabalho Azure Machine Learning, você deve anexá-los. Anexar um destino de computação o disponibiliza para seu workspace.

Siga as etapas anteriores para exibir a lista de destinos de computação. Em seguida, use as etapas abaixo para anexar um destino de computação: 

1. Selecione no sinal de adição (+) para adicionar um destino de computação. 
1. Insira um nome para o destino de computação. 
1. Selecione o tipo de computação a ser anexado para __Treinamento__:

    > [!IMPORTANT]
    > Nem todos os tipos de computação podem ser anexados usando o portal do Azure. Os tipos de computação que podem ser anexados para treinamento são:
    >
    > * VM remota
    > * Azure Databricks (para uso em pipelines de aprendizado de máquina)
    > * Azure Data Lake Analytics (para uso em pipelines de aprendizado de máquina)
    > * Azure HDInsight

1. Preencha o formulário e forneça valores para as propriedades necessárias.

    > [!NOTE]
    > A Microsoft recomenda o uso de chaves SSH, por serem mais seguras do que as senhas. As senhas são vulneráveis a ataques de força bruta. As chaves SSH contam com as assinaturas de criptografia. Para obter informações sobre como criar chaves SSH para uso com Máquinas Virtuais do Azure, consulte os seguintes documentos:
    >
    > * [Criar e usar chaves SSH no Linux ou macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Criar e usar chaves SSH no Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Selecionar __Anexar__. 
1. Exiba o status da operação de anexação, selecionando o destino de computação na lista.

## <a name="set-up-with-cli"></a>Configurar com a CLI

Você pode acessar os destinos de computação associados ao seu espaço de trabalho usando a [extensão da CLI](reference-azure-machine-learning-cli.md) para Azure Machine Learning.  Você pode usar a CLI para:

* Criar um destino de computação gerenciado
* Atualizar um destino de computação gerenciado
* Anexar um destino de computação não gerenciado

Para obter mais informações, veja [Gerenciamento de recursos](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Configurar com VS Code

Você pode acessar, criar e gerenciar os destinos de computação associados ao seu espaço de trabalho usando a [extensão vs Code](how-to-vscode-tools.md#create-and-manage-compute-targets) para Azure Machine Learning.

## <a id="submit"></a>Enviar execução de treinamento usando Azure Machine Learning SDK

Depois de criar uma configuração de execução, você pode usá-la para executar seu teste.  O padrão de código para enviar uma execução de treinamento é o mesmo para todos os tipos de destinos de computação:

1. Criar um experimento para executar
1. Enviar a execução.
1. Aguarde a conclusão da execução.

> [!IMPORTANT]
> Quando você envia a execução de treinamento, um instantâneo do diretório que contém seus scripts de treinamento é criado e enviado para o destino de computação. Ele também é armazenado como parte do experimento em seu espaço de trabalho. Se você alterar os arquivos e enviar a execução novamente, somente os arquivos alterados serão carregados.
>
> Para impedir que arquivos sejam incluídos no instantâneo, crie um arquivo [. gitignore](https://git-scm.com/docs/gitignore) ou `.amlignore` no diretório e adicione os arquivos a ele. O arquivo `.amlignore` usa a mesma sintaxe e padrões que o arquivo [. gitignore](https://git-scm.com/docs/gitignore) . Se ambos os arquivos existirem, o arquivo `.amlignore` terá precedência.
> 
> Para obter mais informações, veja [cópias de sombra](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Criar uma experiência

Primeiro, crie um experimento no seu workspace.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Enviar o teste

Envie o experimento com um objeto `ScriptRunConfig`.  Esse objeto inclui:

* **source_directory**: o diretório de origem que contém o script de treinamento
* **script**: identificar o script de treinamento
* **run_config**: a configuração de execução que, por sua vez, define em que local ocorrerá o treinamento.

Por exemplo, para usar a configuração de [destino local](#local):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Alterne o mesmo experimento para ser executado em um destino de computação diferente usando uma configuração de execução diferente, como [destino amlcompute](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Este exemplo usa como padrão apenas um nó do destino de computação para treinamento. Para usar mais de um nó, defina o `node_count` da configuração de execução para o número de nós desejado. Por exemplo, o código a seguir define o número de nós usados para treinamento para quatro:
>
> ```python
> src.run_config.node_count = 4
> ```

Ou você pode:

* Enviar o teste com um objeto `Estimator` conforme mostrado em [Treinar modelos de ML com estimadores](how-to-train-ml-models.md).
* Envie uma execução HyperDrive para [ajuste de hiperparâmetro](how-to-tune-hyperparameters.md).
* Envie um experimento por meio da [extensão vs Code](how-to-vscode-tools.md#train-and-tune-models).

Para obter mais informações, consulte a documentação do [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) e do [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) .

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Criar configuração de execução e enviar execução usando Azure Machine Learning CLI

Você pode usar [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e [Machine Learning extensão da CLI](reference-azure-machine-learning-cli.md) para criar configurações de execução e enviar execuções em diferentes destinos de computação. Os exemplos a seguir pressupõem que você tenha um Workspace do Azure Machine Learning existente e que tenha feito logon no Azure usando o comando da CLI `az login`. 

### <a name="create-run-configuration"></a>Criar configuração de execução

A maneira mais simples de criar a configuração de execução é navegar na pasta que contém os scripts do Python do Machine Learning e usar o comando da CLI

```azurecli
az ml folder attach
```

Esse comando cria uma subpasta `.azureml` que contém arquivos de configuração de execução de modelo para destinos de computação diferentes. Você pode copiar e editar esses arquivos para personalizar sua configuração, por exemplo, para adicionar pacotes do Python ou alterar as configurações do Docker.  

### <a name="structure-of-run-configuration-file"></a>Estrutura do arquivo de configuração de execução

O arquivo de configuração de execução é YAML formatado, com as seções a seguir
 * O script a ser executado e seus argumentos
 * Nome de destino de computação, "local" ou nome de uma computação no espaço de trabalho.
 * Parâmetros para executar a execução: Framework, Communicator para execuções distribuídas, duração máxima e número de nós de computação.
 * Seção de ambiente. Consulte [criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md) para obter detalhes dos campos nesta seção.
   * Para especificar os pacotes do Python a serem instalados para a execução, crie o [arquivo de ambiente Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)e defina o campo __condaDependenciesFile__ .
 * Execute os detalhes do histórico para especificar a pasta do arquivo de log e para habilitar ou desabilitar a coleta de saída e instantâneos de histórico de execução.
 * Detalhes de configuração específicos para a estrutura selecionada.
 * Referência de dados e detalhes do armazenamento de dados.
 * Detalhes de configuração específicos para Computação do Machine Learning para criar um novo cluster.

### <a name="create-an-experiment"></a>Criar uma experiência

Primeiro, crie um experimento para suas execuções

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Execução de script

Para enviar uma execução de script, execute um comando

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive executar

Você pode usar HyperDrive com CLI do Azure para executar as execuções de ajuste de parâmetro. Primeiro, crie um arquivo de configuração HyperDrive no formato a seguir. Consulte [ajustar hiperparâmetros para seu](how-to-tune-hyperparameters.md) artigo de modelo para obter detalhes sobre parâmetros de ajuste de hiperparâmetro.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Adicione esse arquivo junto com os arquivos de configuração de execução. Em seguida, envie uma execução de HyperDrive usando:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Observe a seção *argumentos* em runconfig e o *espaço de parâmetro* em hyperdrive config. Eles contêm os argumentos de linha de comando a serem passados para o script de treinamento. O valor em runconfig permanece o mesmo para cada iteração, enquanto o intervalo na configuração de HyperDrive é iterado. Não especifique o mesmo argumento em ambos os arquivos.

Para obter mais detalhes sobre esses comandos da CLI ```az ml``` e conjunto completo de argumentos, consulte [a documentação de referência](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Acompanhamento e integração de git

Quando você inicia uma execução de treinamento onde o diretório de origem é um repositório git local, as informações sobre o repositório são armazenadas no histórico de execuções. Por exemplo, a ID de confirmação atual para o repositório é registrada como parte do histórico.

## <a name="notebook-examples"></a>Exemplos de notebook

Veja estes notebooks para obter exemplos de treinamento com vários destinos de computação:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um destino de computação gerenciado para treinar um modelo.
* Saiba como [ajustar os hiperparâmetros com eficiência](how-to-tune-hyperparameters.md) para criar modelos melhores.
* Quando você tiver um modelo treinado, aprenda [como e em que local implantar modelos](how-to-deploy-and-where.md).
* Exiba a referência do SKD da [classe RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py).
* [Usar Azure Machine Learning com redes virtuais do Azure](how-to-enable-virtual-network.md)
