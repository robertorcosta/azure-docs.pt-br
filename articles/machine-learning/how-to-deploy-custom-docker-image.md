---
title: Implantar modelos com imagem Docker personalizada
titleSuffix: Azure Machine Learning
description: Aprenda a usar uma imagem base docker personalizada ao implantar seus modelos de Aprendizado de Máquina do Azure. Embora o Azure Machine Learning forneça uma imagem base padrão para você, você também pode usar sua própria imagem base.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/16/2020
ms.openlocfilehash: a237beb72e35a236e353c58db520a8d611fdfdcd
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617995"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Implantar um modelo usando uma imagem base docker personalizada
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a usar uma imagem base docker personalizada ao implantar modelos treinados com o Azure Machine Learning.

Quando você implanta um modelo treinado em um serviço web ou dispositivo IoT Edge, um pacote é criado que contém um servidor web para lidar com solicitações recebidas.

O Azure Machine Learning fornece uma imagem base docker padrão para que você não tenha que se preocupar em criar uma. Você também pode usar __ambientes__ de Aprendizado de Máquina do Azure para selecionar uma imagem base específica ou usar uma personalizada que você fornece.

Uma imagem base é usada como ponto de partida quando uma imagem é criada para uma implantação. Fornece o sistema operacional e componentes subjacentes. O processo de implantação adiciona componentes adicionais, como seu modelo, ambiente conda e outros ativos, à imagem antes de implantá-la.

Normalmente, você cria uma imagem base personalizada quando deseja usar o Docker para gerenciar suas dependências, manter um controle mais rigoroso sobre as versões dos componentes ou economizar tempo durante a implantação. Por exemplo, você pode querer padronizar uma versão específica do Python, Conda ou outro componente. Você também pode querer instalar o software exigido pelo seu modelo, onde o processo de instalação leva muito tempo. Instalar o software ao criar a imagem base significa que você não precisa instalá-lo para cada implantação.

> [!IMPORTANT]
> Quando você implanta um modelo, não é possível substituir componentes principais, como o servidor web ou os componentes do IoT Edge. Esses componentes fornecem um ambiente de trabalho conhecido que é testado e suportado pela Microsoft.

> [!WARNING]
> A Microsoft pode não ser capaz de ajudar a solucionar problemas causados por uma imagem personalizada. Se você encontrar problemas, você pode ser solicitado a usar a imagem padrão ou uma das imagens que a Microsoft fornece para ver se o problema é específico para sua imagem.

Este documento é dividido em duas seções:

* Crie uma imagem base personalizada: fornece informações aos administradores e DevOps sobre a criação de uma imagem personalizada e a configuração da autenticação em um Registro de Contêineres Do Azure usando o CLI do Azure e o MACHINE Learning CLI.
* Implantar um modelo usando uma imagem base personalizada: Fornece informações aos cientistas de dados e engenheiros de DevOps / ML sobre o uso de imagens personalizadas ao implantar um modelo treinado do Python SDK ou ML CLI.

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de trabalho de aprendizado de máquina do Azure. Para obter mais informações, consulte o Criar um artigo [sobre o espaço de trabalho.](how-to-manage-workspace.md)
* O [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* A [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* A [extensão CLI para Aprendizado de Máquina do Azure](reference-azure-machine-learning-cli.md).
* Um [Registro de Contêiner do Azure](/azure/container-registry) ou outro registro docker que esteja acessível na internet.
* As etapas deste documento supõem que você está familiarizado com a criação e o uso de um objeto de configuração de __inferência__ como parte da implantação do modelo. Para obter mais informações, consulte a seção "prepare-se para implantar" de [Onde implantar e como](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-base-image"></a>Crie uma imagem base personalizada

As informações nesta seção supõem que você está usando um Registro de Contêineres do Azure para armazenar imagens do Docker. Use a lista de verificação a seguir ao planejar criar imagens personalizadas para o Azure Machine Learning:

* Você usará o Registro de Contêineres do Azure criado para o espaço de trabalho Azure Machine Learning ou um Registro de Contêineres Azure autônomo?

    Ao usar imagens armazenadas no registro de __contêineres para o espaço de trabalho,__ você não precisa autenticar no registro. A autenticação é tratada pelo espaço de trabalho.

    > [!WARNING]
    > O Registro de Contêineres Do Azure para o seu espaço de trabalho é __criado na primeira vez que você treina ou implanta um modelo__ usando o espaço de trabalho. Se você criou um novo espaço de trabalho, mas não treinou ou criou um modelo, nenhum Registro de Contêineres do Azure existirá para o espaço de trabalho.

    Para obter informações sobre como recuperar o nome do Registro de Contêineres do Azure para o seu espaço de trabalho, consulte a seção [Obter nome](#getname) de registro de contêiner deste artigo.

    Ao usar imagens armazenadas em um __registro de contêiner autônomo,__ você precisará configurar um diretor de serviço que tenha pelo menos acesso à leitura. Em seguida, você fornece o ID principal do serviço (nome de usuário) e senha para qualquer um que use imagens do registro. A exceção é se você tornar o registro de contêineres acessível publicamente.

    Para obter informações sobre a criação de um Registro privado de contêineres do Azure, consulte [Criar um registro privado de contêiner](/azure/container-registry/container-registry-get-started-azure-cli).

    Para obter informações sobre o uso de princípios de serviço com o Registro de Contêineres do Azure, consulte [a autenticação do Registro de Contêineres do Azure com os princípios do serviço](/azure/container-registry/container-registry-auth-service-principal).

* Registro de contêineres do Azure e informações de imagem: Forneça o nome da imagem para qualquer pessoa que precise usá-la. Por exemplo, uma `myimage`imagem nomeada , `myregistry`armazenada em `myregistry.azurecr.io/myimage` um registro chamado , é referenciada como ao usar a imagem para implantação do modelo

* Requisitos de imagem: O Azure Machine Learning só suporta imagens Docker que fornecem o seguinte software:

    * Ubuntu 16.04 ou superior.
    * Conda 4.5.# ou superior.
    * Python 3.5.# ou 3.6.#.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Obtenha informações de registro de contêineres

Nesta seção, saiba como obter o nome do Registro de Contêineres do Azure para o seu espaço de trabalho azure Machine Learning.

> [!WARNING]
> O Registro de Contêineres Do Azure para o seu espaço de trabalho é __criado na primeira vez que você treina ou implanta um modelo__ usando o espaço de trabalho. Se você criou um novo espaço de trabalho, mas não treinou ou criou um modelo, nenhum Registro de Contêineres do Azure existirá para o espaço de trabalho.

Se você já treinou ou implantou modelos usando o Azure Machine Learning, um registro de contêiner foi criado para o seu espaço de trabalho. Para encontrar o nome deste registro de contêiner, use as seguintes etapas:

1. Abra um novo shell ou prompt de comando e use o seguinte comando para autenticar a sua assinatura do Azure:

    ```azurecli-interactive
    az login
    ```

    Siga as instruções para autenticar a assinatura.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Use o comando a seguir para listar o registro de contêiner para o espaço de trabalho. Substitua pelo `<myworkspace>` nome do espaço de trabalho azure Machine Learning. Substitua pelo `<resourcegroup>` grupo de recursos Do Azure que contém seu espaço de trabalho:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    As informações retornadas são semelhantes ao seguinte texto:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    O `<registry_name>` valor é o nome do Registro de Contêineres do Azure para o seu espaço de trabalho.

### <a name="build-a-custom-base-image"></a>Construa uma imagem base personalizada

As etapas desta seção passo-through criando uma imagem Docker personalizada em seu Registro de Contêiner do Azure.

1. Crie um novo `Dockerfile`arquivo de texto chamado e use o seguinte texto como conteúdo:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. A partir de um shell ou prompt de comando, use o seguinte para autenticar no Registro de Contêineres do Azure. Substitua-o `<registry_name>` pelo nome do registro de contêiner em que deseja armazenar a imagem:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Para carregar o arquivo Docker e construí-lo, use o seguinte comando. Substitua pelo `<registry_name>` nome do registro de contêiner que deseja armazenar a imagem:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > Neste exemplo, uma `:v1` tag of é aplicada à imagem. Se nenhuma tag for fornecida, `:latest` uma tag of é aplicada.

    Durante o processo de compilação, as informações são transmitidas para trás para a linha de comando. Se a compilação for bem sucedida, você receberá uma mensagem semelhante ao seguinte texto:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Para obter mais informações sobre a construção de imagens com um Registro de Contêiner do Azure, consulte [Construir e executar uma imagem de contêiner usando tarefas de registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Para obter mais informações sobre o upload de imagens existentes para um Registro de Contêineres do Azure, consulte [Empurre sua primeira imagem para um registro privado de contêiner Docker](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-base-image"></a>Use uma imagem base personalizada

Para usar uma imagem personalizada, você precisa das seguintes informações:

* O __nome da imagem.__ Por exemplo, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` é o caminho para uma imagem docker básica fornecida pela Microsoft.

    > [!IMPORTANT]
    > Para imagens personalizadas que você criou, certifique-se de incluir quaisquer tags que foram usadas com a imagem. Por exemplo, se sua imagem foi criada com `:v1`uma tag específica, como . Se você não usou uma tag específica ao `:latest` criar a imagem, uma tag de foi aplicada.

* Se a imagem estiver em um __repositório privado,__ você precisará das seguintes informações:

    * O __endereço__do registro . Por exemplo, `myregistry.azureecr.io`.
    * Um nome de usuário principal __do__ serviço e __senha__ que tenha lido acesso ao registro.

    Se você não tiver essas informações, fale com o administrador do Registro de Contêineres do Azure que contém sua imagem.

### <a name="publicly-available-base-images"></a>Imagens base disponíveis publicamente

A Microsoft fornece várias imagens docker em um repositório acessível ao público, que pode ser usado com as etapas nesta seção:

| Imagem | Descrição |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Imagem básica para Aprendizado de Máquina do Azure |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Contém tempo de execução ONNX para inferência da CPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Contém o TEMPO de execução ONNX e CUDA para GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Contém TEMPO de execução ONNX e TensorRT para GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Contém ONNX Runtime e OpenVINO para Intel<sup> </sup> Vision Accelerator Design baseado em VPUs Movidius<sup>TM</sup> MyriadX |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Contém ONNX Runtime e OpenVINO para pendrives Intel<sup> </sup> Movidius<sup>TM</sup> |

Para obter mais informações sobre as imagens base do ONNX Runtime, consulte a seção de arquivo docker do [ONNX Runtime](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) no repo do GitHub.

> [!TIP]
> Uma vez que essas imagens estão disponíveis publicamente, você não precisa fornecer um endereço, nome de usuário ou senha ao usá-las.

Para obter mais informações, consulte [os recipientes de Machine Learning do Azure](https://github.com/Azure/AzureML-Containers).

> [!TIP]
>__Se o seu modelo for treinado no Azure Machine Learning Compute__, usando a __versão 1.0.22 ou superior__ do Azure Machine Learning SDK, uma imagem será criada durante o treinamento. Para descobrir o nome desta `run.properties["AzureML.DerivedImageName"]`imagem, use . O exemplo a seguir demonstra como usar esta imagem:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Use uma imagem com o Azure Machine Learning SDK

Para usar uma imagem armazenada no Registro de Contêineres do **Azure para o seu espaço de trabalho**ou um registro de contêiner **acessível publicamente,** defina os seguintes atributos [de ambiente:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)

+ `docker.enabled=True`
+ `docker.base_image`: Definido no registro e caminho para a imagem.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Para usar uma imagem de um __registro de contêiner privado__ `docker.base_image_registry` que não está em seu espaço de trabalho, você deve usar para especificar o endereço do repositório e um nome de usuário e senha:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

Você deve adicionar azureml-defaults com a versão >= 1.0.45 como uma dependência de pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web. Você também deve definir inferencing_stack_version propriedade no ambiente para "mais recente", isso instalará pacotes apt específicos necessários pelo serviço web. 

Depois de definir o ambiente, use-o com um objeto [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) para definir o ambiente de inferência no qual o modelo e o serviço web serão executados.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Neste ponto, você pode continuar com a implantação. Por exemplo, o seguinte trecho de código implantaria um serviço web localmente usando a configuração de inferência e imagem personalizada:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações sobre a implantação, consulte [Implantar modelos com o Azure Machine Learning](how-to-deploy-and-where.md).

Para obter mais informações sobre a personalização do ambiente Python, consulte [Criar e gerenciar ambientes para treinamento e implantação.](how-to-use-environments.md) 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Use uma imagem com a CLI machine learning

> [!IMPORTANT]
> Atualmente, a CLI de aprendizado de máquina pode usar imagens do Registro de Contêineres do Azure para o seu espaço de trabalho ou repositórios acessíveis ao público. Ele não pode usar imagens de registros privados autônomos.

Antes de implantar um modelo usando o CLI machine learning, crie um [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) que use a imagem personalizada. Em seguida, crie um arquivo de configuração de inferência que faça referência ao ambiente. Você também pode definir o ambiente diretamente no arquivo de configuração de inferência. O documento JSON a seguir demonstra como referenciar uma imagem em um registro de contêiner público. Neste exemplo, o ambiente é definido inline:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Este arquivo é `az ml model deploy` usado com o comando. O `--ic` parâmetro é usado para especificar o arquivo de configuração de inferência.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Para obter mais informações sobre a implantação de um modelo usando o ML CLI, consulte a seção "registro de modelo, criação de perfil e implantação" da extensão CLI para o artigo [Azure Machine Learning.](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [onde implantar e como](how-to-deploy-and-where.md).
* Aprenda a [treinar e implantar modelos de aprendizado de máquina usando pipelines Azure](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
