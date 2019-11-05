---
title: Treinar modelos ML com estimadores
titleSuffix: Azure Machine Learning
description: Saiba como executar um único nó e treinamento distribuído de aprendizado de máquina tradicional e modelos de aprendizado profundo usando Azure Machine Learning classe de estimador
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: bb3b9504abcd453977d63a9bfccf77a33da6455a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489474"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Treinar modelos com o Azure Machine Learning usando o estimador
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Com o Azure Machine Learning, é possível enviar facilmente seu script de treinamento para [vários destinos de computação](how-to-set-up-training-targets.md#compute-targets-for-training), usando o [objeto RunConfiguration](how-to-set-up-training-targets.md#whats-a-run-configuration) e o [objeto ScriptRunConfig](how-to-set-up-training-targets.md#submit). Esse padrão oferece a você muita flexibilidade e o máximo controle.

Para facilitar o treinamento do modelo de aprendizado profundo, o SDK do Python do Azure Machine Learning oferece uma abstração alternativa de nível mais alto, a classe do estimador, que permite que os usuários construam facilmente configurações de execução. Você pode criar e usar um [estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para enviar o script de treinamento usando qualquer estrutura de aprendizado que escolher (como scikit-learn) em qualquer destino de computação escolhido, seja seu computador local, uma única VM no Azure ou um cluster de GPU no Azure. Para as tarefas PyTorch, TensorFlow e Chainer, Azure Machine Learning também fornece os respectivos estimadores [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar o uso dessas estruturas.

## <a name="train-with-an-estimator"></a>Treinar com um estimador

Depois de criar seu [Workspace](concept-workspace.md) e configurar seu [ambiente de desenvolvimento](how-to-configure-environment.md), o treinamento de um modelo no Machine Learning do Azure envolve as seguintes etapas:  
1. Criar um [destino de computação remoto](how-to-set-up-training-targets.md) (observe que também é possível usar o computador local como destino de computação)
2. Carregar seus [dados de treinamento](how-to-access-data.md) para o repositório de dados (opcional)
3. Criar seu [script de treinamento](tutorial-train-models-with-aml.md#create-a-training-script)
4. Criar um `Estimator` objeto
5. Enviar o estimador para um objeto de teste no workspace

Este artigo se concentra nas etapas 4 a 5. Para as etapas 1 a 3, consulte o [tutorial treinar um modelo](tutorial-train-models-with-aml.md) para obter um exemplo.

### <a name="single-node-training"></a>Treinamento de nó único

Use um `Estimator` para um treinamento de nó único executado em computação remota no Azure para um modelo scikit-learn. Você já deve ter criado seu [destino de computação](how-to-set-up-training-targets.md#amlcompute) objeto `compute_target` e sua [datastore](how-to-access-data.md) objeto `ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Este snippet de código especifica os seguintes parâmetros para o `Estimator` construtor.

Parâmetro | DESCRIÇÃO
--|--
`source_directory`| O diretório local que contém todo o código necessário para o trabalho de treinamento. Essa pasta é copiada do computador local para a computação remota.
`script_params`| Dicionário que especifica os argumentos de linha de comando a serem passados para o script de treinamento `entry_script`, na forma de pares de `<command-line argument, value>`. Para especificar um sinalizador detalhado em `script_params`, use `<command-line argument, "">`.
`compute_target`| O destino de computação remoto no qual seu script de treinamento será executado, neste caso, um cluster ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) da Computação do Azure Machine Learning. (Observe que, embora o cluster AmlCompute seja o destino comumente usado, também é possível escolher outros tipos de destino de computação, como VMs do Azure ou mesmo computador local.)
`entry_script`| FilePath (relativo ao `source_directory`) do script de treinamento para ser executado na computação remota. Esse arquivo e os arquivos adicionais dos quais ele depende devem estar localizados nessa pasta.
`conda_packages`| A lista de pacotes do Python a serem instalados via conda, necessária ao seu script de treinamento.  

O construtor tem outro parâmetro chamado `pip_packages` que você usa para qualquer pacote de Pip necessário.

Agora que você criou seu `Estimator` de objeto, envie o trabalho de treinamento para ser executado na computação remota com uma chamada para a `submit` função no seu objeto [Experimento](concept-azure-machine-learning-architecture.md#experiments) `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Pastas Especiais** Duas pastas, *produzem* e *logs*, recebem tratamento especial do Aprendizado de Máquina do Azure. Durante o treinamento, ao gravar arquivos em pastas denominadas *gera* e *logs* que são relativos ao diretório raiz (`./outputs` e `./logs`, respectivamente), os arquivos serão automaticamente Carregue o histórico de execução para que você tenha acesso a eles quando sua execução for concluída.
>
> Para criar artefatos durante o treinamento (como arquivos de modelo, os pontos de verificação, arquivos de dados ou imagens plotadas) escreva para a pasta `./outputs`.
>
> Da mesma forma, você pode escrever todos os logs da sua corrida de treinamento para a pasta `./logs`. Para utilizar a integração [TensorBoard](https://aka.ms/aml-notebook-tb) do Azure Machine Learning, certifique-se de gravar seus logs TensorBoard nessa pasta. Enquanto a sua corrida estiver em andamento, você poderá lançar o TensorBoard e transmitir esses logs.  Mais tarde, você também poderá restaurar os logs de qualquer uma das execuções anteriores.
>
> Por exemplo, para fazer o download de um arquivo gravado na pasta *de saídas* para sua máquina local após a execução do seu treinamento remoto: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Treinamento distribuído e imagens personalizadas do Docker

Há dois cenários de treinamento adicional, você pode realizar com o `Estimator`:
* Usando uma imagem do Docker personalizada
* Treinamento distribuído em um cluster com vários nó

O código a seguir mostra como realizar o treinamento distribuído para um modelo Keras. Além disso, em vez de usar as imagens padrão do Azure Machine Learning, ele especifica uma imagem personalizada do docker do Hub do Docker `continuumio/miniconda` para treinamento.

Você já deve ter criado seu objeto de [destino de computação](how-to-set-up-training-targets.md#amlcompute)`compute_target`. Você pode criar o avaliador da seguinte maneira:

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

Parâmetro | DESCRIÇÃO | Padrão
--|--|--
`custom_docker_image`| O nome da imagem que você deseja usar. Fornece somente as imagens disponíveis em repositórios do docker público (no Hub do Docker neste caso). Para usar uma imagem de um repositório privado do docker, use o parâmetro `environment_definition` do construtor em vez disso. [Confira o exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| O número de nós a serem usados no seu trabalho de treinamento. | `1`
`process_count_per_node`| Número de processos (ou "trabalhos") a serem executado em cada nó. Nesse caso, você usa o `2` GPUs disponíveis em cada nó.| `1`
`distributed_training`| Objeto [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) para iniciar o treinamento distribuído usando o back-end MPI.  | `None`


Por fim, envie o trabalho de treinamento:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>Acompanhamento e integração do GitHub

Quando você inicia uma execução de treinamento onde o diretório de origem é um repositório git local, as informações sobre o repositório são armazenadas no histórico de execuções. Para obter mais informações, consulte [integração do git para Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="examples"></a>Exemplos
Para um notebook que mostra as noções básicas de um padrão estimador, consulte:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Para um bloco de anotações que treina um modelo scikit usando o estimador, consulte:
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Para blocos de anotações em modelos de treinamento usando avaliadores específicos da estrutura de aprendizado profundo, consulte:
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* [Executar as métricas de durante o treinamento de faixa](how-to-track-experiments.md)
* [Treinar modelos de PyTorch](how-to-train-pytorch.md)
* [Treinar modelos de TensorFlow](how-to-train-tensorflow.md)
* [Ajustar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implantar um modelo treinado](how-to-deploy-and-where.md)
