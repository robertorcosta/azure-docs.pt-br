---
title: Extensão cli
titleSuffix: Azure Machine Learning
description: Aprenda sobre a extensão CLI do Azure Machine Learning para a CLI do Azure. A CLI do Azure é um utilitário de linha de comando de plataforma cruzada que permite trabalhar com recursos na nuvem do Azure. A extensão Machine Learning permite que você trabalhe com o Azure Machine Learning. O ML CLI cria e gerencia recursos como seu espaço de trabalho, datastores, conjuntos de dados, pipelines, modelos e implantações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 16f9080487af95e7de5c5f8c91fd5c8d356b7bde
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618075"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Use a extensão CLI para Aprendizado de Máquina do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A CLI do Azure Machine Learning é uma extensão do [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), uma interface de linha de comando de plataforma cruzada para a plataforma Azure. Esta extensão fornece comandos para trabalhar com o Azure Machine Learning. Ele permite que você automatize suas atividades de aprendizado de máquina. A lista a seguir fornece alguns exemplos de ações que você pode fazer com a extensão CLI:

+ Executar experimentos para criar modelos de aprendizado de máquina

+ Registrar modelos de aprendizado de máquina para uso do cliente

+ Empacote, implante e rastreie o ciclo de vida de seus modelos de aprendizado de máquina

O CLI não é um substituto para o SDK do Azure Machine Learning. É uma ferramenta complementar que é otimizada para lidar com tarefas altamente parametrizadas que se adequam bem à automação.

## <a name="prerequisites"></a>Pré-requisitos

* Para usar a CLI, você deve ter uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar os comandos CLI neste documento do ambiente **local,** você precisa do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Se você usa o [Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)o CLI é acessado através do navegador e vive na nuvem.

## <a name="full-reference-docs"></a>Docs de referência completos

Encontre os [docs de referência completos para a extensão azure-cli-ml do Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="connect-the-cli-to-your-azure-subscription"></a>Conectar a CLI à assinatura do Azure

> [!IMPORTANT]
> Se você estiver usando o Azure Cloud Shell, você pode pular esta seção. O shell na nuvem autentica automaticamente você usando a conta que você faz login na sua assinatura do Azure.

Existem várias maneiras que você pode autenticar para a sua assinatura do Azure a partir da CLI. O mais básico é autenticar interativamente usando um navegador. Para autenticar interativamente, abra uma linha de comando ou terminal e use o seguinte comando:

```azurecli-interactive
az login
```

Se a CLI pode abrir seu navegador padrão, ela irá fazê-lo e carregar uma página de entrada. Caso contrário, você precisa abrir um navegador e seguir as instruções na linha de comando. As instruções [https://aka.ms/devicelogin](https://aka.ms/devicelogin) envolvem navegar e inserir um código de autorização.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Para obter outros métodos de autenticação, consulte [Entrar com o Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalar a extensão

Para instalar a extensão CLI do Machine Learning, use o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Exemplos de arquivos que você pode usar com os comandos abaixo podem ser encontrados [aqui](https://aka.ms/azml-deploy-cloud).

Quando solicitado, selecione `y` para instalar a extensão.

Para verificar se a extensão foi instalada, use o seguinte comando para exibir uma lista de subcomandos específicos do ML:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Atualizar a extensão

Para atualizar a extensão CLI de aprendizado de máquina, use o seguinte comando:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Remover a extensão

Para remover a extensão CLI, use o seguinte comando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gerenciamento de recursos

Os comandos a seguir demonstram como usar a CLI para gerenciar recursos usados pelo Azure Machine Learning.

+ Se você ainda não tiver um, crie um grupo de recursos:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Criar um espaço de trabalho de aprendizado de máquina do Azure:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Este comando cria um espaço de trabalho de edição básica. Para criar um espaço de `--sku enterprise` trabalho `az ml workspace create` corporativo, use o switch com o comando. Para obter mais informações sobre as edições do Azure Machine Learning, consulte [O que é Aprendizado de Máquina do Azure](overview-what-is-azure-ml.md#sku).

    Para obter mais informações, consulte [az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Conecte uma configuração de espaço de trabalho a uma pasta para ativar a consciência contextual da CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Este comando `.azureml` cria um subdiretório que contém arquivos de ambiente runconfig e conda de exemplo. Ele também `config.json` contém um arquivo que é usado para se comunicar com o seu espaço de trabalho Azure Machine Learning.

    Para obter mais informações, consulte [a pasta az ml attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Anexar um recipiente de blob Azure como um Datastore.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Para obter mais informações, consulte [az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Faça upload de arquivos para um Datastore.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Para obter mais informações, consulte [az ml datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Anexar um cluster AKS como um alvo de computação.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Para obter mais informações, consulte [az ml computetarget anexar aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Crie um novo alvo de AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Para obter mais informações, consulte [az ml computetarget criar amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a name="run-experiments"></a><a id="experiments"></a>Executar experimentos

* Comece uma corrida de sua experiência. Ao usar este comando, especifique o nome \*do arquivo runconfig (o texto antes de .runconfig se você estiver olhando para o sistema de arquivos) contra o parâmetro -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > O `az ml folder attach` comando `.azureml` cria um subdiretório, que contém dois exemplos de arquivos runconfig. 
    >
    > Se você tiver um script Python que crie um objeto de configuração de execução programáticamente, você pode usar [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) para salvá-lo como um arquivo runconfig.
    >
    > O esquema de runconfig completo pode ser encontrado neste [arquivo JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). O esquema é auto-documentado através `description` da chave de cada objeto. Além disso, há enums para possíveis valores e um trecho de modelo no final.

    Para obter mais informações, consulte [az ml executar o script de envio](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Veja uma lista de experimentos:

    ```azurecli-interactive
    az ml experiment list
    ```

    Para obter mais informações, consulte [a lista de experimentos az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Gerenciamento de conjuntos de dados

Os seguintes comandos demonstram como trabalhar com conjuntos de dados no Azure Machine Learning:

+ Registre um conjunto de dados:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Para obter informações sobre o formato do arquivo JSON `az ml dataset register --show-template`usado para definir o conjunto de dados, use .

    Para obter mais informações, consulte [o registro do conjunto de dados az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Arquive um conjunto de dados ativo ou preterido:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Para obter mais informações, consulte [o arquivo de conjunto de dados az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Depreciar um conjunto de dados:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Para obter mais informações, consulte [az ml dataset depreciar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Liste todos os conjuntos de dados em um espaço de trabalho:

    ```azurecli-interactive
    az ml dataset list
    ```

    Para obter mais informações, consulte [a lista de conjuntos de dados az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Obtenha detalhes de um conjunto de dados:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Para obter mais informações, consulte [az ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Reativar um conjunto de dados arquivado ou preterido:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Para obter mais informações, consulte [az ml dataset reativar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Desregistre um conjunto de dados:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Para obter mais informações, consulte [o conjunto de dados az ml sem registro](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Gestão ambiental

Os seguintes comandos demonstram como criar, registrar e listar [ambientes](how-to-configure-environment.md) de Machine Learning do Azure para o seu espaço de trabalho:

+ Criar arquivos de andaimes para um ambiente:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Para obter mais informações, consulte [o andaime do ambiente az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registre um ambiente:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Para obter mais informações, consulte [o registro de ambiente az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Listar ambientes registrados:

    ```azurecli-interactive
    az ml environment list
    ```

    Para obter mais informações, consulte [a lista de ambientes az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Baixe um ambiente registrado:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Para obter mais informações, consulte [o download do ambiente az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Esquema de configuração do ambiente

Se você `az ml environment scaffold` usou o comando, `azureml_environment.json` ele gerará um arquivo de modelo que pode ser modificado e usado para criar configurações de ambiente personalizadas com o CLI. O objeto de nível superior [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) mapeia vagamente para a classe no Python SDK. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

A tabela a seguir detalha cada campo de nível superior no arquivo JSON, seu tipo e uma descrição. Se um tipo de objeto estiver vinculado a uma classe do Python SDK, haverá uma correspondência 1:1 solta entre cada campo JSON e o nome da variável pública na classe Python. Em alguns casos, o campo pode mapear para um argumento construtor em vez de uma variável de classe. Por exemplo, `environmentVariables` o campo `environment_variables` mapeia a variável na [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) classe.

| Campo JSON | Type | Descrição |
|---|---|---|
| `name` | `string` | Nome do ambiente. Não inicie o nome com **microsoft** ou **AzureML**. |
| `version` | `string` | Versão do ambiente. |
| `environmentVariables` | `{string: string}` | Um mapa hash de nomes e valores variáveis de ambiente. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Objeto que define o ambiente Python e o interpretador para usar no recurso de computação de destino. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Define configurações para personalizar a imagem Docker construída de acordo com as especificações do ambiente. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | A seção configura configura configurações de Faísca. Ele só é usado quando a estrutura é definida como PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Configura as dependências da biblioteca Databricks. |
| `inferencingStackVersion` | `string` | Especifica a versão de pilha de inferência adicionada à imagem. Para evitar adicionar uma pilha de `null`inferências, deixe este campo . Valor válido: "mais recente". |

## <a name="ml-pipeline-management"></a>Gerenciamento de gasodutos ML

Os seguintes comandos demonstram como trabalhar com pipelines de aprendizado de máquina:

+ Crie um pipeline de aprendizado de máquina:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Para obter mais informações, consulte [a criação de pipeline az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Para obter mais informações sobre o arquivo YAML do pipeline, consulte [Definir pipelines de aprendizado de máquina em YAML](reference-pipeline-yaml.md).

+ Executar um oleoduto:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Para obter mais informações, consulte [az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Para obter mais informações sobre o arquivo YAML do pipeline, consulte [Definir pipelines de aprendizado de máquina em YAML](reference-pipeline-yaml.md).

+ Agende um gasoduto:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Para obter mais informações, consulte [az ml pipeline create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Para obter mais informações sobre o arquivo YAML do cronograma do pipeline, consulte [Definir pipelines de aprendizado de máquina em YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Registro de modelo, perfil, implantação

Os comandos a seguir demonstram como registrar um modelo treinado e implantá-lo como um serviço de produção:

+ Registre um modelo com o Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Para obter mais informações, consulte [o registro do modelo az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **OPCIONAL** Faça o perfil do modelo para obter valores ideais de CPU e memória para implantação.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Para obter mais informações, consulte [o perfil do modelo az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Implante seu modelo para a KS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Para obter mais informações sobre o esquema de configuração de inferência, consulte esquema de [configuração de inferência](#inferenceconfig).
    
    Para obter mais informações sobre o esquema de configuração de implantação, consulte [Esquema de configuração de implantação](#deploymentconfig).

    Para obter mais informações, consulte [aaz ml modelo deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Esquema de configuração de inferência

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Esquema de configuração de implantação

### <a name="local-deployment-configuration-schema"></a>Esquema de configuração de implantação local

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Esquema de configuração de configuração de configuração de instância de contêiner do Azure 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Esquema de configuração de configuração do Serviço Azure Kubernetes

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Próximas etapas

* [Referência de comando para a extensão CLI machine learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Treine e implante modelos de aprendizado de máquina usando a Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
