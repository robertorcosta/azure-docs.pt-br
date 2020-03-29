---
title: Treinar modelos de aprendizagem de máquina scikit-learn
titleSuffix: Azure Machine Learning
description: Aprenda a executar seus scripts de treinamento scikit-learn em escala corporativa usando a classe estimador Azure Machine Learning SKlearn. Os scripts de exemplo classificam as imagens de flores de íris para construir um modelo de aprendizado de máquina baseado no conjunto de dados de íris do scikit-learn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: bdd2cc400c3df75742689258caea8cb87ee8ccc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942249"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Construa modelos de aprendizado de scikit em escala com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprenda a executar seus scripts de treinamento scikit-learn em escala corporativa usando a classe estimador Azure Machine Learning [SKlearn.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 

Os scripts de exemplo deste artigo são usados para classificar imagens de flores de íris para construir um modelo de aprendizado de máquina baseado no conjunto de [dados de íris](https://archive.ics.uci.edu/ml/datasets/iris)do scikit-learn .

Se você está treinando um modelo de aprendizado de máquina scikit-learn a partir do zero ou você está trazendo um modelo existente para a nuvem, você pode usar o Azure Machine Learning para dimensionar trabalhos de treinamento de código aberto usando recursos elásticos de computação em nuvem. Você pode construir, implantar, versá-lo e monitorar modelos de nível de produção com o Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um desses ambientes:
 - Instância de computação do Azure Machine Learning - sem downloads ou instalação necessária

    - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de amostras.
    - Na pasta de treinamento de amostras no servidor de notebook, encontre um notebook completo e expandido navegando para este diretório: **como usar-azureml > de ml-frameworks > scikit-learn > treinamento > pasta train-hyperparameter-tune-deploy-with-sklearn.**

 - Seu próprio servidor Jupyter Notebook

    - [Instale o Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
    - Baixe o conjunto de dados e o arquivo de script de exemplo 
        - [iris conjunto de dados](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) deste guia na página de amostras do GitHub. O notebook inclui uma seção expandida cobrindo a sintonia inteligente do hiperparâmetro e recuperando o melhor modelo por métricas primárias.

## <a name="set-up-the-experiment"></a>Configure o experimento

Esta seção configura o experimento de treinamento carregando os pacotes python necessários, iniciando um espaço de trabalho, criando um experimento e carregando os dados de treinamento e scripts de treinamento.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas Python necessárias.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicialize um espaço de trabalho

O [espaço de trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Ele fornece um lugar centralizado para trabalhar com todos os artefatos que você cria. No Python SDK, você pode acessar os artefatos [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) do espaço de trabalho criando um objeto.

Crie um objeto de `config.json` espaço de trabalho a partir do arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Criar um experimento de aprendizado de máquina

Crie um experimento e uma pasta para manter seus scripts de treinamento. Neste exemplo, crie um experimento chamado "sklearn-iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Preparar o script de treinamento

Neste tutorial, o script de treinamento **train_iris.py** já está fornecido para você. Na prática, você deve ser capaz de pegar qualquer script de treinamento personalizado como está e executá-lo com o Azure ML sem ter que modificar seu código.

Para usar os recursos de rastreamento e métricas do Azure ML, adicione uma pequena quantidade de código Azure ML dentro do seu script de treinamento.  O script de treinamento **train_iris.py** mostra como registrar algumas métricas `Run` para o seu Azure ML executado usando o objeto dentro do script.

O script de treinamento fornecido `iris = datasets.load_iris()` usa dados de exemplo da função.  Para seus próprios dados, você pode precisar usar etapas como [upload de conjunto de dados e scripts](how-to-train-keras.md#data-upload) para disponibilizar dados durante o treinamento.

Copie o script de treinamento **train_iris.py** em seu diretório de projeto.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Criar ou obter um alvo de computação

Crie um alvo de computação para o seu trabalho de aprender scikit para executar. Scikit-learn só suporta nó único, computação de CPU.

O código a seguir cria um Azure Machine Learning gerenciado computação (AmlCompute) para seu recurso de computação de treinamento remoto. A criação do AmlCompute leva aproximadamente 5 minutos. Se o AmlCompute com esse nome já estiver no seu espaço de trabalho, esse código pulará o processo de criação.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Para obter mais informações sobre metas de computação, consulte [o que é um artigo-alvo de computação.](concept-compute-target.md)

## <a name="create-a-scikit-learn-estimator"></a>Crie um estimador de scikit-learn

O [estimador scikit-learn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) fornece uma maneira simples de lançar um trabalho de treinamento scikit-learn em um alvo de computação. Ele é implementado [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) através da classe, que pode ser usado para suportar o treinamento de CPU de nó único.

Se o seu script de treinamento precisar de pacotes pip ou conda adicionais para executar, você `pip_packages` `conda_packages` pode ter os pacotes instalados na imagem do docker resultante, passando seus nomes através dos argumentos.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```


Para obter mais informações sobre a personalização do ambiente Python, consulte [Criar e gerenciar ambientes para treinamento e implantação.](how-to-use-environments.md) 

## <a name="submit-a-run"></a>Enviar uma corrida

O [objeto Executar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e depois de concluído.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

À medida que a execução é executada, ela passa pelas seguintes etapas:

- **Preparação**: Uma imagem de docker é criada de acordo com o estimador TensorFlow. A imagem é enviada para o registro de contêineres do espaço de trabalho e armazenada em cache para posteriores. Os registros também são transmitidos para o histórico de execução e podem ser visualizados para monitorar o progresso.

- **Dimensionamento**: O cluster tenta aumentar se o cluster De IA de lote exigir mais nós para executar a execução do que estão disponíveis atualmente.

- **Execução**: Todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o entry_script é executado. As saídas do stdout e da pasta ./logs são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: A pasta ./outputs da execução é copiada para o histórico de execução.

## <a name="save-and-register-the-model"></a>Salvar e registrar o modelo

Uma vez treinado o modelo, você pode salvá-lo e registrá-lo no seu espaço de trabalho. O registro de modelos permite que você armazene e venda seus modelos em seu espaço de trabalho para simplificar o [gerenciamento e a implantação do modelo.](concept-model-management-and-deployment.md)

Adicione o seguinte código ao seu script de treinamento, train_iris.py, para salvar o modelo. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registre o modelo no seu espaço de trabalho com o seguinte código. Ao especificar `model_framework`os `model_framework_version`parâmetros, e `resource_configuration`a implantação do modelo sem código fica disponível. Isso permite que você implante diretamente seu modelo como um [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) serviço web a partir do modelo registrado, e o objeto define o recurso de computação para o serviço web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Implantação

O modelo que você acabou de registrar pode ser implantado exatamente da mesma forma que qualquer outro modelo registrado no Azure Machine Learning, independentemente de qual estimador você usou para treinamento. A implantação de como fazer contém uma seção sobre modelos de registro, mas você pode pular diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implantação, uma vez que você já tem um modelo registrado.

### <a name="preview-no-code-model-deployment"></a>(Visualização) Implantação de modelo sem código

Em vez da rota de implantação tradicional, você também pode usar o recurso de implantação sem código (visualização) para o scikit-learn. A implantação de modelos sem código é suportada para todos os tipos de modelos de scikit-learn incorporados. Ao registrar seu modelo como `model_framework`mostrado `model_framework_version`acima `resource_configuration` com os parâmetros, você pode simplesmente usar a [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) função estática para implantar seu modelo.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

NOTA: Essas dependências estão incluídas no recipiente de inferência scikit-learn pré-construído.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

O [how-to](how-to-deploy-and-where.md) completo abrange a implantação no Azure Machine Learning em maior profundidade.


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você treinou e registrou um modelo de scikit-learn, e aprendeu sobre opções de implantação. Veja esses outros artigos para saber mais sobre o Azure Machine Learning.

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Arquitetura de referência para treinamento de deep learning distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
