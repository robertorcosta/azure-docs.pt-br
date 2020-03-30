---
title: Visualizar experimentos com o TensorBoard
titleSuffix: Azure Machine Learning
description: Lance o TensorBoard para visualizar históricos de execução de experimentos e identificar áreas potenciais para ajuste e retreinamento de hiperparâmetros.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195372"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualize as corridas e métricas do experimento com tensorboard e aprendizado de máquina do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende a ver suas corridas e métricas de experimentos no TensorBoard usando [o `tensorboard` pacote](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) no Principal Azure Machine Learning SDK. Depois de inspecionar suas corridas de experimento, você pode ajustar e retreinar seus modelos de aprendizado de máquina.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) é um conjunto de aplicações web para inspecionar e entender sua estrutura e desempenho do experimento.

Como você lança o TensorBoard com experimentos de Machine Learning do Azure depende do tipo de experimento:
+ Se o experimento de produção de sacências nativas de arquivos de log que são consumíveis pelo TensorBoard, como experimentos PyTorch, Chainer e TensorFlow, então você pode [iniciar o TensorBoard diretamente](#direct) do histórico de execução do experimento. 

+ Para experimentos que não produzirem nativamente arquivos consumíveis do TensorBoard, como experimentos de Aprendizagem de Máquina scikit ou Azure, use [o `export_to_tensorboard()` método](#export) para exportar os históricos de execução como logs tensorBoard e lançar tensorboard a partir daí. 

> [!TIP]
> As informações deste documento são principalmente para cientistas de dados e desenvolvedores que desejam monitorar o processo de treinamento do modelo. Se você é um administrador interessado em monitorar o uso de recursos e eventos do Azure Machine learning, como cotas, corridas de treinamento concluídas ou implantações de modelos concluídas, consulte Monitorando o Aprendizado de [Máquina do Azure](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos

* Para lançar o TensorBoard e visualizar seus históricos de execução de experimentos, seus experimentos precisam ter ativado anteriormente o registro para rastrear suas métricas e desempenho.  

* O código neste documento pode ser executado em qualquer um dos seguintes ambientes: 

    * Instância de computação do Azure Machine Learning - sem downloads ou instalação necessária

        * Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de amostras.

        * Na pasta de amostras no servidor de notebook, encontre dois notebooks concluídos e expandidos navegando para esses diretórios:
            * **como usar-azureml > treinamento-com-aprendizado profundo > export-run-history-to-tensorboard > export-run-history-to-tensorboard.ipynb**

            * **como usar-azureml > experiências de pista e monitor > tensorboard.ipynb**

    * Seu próprio servidor de notebook Juptyer
       * [Instale o Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) com o `tensorboard` extra
        * [Crie um espaço de trabalho de aprendizado de máquina do Azure](how-to-manage-workspace.md).  
        * [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opção 1: exibir diretamente o histórico de execução no TensorBoard

Essa opção funciona para experimentos que inconformidades com arquivos de log consumíveis pelo TensorBoard, como experimentos PyTorch, Chainer e TensorFlow. Se esse não for o caso do seu experimento, use [o `export_to_tensorboard()` método](#export) em vez disso.

O código de exemplo a seguir usa o [experimento de demonstração MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) do repositório do TensorFlow em um alvo remoto de computação, o Azure Machine Learning Compute. Em seguida, treinamos nosso modelo com o [estimador tensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)personalizado do SDK e, em seguida, iniciamos o TensorBoard contra este experimento TensorFlow, ou seja, um experimento que produz nativamente arquivos de eventos TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Defina o nome do experimento e crie pasta de projeto

Aqui nós nomeamos o experimento e criamos sua pasta. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Baixar o código de experimento de demonstração do TensorFlow

O repositório do TensorFlow tem uma demonstração MNIST com extensa instrumentação TensorBoard. Não alteramos nenhum código desta demonstração para que ele funcione com o Azure Machine Learning. No código a seguir, baixamos o código MNIST e o salvamos em nossa pasta de experimentos recém-criada.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Através do arquivo de código MNIST, mnist_with_summaries.py, `tf.summary.scalar()` `tf.summary.histogram()`observe `tf.summary.FileWriter()` que existem linhas que chamam , etc. Esses métodos agrupam, registram e marcam as métricas-chave de seus experimentos no histórico de execução. O `tf.summary.FileWriter()` é especialmente importante, pois serializa os dados de suas métricas de experimento sacadas, o que permite que o TensorBoard gere visualizações fora deles.

 ### <a name="configure-experiment"></a>Configurar o experimento

A seguir, configuramos nosso experimento e configuramos diretórios para registros e dados. Esses logs serão carregados no Serviço de Artefatos, que o TensorBoard acessa mais tarde.

>[!Note]
> Para este exemplo tensorFlow, você precisará instalar tensorFlow em sua máquina local. Além disso, o módulo TensorBoard (ou seja, o incluído no TensorFlow) deve ser acessível ao kernel deste notebook, já que a máquina local é o que executa o TensorBoard.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Crie um cluster para sua experiência
Criamos um cluster AmlCompute para este experimento, no entanto seus experimentos podem ser criados em qualquer ambiente e você ainda é capaz de lançar tensorBoard contra o histórico de execução do experimento. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>Enviar execução com o estimador TensorFlow

O estimador TensorFlow fornece uma maneira simples de iniciar um trabalho de treinamento TensorFlow em um alvo de computação. É implementado através da [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe genérica, que pode ser usado para apoiar qualquer estrutura. Para obter mais informações sobre modelos de treinamento usando o estimador genérico, consulte [modelos de trem com a azure Machine Learning usando estimador](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Iniciar TensorBoard

Você pode iniciar o TensorBoard durante a sua execução ou depois que ele for concluído. No seguinte, criamos uma instância de `tb`objeto TensorBoard, que pega `run`o histórico de execução do `start()` experimento carregado no , e, em seguida, lança TensorBoard com o método. 
  
O [construtor TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) faz uma série de corridas, então certifique-se de passá-la como uma matriz de elemento único.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 Embora este exemplo tenha usado TensorFlow, tensorBoard pode ser usado tão facilmente com os modelos PyTorch ou Chainer. TensorFlow deve estar disponível na máquina que executa tensorBoard, mas não é necessário na máquina fazendo cálculos PyTorch ou Chainer. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opção 2: Histórico de exportação como log para exibição no TensorBoard

O código a seguir configura um experimento de amostra, inicia o processo de registro usando as APIs de histórico de execução do Azure Machine Learning e exporta o histórico de execução do experimento em logs consumíveis pela TensorBoard para visualização. 

### <a name="set-up-experiment"></a>Configurar experimento

O código a seguir configura um novo `root_run`experimento e nomeia o diretório em execução . 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Aqui nós carregamos o conjunto de dados do diabetes- um pequeno conjunto de dados embutido que vem com scikit-learn, e dividi-lo em conjuntos de teste e treinamento.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Executar métricas de experimento e log

Para este código, treinamos um modelo de regressão linear e `alpha`registramos métricas-chave, o coeficiente alfa, e o erro quadrado médio, `mse`no histórico de execução.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Exportação é executada para TensorBoard

Com o método [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) do SDK, podemos exportar o histórico de execução do nosso experimento de aprendizado de máquina Do Zure para logs tensorBoard, para que possamos visualizá-los via TensorBoard.  

No código a seguir, `logdir` criamos a pasta em nosso diretório de trabalho atual. Esta pasta é onde exportaremos nosso histórico `root_run` de execução de experimentos e logs de e, em seguida, marcar que executar como concluído. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 Você também pode exportar uma execução específica para tensorBoard especificando o nome da execução`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Inicie e pare tensorboard
Uma vez que nosso histórico de execução para este experimento é exportado, podemos lançar tensorboard com o método [de início().](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Quando terminar, certifique-se de chamar o método [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) do objeto TensorBoard. Caso contrário, o TensorBoard continuará a ser executado até que você desligue o kernel do notebook. 

```python
tb.stop()
```

## <a name="next-steps"></a>Próximas etapas

Neste como você, criei dois experimentos e aprendi como lançar o TensorBoard contra seus históricos de execução para identificar áreas para potencial ajuste e retreinamento. 

* Se você está satisfeito com o seu modelo, vá até o nosso Como implantar um artigo [modelo.](how-to-deploy-and-where.md) 
* Saiba mais sobre [a sintonia do hiperparâmetro](how-to-tune-hyperparameters.md). 
