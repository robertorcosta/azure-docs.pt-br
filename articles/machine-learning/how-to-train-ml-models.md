---
title: Treinar modelos ML com estimadores
titleSuffix: Azure Machine Learning
description: Aprenda a realizar treinamento sumido e distribuído de aprendizado de máquina tradicional e modelos de aprendizagem profunda usando a aula de Estimator de Aprendizado de Máquina do Azure
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: a9a3c10687b92d946fccb282f2eda4af637cf2ab
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686765"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Treinar modelos com o Azure Machine Learning usando o estimador
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Com o Azure Machine Learning, você pode enviar facilmente seu script de treinamento para [vários alvos de computação,](how-to-set-up-training-targets.md#compute-targets-for-training)usando um [objeto RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) e um [objeto ScriptRunConfig](how-to-set-up-training-targets.md#submit). Esse padrão oferece a você muita flexibilidade e o máximo controle.

Para facilitar o treinamento do modelo de aprendizado profundo, o SDK do Python do Azure Machine Learning oferece uma abstração alternativa de nível mais alto, a classe do estimador, que permite que os usuários construam facilmente configurações de execução. Você pode criar e usar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para enviar script de treinamento usando qualquer estrutura de aprendizado que você escolher (como scikit-learn) em qualquer alvo de computação que você escolher, seja sua máquina local, um único VM no Azure ou um cluster de GPU no Azure. Para as tarefas PyTorch, TensorFlow e Chainer, o Azure Machine Learning também fornece os respectivos estimadores [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar o uso dessas frameworks.

## <a name="train-with-an-estimator"></a>Treinar com um estimador

Depois de criar seu [Workspace](concept-workspace.md) e configurar seu [ambiente de desenvolvimento](how-to-configure-environment.md), o treinamento de um modelo no Machine Learning do Azure envolve as seguintes etapas:  
1. Criar um [destino de computação remoto](how-to-set-up-training-targets.md) (observe que também é possível usar o computador local como destino de computação)
2. Carregar seus [dados de treinamento](how-to-access-data.md) para o repositório de dados (opcional)
3. Criar seu [script de treinamento](tutorial-train-models-with-aml.md#create-a-training-script)
4. Criar um `Estimator` objeto
5. Enviar o estimador para um objeto de teste no workspace

Este artigo se concentra nas etapas 4 a 5. Para as etapas 1 a 3, consulte o [tutorial treinar um modelo](tutorial-train-models-with-aml.md) para obter um exemplo.

### <a name="single-node-training"></a>Treinamento de nó único

Use um `Estimator` para um treinamento de nó único executado em computação remota no Azure para um modelo scikit-learn. Você já deveria ter criado seu `compute_target` objeto de destino `ds`de [computação](how-to-set-up-training-targets.md#amlcompute) e seu objeto [FileDataset](how-to-create-register-datasets.md) .

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Este snippet de código especifica os seguintes parâmetros para o `Estimator` construtor.

Parâmetro | Descrição
--|--
`source_directory`| O diretório local que contém todo o código necessário para o trabalho de treinamento. Esta pasta é copiada da máquina local para a computação remota.
`script_params`| Dicionário especificando os argumentos de linha de `entry_script`comando para `<command-line argument, value>` passar para o seu script de treinamento, na forma de pares. Para especificar uma bandeira `script_params`verbose em , use `<command-line argument, "">`.
`compute_target`| O destino de computação remoto no qual seu script de treinamento será executado, neste caso, um cluster ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) da Computação do Azure Machine Learning. (Observe que mesmo que o cluster AmlCompute seja o alvo comumente usado, também é possível escolher outros tipos de destino de computação, como VMs do Azure ou mesmo computador local.)
`entry_script`| FilePath (relativo ao `source_directory`) do script de treinamento para ser executado na computação remota. Este arquivo, e quaisquer arquivos adicionais de que ele depende, deve ser localizado nesta pasta.
`conda_packages`| A lista de pacotes do Python a serem instalados via conda, necessária ao seu script de treinamento.  

O construtor tem outro `pip_packages` parâmetro chamado que você usa para quaisquer pacotes pip necessários.

Agora que você criou seu `Estimator` de objeto, envie o trabalho de treinamento para ser executado na computação remota com uma chamada para a `submit` função no seu objeto [Experimento](concept-azure-machine-learning-architecture.md#experiments)`experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Pastas Especiais** Duas pastas, *produzem* e *logs*, recebem tratamento especial do Aprendizado de Máquina do Azure. Durante o treinamento, ao gravar arquivos em pastas denominadas *gera* e *logs* que são relativos ao diretório raiz (`./outputs` e `./logs`, respectivamente), os arquivos serão automaticamente Carregue o histórico de execução para que você tenha acesso a eles quando sua execução for concluída.
>
> Para criar artefatos durante o treinamento (como arquivos de modelo, os pontos de verificação, arquivos de dados ou imagens plotadas) escreva para a pasta `./outputs`.
>
> Da mesma forma, você pode escrever todos os logs da sua corrida de treinamento para a pasta `./logs`. Para utilizar a integração [TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) do Azure Machine Learning, certifique-se de gravar seus logs TensorBoard nessa pasta. Enquanto a sua corrida estiver em andamento, você poderá lançar o TensorBoard e transmitir esses logs.  Mais tarde, você também poderá restaurar os logs de qualquer uma das execuções anteriores.
>
> Por exemplo, para fazer o download de um arquivo gravado na pasta *de saídas* para sua máquina local após a execução do seu treinamento remoto: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Treinamento distribuído e imagens personalizadas do Docker

Há dois cenários de treinamento adicional, você pode realizar com o `Estimator`:
* Usando uma imagem do Docker personalizada
* Treinamento distribuído em um cluster com vários nó

O código a seguir mostra como realizar o treinamento distribuído para um modelo Keras. Além disso, em vez de usar as imagens padrão do Azure Machine Learning, ele especifica uma imagem personalizada do docker do Hub do Docker `continuumio/miniconda` para treinamento.

Você já deve ter criado seu [destino de computação](how-to-set-up-training-targets.md#amlcompute) objeto `compute_target`. Você pode criar o avaliador da seguinte maneira:

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

O código acima expõe os seguintes novos parâmetros para o `Estimator` construtor:

Parâmetro | Descrição | Padrão
--|--|--
`custom_docker_image`| O nome da imagem que você deseja usar. Fornece somente as imagens disponíveis em repositórios do docker público (no Hub do Docker neste caso). Para usar uma imagem de um repositório privado do docker, use o parâmetro `environment_definition` do construtor em vez disso. [Confira o exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| O número de nós a serem usados no seu trabalho de treinamento. | `1`
`process_count_per_node`| Número de processos (ou "trabalhos") a serem executado em cada nó. Nesse caso, você usa o `2` GPUs disponíveis em cada nó.| `1`
`distributed_training`| [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) object for launching distributed training using MPI backend.  | `None`


Por fim, envie o trabalho de treinamento:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Registro de um modelo

Uma vez treinado o modelo, você pode salvá-lo e registrá-lo no seu espaço de trabalho. O registro de modelos permite que você armazene e venda seus modelos em seu espaço de trabalho para simplificar o [gerenciamento e a implantação do modelo.](concept-model-management-and-deployment.md)

A execução do código a seguir registrará o modelo no seu espaço de trabalho e o disponibilizará para referência por nome em contextos de computação remota ou scripts de implantação. Consulte [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) nos docs de referência para obter mais informações e parâmetros adicionais.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>Rastreamento e integração do GitHub

Quando você inicia uma corrida de treinamento onde o diretório de origem é um repositório git local, as informações sobre o repositório são armazenadas no histórico de execução. Para obter mais informações, consulte [a integração Git para Aprendizado de Máquina do Azure](concept-train-model-git-integration.md).

## <a name="examples"></a>Exemplos
Para um notebook que mostra o básico de um padrão estimador, veja:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Para um notebook que treina um modelo de scikit-learn usando estimador, veja:
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Para notebooks sobre modelos de treinamento usando estimadores específicos de estrutura de aprendizagem profunda, consulte:

* [como usar-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Treinar modelos de PyTorch](how-to-train-pytorch.md)
* [Treinar modelos de TensorFlow](how-to-train-tensorflow.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
* [Crie e gerencie ambientes para treinamento e implantação](how-to-use-environments.md)