---
title: Treine e implante modelos da CLI
titleSuffix: Azure Machine Learning
description: Aprenda a usar a extensão de aprendizado de máquina para o Azure CLI treinar, registrar e implantar um modelo a partir da linha de comando.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 401ce2aed2c783169592f0dc664a3a7baea415b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336616"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Tutorial: Treine e implante um modelo da CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você usa a extensão de aprendizado de máquina para o Azure CLI treinar, registrar e implantar um modelo.

Os scripts de treinamento Python neste tutorial usam [scikit-learn](https://scikit-learn.org/) para treinar um modelo básico. O foco deste tutorial não está nos scripts ou no modelo, mas no processo de usar a CLI para trabalhar com o Azure Machine Learning.

Saiba como executar as seguintes ações:

> [!div class="checklist"]
> * Instale a extensão de aprendizado de máquina
> * Criar um workspace do Azure Machine Learning
> * Criar o recurso de computação usado para treinar o modelo
> * Definir e registrar o conjunto de dados usado para treinar o modelo
> * Comece uma corrida de treinamento
> * Registre-se e baixe um modelo
> * Implantar o modelo como um serviço Web
> * Pontuação de dados usando o serviço web

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar os comandos CLI neste documento do ambiente **local,** você precisa do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Se você usa o [Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)o CLI é acessado através do navegador e vive na nuvem.

## <a name="download-the-example-project"></a>Baixe o projeto de exemplo

Para este tutorial, [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) baixe o projeto. Os arquivos `examples/cli-train-deploy` no diretório são usados pelas etapas deste tutorial.

Para obter uma cópia local dos arquivos, [baixe um arquivo .zip](https://github.com/microsoft/MLOps/archive/master.zip)ou use o seguinte comando Git para clonar o repositório:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Arquivos de treinamento

O `examples/cli-train-deploy` diretório do projeto contém os seguintes arquivos, que são usados ao treinar um modelo:

* `.azureml\mnist.runconfig`: Um arquivo __de configuração em execução.__ Este arquivo define o ambiente de tempo de execução necessário para treinar o modelo. Neste exemplo, ele também monta os dados utilizados para treinar o modelo no ambiente de treinamento.
* `scripts\train.py`: O roteiro de treinamento. Este arquivo treina o modelo.
* `scripts\utils.py`: Um arquivo auxiliar usado pelo script de treinamento.
* `.azureml\conda_dependencies.yml`: Define as dependências de software necessárias para executar o script de treinamento.
* `dataset.json`: A definição do conjunto de dados. Usado para registrar o conjunto de dados MNIST no espaço de trabalho Azure Machine Learning.

### <a name="deployment-files"></a>Arquivos de implantação

O repositório contém os seguintes arquivos, que são usados para implantar o modelo treinado como um serviço web:

* `aciDeploymentConfig.yml`: Um arquivo __de configuração de implantação.__ Este arquivo define o ambiente de hospedagem necessário para o modelo.
* `inferenceConfig.json`: Um arquivo __de configuração de inferência.__ Este arquivo define o ambiente de software usado pelo serviço para pontuar dados com o modelo.
* `score.py`: Um script python que aceita dados de entrada, pontua-os usando o modelo e, em seguida, retorna uma resposta.
* `scoring-env.yml`: As dependências de conda `score.py` precisavam executar o modelo e o script.
* `testdata.json`: Um arquivo de dados que pode ser usado para testar o serviço web implantado.

## <a name="connect-to-your-azure-subscription"></a>Conecte-se à sua assinatura do Azure

Existem várias maneiras que você pode autenticar para a sua assinatura do Azure a partir da CLI. O mais básico é autenticar interativamente usando um navegador. Para autenticar interativamente, abra uma linha de comando ou terminal e use o seguinte comando:

```azurecli-interactive
az login
```

Se a CLI pode abrir seu navegador padrão, ela irá fazê-lo e carregar uma página de entrada. Caso contrário, você precisa abrir um navegador e seguir as instruções na linha de comando. As instruções [https://aka.ms/devicelogin](https://aka.ms/devicelogin) envolvem navegar e inserir um código de autorização.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

## <a name="install-the-machine-learning-extension"></a>Instale a extensão de aprendizado de máquina

Para instalar a extensão de aprendizado de máquina, use o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Se você receber uma mensagem de que a extensão já está instalada, use o seguinte comando para atualizar para a versão mais recente:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner básico de recursos na plataforma Azure. Ao trabalhar com o Azure Machine Learning, o grupo de recursos conterá seu espaço de trabalho de Aprendizado de Máquina do Azure. Ele também conterá outros serviços do Azure usados pelo espaço de trabalho. Por exemplo, se você treinar seu modelo usando um recurso de computação baseado em nuvem, esse recurso será criado no grupo de recursos.

Para __criar um novo grupo de recursos,__ use o seguinte comando. Substitua-o pelo `<resource-group-name>` nome a ser usado para este grupo de recursos. Substitua pela `<location>` região Azure para usar para este grupo de recursos:

> [!TIP]
> Você deve selecionar uma região onde o Azure Machine Learning está disponível. Para obter informações, consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A resposta deste comando é semelhante ao seguinte JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Para obter mais informações sobre como trabalhar com grupos de recursos, consulte [o grupo az](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Criar um workspace

Para criar um novo espaço de trabalho, use o seguinte comando. Substitua pelo `<workspace-name>` nome que deseja usar para este espaço de trabalho. Substituir `<resource-group-name>` pelo nome do grupo de recursos:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

A saída deste comando é semelhante ao seguinte JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Conecte projeto local ao espaço de trabalho

A partir de um prompt de terminal ou comando, use os seguintes comandos alterar diretórios para o `cli-train-deploy` diretório e, em seguida, conecte-se ao seu espaço de trabalho:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

A saída deste comando é semelhante ao seguinte JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Este comando `.azureml/config.json` cria um arquivo, que contém informações necessárias para se conectar ao seu espaço de trabalho. O resto `az ml` dos comandos usados neste tutorial usará este arquivo, para que você não precise adicionar o espaço de trabalho e o grupo de recursos a todos os comandos.

## <a name="create-the-compute-target-for-training"></a>Crie o alvo de computação para treinamento

Este exemplo usa um cluster Azure Machine Learning Compute para treinar o modelo. Para criar um novo cluster de computação, use o seguinte comando:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

A saída deste comando é semelhante ao seguinte JSON:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Este comando cria um novo `cpu-cluster`alvo de computação chamado , com um máximo de quatro nós. O tamanho da VM selecionado fornece uma VM com um recurso de GPU. Para obter informações sobre o tamanho da VM, consulte [tipos e tamanhos de VM].

> [!IMPORTANT]
> O nome do alvo`cpu-cluster` da computação (neste caso), é importante; ele é referenciado `.azureml/mnist.runconfig` pelo arquivo usado na próxima seção.

## <a name="define-the-dataset"></a>Defina o conjunto de dados

Para treinar um modelo, você pode fornecer os dados de treinamento usando um conjunto de dados. Para criar um conjunto de dados a partir da CLI, você deve fornecer um arquivo de definição do conjunto de dados. O `dataset.json` arquivo fornecido no repo cria um novo conjunto de dados usando os dados do MNIST. O conjunto de dados `mnist-dataset`que ele cria é nomeado .

Para registrar o conjunto `dataset.json` de dados usando o arquivo, use o seguinte comando:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

A saída deste comando é semelhante ao seguinte JSON:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> Copie o valor `id` da entrada, como é usada na próxima seção.

Para ver um modelo mais abrangente para um conjunto de dados, use o seguinte comando:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Consulte o conjunto de dados

Para disponibilizar o conjunto de dados no ambiente de treinamento, você deve referencia-lo a partir do arquivo runconfig. O `.azureml/mnist.runconfig` arquivo contém as seguintes entradas YAML:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Alterar o valor `id` da entrada para corresponder ao valor retornado quando você registrou o conjunto de dados. Esse valor é usado para carregar os dados no alvo da computação durante o treinamento.

Este YAML resulta nas seguintes ações durante o treinamento:

* Monta o conjunto de dados (com base no ID do conjunto de dados) no `mnist` ambiente de treinamento e armazena o caminho até o ponto de montagem na variável ambiente.
* Passa a localização dos dados (ponto de montagem) dentro `--data-folder` do ambiente de treinamento para o script usando o argumento.

O arquivo runconfig também contém informações usadas para configurar o ambiente usado pela execução do treinamento. Se você inspecionar este arquivo, verá que `cpu-compute` ele faz referência ao alvo de computação que você criou anteriormente. Ele também lista o número de nós`"nodeCount": "4"`a serem `"condaDependencies"` usados durante o treinamento (), e contém uma seção que lista os pacotes Python necessários para executar o script de treinamento.

> [!TIP]
> Embora seja possível criar manualmente um arquivo runconfig, o deste `generate-runconfig.py` exemplo foi criado usando o arquivo incluído no repositório. Este arquivo recebe uma referência ao conjunto de dados registrado, cria uma configuração de execução programática e, em seguida, persiste para arquivá-lo.

Para obter mais informações sobre arquivos de configuração em execução, consulte [Configurar e usar metas de computação para treinamento de modelos](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli). Para obter uma referência JSON completa, consulte o [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

## <a name="submit-the-training-run"></a>Submeter a corrida de treinamento

Para iniciar uma execução de treinamento no alvo de `cpu-cluster` computação, use o seguinte comando:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Este comando especifica um nome`myexperiment`para o experimento ( ). O experimento armazena informações sobre essa corrida no espaço de trabalho.

O `-c mnist` parâmetro especifica `.azureml/mnist.runconfig` o arquivo.

O `-t` parâmetro armazena uma referência a esta execução em um arquivo JSON, e será usado nos próximos passos para registrar e baixar o modelo.

À medida que o treinamento executa processos, ele transmite informações da sessão de treinamento sobre o recurso de computação remota. Parte das informações é semelhante ao seguinte texto:

```output
Predict the test set
Accuracy is 0.9185
```

Este texto é registrado a partir do script de treinamento e exibe a precisão do modelo. Outros modelos terão diferentes métricas de desempenho.

Se você inspecionar o script de treinamento, você notará que ele também `outputs/sklearn_mnist_model.pkl`usa o valor alfa quando armazena o modelo treinado para .

O modelo foi `./outputs` salvo no diretório sobre o alvo de computação onde foi treinado. Neste caso, a instância de Computação de Aprendizado de Máquina do Azure na nuvem do Azure. O processo de treinamento carrega automaticamente `./outputs` o conteúdo do diretório a partir do objetivo de computação onde o treinamento ocorre para o seu espaço de trabalho azure Machine Learning. É armazenado como parte do`myexperiment` experimento (neste exemplo).

## <a name="register-the-model"></a>Registre o modelo

Para registrar o modelo diretamente da versão armazenada em seu experimento, use o seguinte comando:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Este comando registra `outputs/sklearn_mnist_model.pkl` o arquivo criado pelo training run `mymodel`como um novo registro de modelo chamado . As `--assets-path` referências são um caminho em um experimento. Neste caso, as informações de experiência e `runoutput.json` execução são carregadas a partir do arquivo criado pelo comando de treinamento. O `-t registeredmodel.json` cria um arquivo JSON que faz referência ao novo modelo registrado criado por este comando, e é usado por outros comandos CLI que trabalham com modelos registrados.

A saída deste comando é semelhante ao seguinte JSON:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Controle de versão de modelo

Observe o número da versão retornado para o modelo. A versão é incrementada cada vez que você registra um novo modelo com esse nome. Por exemplo, você pode baixar o modelo e registrá-lo a partir de um arquivo local usando os seguintes comandos:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

O primeiro comando baixa o modelo registrado para o diretório atual. O nome `sklearn_mnist_model.pkl`do arquivo é , que é o arquivo referenciado quando você registrou o modelo. O segundo comando registra o`-p "sklearn_mnist_model.pkl"`modelo local ( ) com`mymodel`o mesmo nome do registro anterior ( ). Desta vez, os dados json retornados listam a versão como 2.

## <a name="deploy-the-model"></a>Implantar o modelo

Para implantar um modelo, use o seguinte comando:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Você pode receber um aviso sobre "Falha ao verificar a existência do LocalWebservice" ou "Falha ao criar cliente Docker". Você pode ignorar isso com segurança, pois não está implantando um serviço web local.

Este comando implanta um `myservice`novo serviço chamado , usando a versão 1 do modelo que você registrou anteriormente.

O `inferenceConfig.yml` arquivo fornece informações sobre como usar o modelo para inferência. Por exemplo, ele faz referência`score.py`ao script de entrada ( ) e às dependências de software.

Para obter mais informações sobre a estrutura deste arquivo, consulte o esquema de configuração de [inferência](reference-azure-machine-learning-cli.md#inference-configuration-schema). Para obter mais informações sobre scripts de entrada, consulte [Implantar modelos com o Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

O `aciDeploymentConfig.yml` descreve o ambiente de implantação usado para hospedar o serviço. A configuração de implantação é específica para o tipo de computação que você usa para a implantação. Neste caso, é usada uma ocorrência de contêiner Azure. Para obter mais informações, consulte o [esquema de configuração de implantação](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Levará vários minutos até que o processo de implantação seja concluído.

> [!TIP]
> Neste exemplo, é usada a Ocorrência de Contêineres Azure. Implantações para ACI criam automaticamente o recurso ACI necessário. Se você for, em vez disso, implantar no Azure Kubernetes Service, você deve `az ml model deploy` criar um cluster AKS antes do tempo e especificá-lo como parte do comando. Para um exemplo de implantação no AKS, consulte [Implantar um modelo em um cluster do Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

Após vários minutos, informações semelhantes às seguintes JSON são devolvidas:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>O URI pontuação

O `scoringUri` retorno da implantação é o ponto final REST para um modelo implantado como um serviço web. Você também pode obter este URI usando o seguinte comando:

```azurecli-interactive
az ml service show -n myservice
```

Este comando retorna o mesmo documento `scoringUri`JSON, incluindo o .

O ponto final REST pode ser usado para enviar dados para o serviço. Para obter informações sobre a criação de um aplicativo cliente que envia dados para o serviço, consulte [Consumir um modelo de Machine Learning do Azure implantado como um serviço web](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>Enviar dados para o serviço

Embora você possa criar um aplicativo cliente para chamar o ponto final, o CLI de aprendizado de máquina fornece um utilitário que pode atuar como um cliente de teste. Use o seguinte comando para `testdata.json` enviar dados no arquivo para o serviço:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Se você usar o PowerShell, use o seguinte comando:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

A resposta do comando `[ 3 ]`é semelhante a .

## <a name="clean-up-resources"></a>Limpar recursos

> [!IMPORTANT]
> Os recursos que você criou podem ser usados como pré-requisitos em outros tutoriais e artigos de instruções do Azure Machine Learning.

### <a name="delete-deployed-service"></a>Excluir serviço implantado

Se você planeja continuar a usar o espaço de trabalho Azure Machine Learning, mas deseja se livrar do serviço implantado para reduzir custos, use o seguinte comando:

```azurecli-interactive
az ml service delete -n myservice
```

Este comando retorna um documento JSON que contém o nome do serviço excluído. Pode levar vários minutos até que o serviço seja excluído.

### <a name="delete-the-training-compute"></a>Exclua o cálculo de treinamento

Se você planeja continuar a usar o espaço de trabalho Azure `cpu-cluster` Machine Learning, mas deseja se livrar do alvo de computação criado para treinamento, use o seguinte comando:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

Este comando retorna um documento JSON que contém o ID do destino de computação excluído. Pode levar vários minutos até que o alvo de computação seja excluído.

### <a name="delete-everything"></a>Excluir tudo

Se você não planeja usar os recursos criados, exclua-os para que você não incorra em encargos adicionais.

Para excluir o grupo de recursos e todos os recursos do Azure criados neste documento, use o seguinte comando. Substitua pelo `<resource-group-name>` nome do grupo de recursos que você criou anteriormente:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial de Machine Learning do Azure, você usou a CLI de aprendizado de máquina para as seguintes tarefas:

> [!div class="checklist"]
> * Instale a extensão de aprendizado de máquina
> * Criar um workspace do Azure Machine Learning
> * Criar o recurso de computação usado para treinar o modelo
> * Definir e registrar o conjunto de dados usado para treinar o modelo
> * Comece uma corrida de treinamento
> * Registre-se e baixe um modelo
> * Implantar o modelo como um serviço Web
> * Pontuação de dados usando o serviço web

Para obter mais informações sobre o uso da CLI, consulte [Use a extensão CLI para Aprendizado de Máquina do Azure](reference-azure-machine-learning-cli.md).
