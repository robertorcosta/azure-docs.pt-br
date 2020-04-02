---
title: Como e onde implantar modelos
titleSuffix: Azure Machine Learning
description: Saiba como e onde implantar seus modelos de Machine Learning do Azure, incluindo instâncias de contêiner do Azure, Serviço Azure Kubernetes, Borda IoT do Azure e conjuntos de porta programáveis em campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 02/27/2020
ms.custom: seoapril2019
ms.openlocfilehash: f6bab532b872a0974993f708edcb252d8bb54432
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529701"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Implantar modelos com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a implantar seu modelo de aprendizado de máquina como um serviço web na nuvem Do Zure ou em dispositivos Azure IoT Edge.

O fluxo de trabalho é semelhante, não importa [onde você implante](#target) seu modelo:

1. Registre o modelo.
1. Prepare-se para implantar. (Especifique os ativos, o uso e o destino de computação.)
1. Implante o modelo no destino de computação.
1. Teste o modelo implantado, também chamado de serviço web.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, consulte [Gerenciar, implantar e monitorar modelos com o Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho de aprendizado de máquina do Azure](how-to-manage-workspace.md).

- Um modelo. Se você não tem um modelo treinado, você pode usar os arquivos de modelo e dependência fornecidos [neste tutorial](https://aka.ms/azml-deploy-cloud).

- A [extensão Azure CLI para o serviço de Aprendizado de Máquina,](reference-azure-machine-learning-cli.md)o [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a [extensão Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="connect-to-your-workspace"></a>Conectar-se ao workspace

O código a seguir mostra como se conectar a um espaço de trabalho do Azure Machine Learning usando informações armazenadas em cache no ambiente de desenvolvimento local:

+ **Usar o SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Para obter mais informações sobre como usar o SDK para se conectar a um espaço de trabalho, consulte o [Azure Machine Learning SDK para](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) obter documentação Python.

+ **Usando a CLI**

   Ao usar o CLI, use o `-w` ou `--workspace-name` parâmetro para especificar o espaço de trabalho para o comando.

+ **Usar o Visual Studio Code**

   Quando você usa o Visual Studio Code, você seleciona o espaço de trabalho usando uma interface gráfica. Para obter mais informações, consulte [Implantar e gerenciar modelos](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) na documentação de extensão visual studio code.

## <a name="register-your-model"></a><a id="registermodel"></a>Registre seu modelo

Um modelo registrado é um recipiente lógico para um ou mais arquivos que compõem o seu modelo. Por exemplo, se você tem um modelo armazenado em vários arquivos, você pode registrá-los como um único modelo no espaço de trabalho. Depois de registrar os arquivos, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que você registrou.

> [!TIP]
> Quando você registra um modelo, você fornece o caminho de um local em nuvem (a partir de uma corrida de treinamento) ou de um diretório local. Este caminho é apenas para localizar os arquivos para upload como parte do processo de registro. Ele não precisa corresponder ao caminho usado no script de entrada. Para obter mais informações, consulte [Localizar arquivos de modelo em seu script de entrada](#load-model-files-in-your-entry-script).

Os modelos de aprendizado de máquina estão registrados no seu espaço de trabalho Azure Machine Learning. O modelo pode vir do Azure Machine Learning ou de outro lugar. Ao registrar um modelo, você pode fornecer metadados sobre o modelo. Os `tags` `properties` dicionários que você aplica a um registro de modelo podem ser usados para filtrar modelos.

Os exemplos a seguir demonstram como registrar um modelo.

### <a name="register-a-model-from-an-experiment-run"></a>Registre um modelo de uma execução de experimento

Os trechos de código nesta seção demonstram como registrar um modelo a partir de uma corrida de treinamento:

> [!IMPORTANT]
> Para usar esses trechos, você precisa ter realizado previamente uma corrida `Run` de treinamento e você precisa ter acesso ao objeto (exemplo sdk) ou ao valor de ID de execução (exemplo CLI). Para obter mais informações sobre modelos de treinamento, consulte [Configurar metas de computação para treinamento de modelos](how-to-set-up-training-targets.md).

+ **Usar o SDK**

  Quando você usa o SDK para treinar um modelo, você pode receber um objeto [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) ou um objeto [AutoMLRun,](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) dependendo de como você treinou o modelo. Cada objeto pode ser usado para registrar um modelo criado por um experimento executado.

  + Registre um modelo `azureml.core.Run` a partir de um objeto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    O `model_path` parâmetro refere-se à localização da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários arquivos no `model_path` registro do modelo, defina o caminho de uma pasta que contém os arquivos. Para obter mais informações, consulte a documentação [run.register_model.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)

  + Registre um modelo `azureml.train.automl.run.AutoMLRun` a partir de um objeto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    Neste exemplo, `metric` os `iteration` parâmetros e parâmetros não são especificados, de modo que a iteração com a melhor métrica primária será registrada. O `model_id` valor devolvido da execução é usado em vez de um nome de modelo.

    Para obter mais informações, consulte a documentação [AutoMLRun.register_model.](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)

+ **Usando a CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  O `--asset-path` parâmetro refere-se à localização da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários arquivos no `--asset-path` registro do modelo, defina o caminho de uma pasta que contém os arquivos.

+ **Usar o Visual Studio Code**

  Registre modelos usando quaisquer arquivos ou pastas de modelo usando a extensão [Visual Studio Code.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

### <a name="register-a-model-from-a-local-file"></a>Registre um modelo de um arquivo local

Você pode registrar um modelo fornecendo o caminho local do modelo. Você pode fornecer o caminho de uma pasta ou de um único arquivo. Você pode usar este método para registrar modelos treinados com o Azure Machine Learning e, em seguida, baixados. Você também pode usar este método para registrar modelos treinados fora do Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Usando o SDK e o ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Para incluir vários arquivos no `model_path` registro do modelo, defina o caminho de uma pasta que contém os arquivos.

+ **Usando a CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Para incluir vários arquivos no `-p` registro do modelo, defina o caminho de uma pasta que contém os arquivos.

**Estimativa de**tempo: Aproximadamente 10 segundos.

Para obter mais informações, consulte a documentação da [classe Modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Para obter mais informações sobre como trabalhar com modelos treinados fora do Azure Machine Learning, consulte [Como implantar um modelo existente](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Pontos finais únicos versus multimodelos
O Azure ML suporta a implantação de modelos únicos ou múltiplos atrás de um único ponto final.

Os pontos finais de vários modelos usam um contêiner compartilhado para hospedar vários modelos. Isso ajuda a reduzir os custos gerais, melhora a utilização e permite que você acorrente módulos em conjuntos. Os modelos especificados no script de implantação são montados e disponibilizados no disco do contêiner de serviço - você pode carregá-los na memória sob demanda e pontuar com base no modelo específico que está sendo solicitado na hora de pontuação.

Para um exemplo do E2E, que mostra como usar vários modelos atrás de um único ponto final contêiner, veja [este exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model)

## <a name="prepare-to-deploy"></a>Preparar-se para implantar

Para implantar o modelo como serviço, você precisa dos seguintes componentes:

* **Definir ambiente de inferência**. Este ambiente encapsula as dependências necessárias para executar seu modelo para inferência.
* **Definir código de pontuação**. Este script aceita solicitações, pontua as solicitações usando o modelo e retorna os resultados.
* **Definir configuração de inferência**. A configuração de inferência especifica a configuração do ambiente, o script de entrada e outros componentes necessários para executar o modelo como serviço.

Uma vez que você tenha os componentes necessários, você pode traçar o perfil do serviço que será criado como resultado da implantação do seu modelo para entender seus requisitos de CPU e memória.

### <a name="1-define-inference-environment"></a>1. Defina ambiente de inferência

Uma configuração de inferência descreve como configurar o serviço web que contém seu modelo. É usado mais tarde, quando você implantar o modelo.

A configuração de inferência usa ambientes de Aprendizado de Máquina do Azure para definir as dependências de software necessárias para sua implantação. Os ambientes permitem criar, gerenciar e reutilizar as dependências de software necessárias para treinamento e implantação. Você pode criar um ambiente a partir de arquivos de dependência personalizados ou usar um dos ambientes de aprendizado de máquina do Azure com curadoria. O YAML a seguir é um exemplo de um arquivo de dependências conda para inferência. Observe que você deve indicar azureml-defaults com verion >= 1.0.45 como uma dependência pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web. Se você quiser usar a geração automática de esquemas, seu script de entrada também deve importar os `inference-schema` pacotes.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Se sua dependência estiver disponível tanto através do Conda quanto do Pip (do PyPi), a Microsoft recomenda o uso da versão Conda, já que os pacotes Conda normalmente vêm com binários pré-construídos que tornam a instalação mais confiável.
>
> Para obter mais informações, consulte [Entendendo Conda e Pip](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Para verificar se sua dependência está disponível `conda search <package-name>` através do Conda, [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) use [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo)o comando ou use os índices do pacote em e .

Você pode usar o arquivo dependências para criar um objeto de ambiente e salvá-lo no seu espaço de trabalho para uso futuro:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. Defina o código de pontuação

O script de entrada recebe os dados enviados para um serviço Web implantado e os transmite ao modelo. Ele então envia de volta ao cliente a resposta retornada pelo modelo. *O script é específico para o seu modelo.* Ele deve entender os dados que o modelo espera e retorna.

O script contém duas funções que carregam e executam o modelo:

* `init()`: Normalmente, essa função carrega o modelo em um objeto global. Esta função é executada apenas uma vez, quando o contêiner Docker para o seu serviço web é iniciado.

* `run(input_data)`: Esta função usa o modelo para prever um valor baseado nos dados de entrada. As entradas e saídas da execução normalmente usam JSON para serialização e desserialização. Você também pode trabalhar com os dados binários brutos. Você pode transformar os dados antes de enviá-los ao modelo ou antes de retorná-los ao cliente.

#### <a name="load-model-files-in-your-entry-script"></a>Carregar arquivos de modelo em seu script de entrada

Existem duas maneiras de localizar modelos em seu script de entrada:
* `AZUREML_MODEL_DIR`: Uma variável de ambiente contendo o caminho para a localização do modelo.
* `Model.get_model_path`: Uma API que retorna o caminho para o arquivo do modelo usando o nome do modelo registrado.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR é uma variável de ambiente criada durante a implantação do serviço. Você pode usar esta variável de ambiente para encontrar a localização dos modelos implantados.

A tabela a seguir descreve o valor de AZUREML_MODEL_DIR dependendo do número de modelos implantados:

| Implantação | Valor variável do ambiente |
| ----- | ----- |
| Modelo único | O caminho para a pasta que contém o modelo. |
| Múltiplos modelos | O caminho para a pasta contendo todos os modelos. Os modelos estão localizados por`$MODEL_NAME/$VERSION`nome e versão nesta pasta ( ) |

Durante o registro e implantação do modelo, os modelos são colocados no caminho AZUREML_MODEL_DIR e seus nomes de arquivo originais são preservados.

Para obter o caminho para um arquivo de modelo em seu script de entrada, combine a variável de ambiente com o caminho do arquivo que você está procurando.

**Exemplo de modelo único**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Exemplo de modelo múltiplo**
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Quando você registra um modelo, você fornece um nome modelo que é usado para gerenciar o modelo no registro. Você usa esse nome com o método [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) para recuperar o caminho do arquivo modelo ou arquivos no sistema de arquivos local. Se você registrar uma pasta ou uma coleção de arquivos, esta API retorna o caminho do diretório que contém esses arquivos.

Quando você registra um modelo, você dá-lhe um nome. O nome corresponde ao local onde o modelo é colocado, localmente ou durante a implantação do serviço.

#### <a name="optional-define-model-web-service-schema"></a>(Opcional) Definir esquema de serviço web modelo

Para gerar automaticamente um esquema para o seu serviço web, forneça uma amostra da entrada e/ou saída no construtor para um dos objetos de tipo definidos. O tipo e a amostra são usados para criar automaticamente o esquema. O Azure Machine Learning cria então uma especificação [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) para o serviço web durante a implantação.

Esses tipos são suportados atualmente:

* `pandas`
* `numpy`
* `pyspark`
* Objeto Python padrão

Para usar a geração de esquemas, inclua o pacote de código `inference-schema` aberto em seu arquivo de dependências. Para obter mais informações [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)sobre este pacote, consulte . Defina os formatos de `input_sample` amostra `output_sample` de entrada e saída nas variáveis e que representam os formatos de solicitação e resposta para o serviço web. Use essas amostras nos decoradores de `run()` função de entrada e saída na função. O exemplo a seguir de scikit-learn usa a geração de esquemas.

##### <a name="example-entry-script"></a>Script de entrada por exemplo

O exemplo a seguir demonstra como aceitar e retornar dados JSON:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

O exemplo a seguir demonstra como `<key: value>` definir os dados de entrada como um dicionário usando um DataFrame. Este método é suportado para consumir o serviço web implantado do Power BI. ([Saiba mais sobre como consumir o serviço web do Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Para obter mais exemplos, consulte os seguintes scripts:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Binary Data](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. Defina a configuração da inferência
    
O exemplo a seguir demonstra carregar um ambiente do seu espaço de trabalho e, em seguida, usá-lo com a configuração de inferência:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Para obter mais informações sobre ambientes, consulte [Criar e gerenciar ambientes para treinamento e implantação.](how-to-use-environments.md)

Para obter mais informações sobre a configuração da inferência, consulte a documentação da classe [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)

Para obter informações sobre o uso de uma imagem Docker personalizada com uma configuração de inferência, consulte [Como implantar um modelo usando uma imagem Docker personalizada](how-to-deploy-custom-docker-image.md).

#### <a name="cli-example-of-inferenceconfig"></a>Exemplo de CLI de InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

O comando a seguir demonstra como implantar um modelo usando o CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Neste exemplo, a configuração especifica as seguintes configurações:

* Que o modelo requer Python.
* O [script de entrada](#script), que é usado para lidar com solicitações da Web enviadas ao serviço implantado.
* O arquivo Conda que descreve os pacotes Python necessários para inferência.

Para obter informações sobre o uso de uma imagem Docker personalizada com uma configuração de inferência, consulte [Como implantar um modelo usando uma imagem Docker personalizada](how-to-deploy-custom-docker-image.md).

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (Opcional) Perfile seu modelo para determinar a utilização de recursos

Depois de registrar seu modelo e preparar os outros componentes necessários para sua implantação, você pode determinar a CPU e a memória que o serviço implantado precisará. O perfil testa o serviço que executa seu modelo e retorna informações como o uso da CPU, o uso da memória e a latência de resposta. Ele também fornece uma recomendação para a CPU e memória com base no uso de recursos.

Para traçar o perfil do seu modelo, você precisará:
* Um modelo registrado.
* Uma configuração de inferência baseada no seu script de entrada e na definição do ambiente de inferência.
* Um conjunto de dados tabular de uma única coluna, onde cada linha contém uma seqüência representando dados de solicitação de amostra.

> [!IMPORTANT]
> Neste ponto, só suportamos o perfil de serviços que esperam que seus dados de solicitação sejam uma string, por exemplo: string serialized json, text, string serialized image, etc. O conteúdo de cada linha do conjunto de dados (string) será colocado no corpo da solicitação HTTP e enviado ao serviço encapsulando o modelo para pontuação.

Abaixo está um exemplo de como você pode construir um conjunto de dados de entrada para traçar o perfil de um serviço que espera que seus dados de solicitação de entrada contenham json serializado. Neste caso, criamos um conjunto de dados baseado em cem instâncias do mesmo conteúdo de dados de solicitação. Em cenários do mundo real, sugerimos que você use conjuntos de dados maiores contendo várias entradas, especialmente se o uso/comportamento do seu modelo de recurso é dependente de entrada.

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Depois de ter o conjunto de dados contendo dados de solicitação de amostra pronto, crie uma configuração de inferência. A configuração de inferência é baseada na score.py e na definição do ambiente. O exemplo a seguir demonstra como criar a configuração de inferência e executar o perfil:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

O comando a seguir demonstra como perfilar um modelo usando o CLI:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Para persistir as informações retornadas por criação de perfil, use tags ou propriedades para o modelo. O uso de tags ou propriedades armazena os dados com o modelo no registro do modelo. Os exemplos a seguir demonstram a `requestedCpu` adição de uma nova tag contendo e `requestedMemoryInGb` informações:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Implantar para o alvo

A implantação usa a configuração de configuração de inferência para implantar os modelos. O processo de implantação é semelhante, independentemente do alvo da computação. A implantação no AKS é ligeiramente diferente porque você deve fornecer uma referência ao cluster AKS.

### <a name="choose-a-compute-target"></a>Escolha um alvo de computação

Você pode usar os seguintes alvos de computação ou recursos de computação para hospedar sua implantação de serviço web:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>Defina sua configuração de implantação

Antes de implantar seu modelo, você deve definir a configuração de implantação. *A configuração de implantação é específica para o destino de computação que hospedará o serviço web.* Por exemplo, quando você implanta um modelo localmente, você deve especificar a porta onde o serviço aceita solicitações. A configuração de implantação não faz parte do seu script de entrada. É usado para definir as características do alvo de computação que hospedará o modelo e o script de entrada.

Você também pode precisar criar o recurso de computação, se, por exemplo, você ainda não tiver uma instância AkS (AKS) do Azure Kubernetes Service associada ao seu espaço de trabalho.

A tabela a seguir fornece um exemplo de criação de uma configuração de implantação para cada alvo de computação:

| Destino de computação | Exemplo de configuração de implantação |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Instâncias de Contêiner do Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Serviço de Kubernetes do Azure | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As classes para serviços locais, instâncias de contêiner Azure `azureml.core.webservice`e Web AKS podem ser importadas de :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>Garantir implantações com TLS

Para obter mais informações sobre como garantir uma implantação de serviço web, consulte [Ativar TLS e implantar](how-to-secure-web-service.md#enable).

### <a name="local-deployment"></a><a id="local"></a>Implantação local

Para implantar um modelo localmente, você precisa ter o Docker instalado em sua máquina local.

#### <a name="using-the-sdk"></a>Usar o SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações, consulte a documentação de [LocalWebservice,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py) [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Usando a CLI

Para implantar um modelo usando o CLI, use o seguinte comando. Substitua pelo `mymodel:1` nome e versão do modelo registrado:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Para obter mais informações, consulte a documentação [de implantação do modelo az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="understanding-service-state"></a>Compreensão do estado de serviço

Durante a implantação do modelo, você pode ver o estado de serviço mudar enquanto ele é totalmente implantado.

A tabela a seguir descreve os diferentes estados de serviço:

| Estado do Webservice | Descrição | Último estado?
| ----- | ----- | ----- |
| Transição | O serviço está em processo de implantação. | Não |
| Não Íntegro | O serviço foi implantado, mas atualmente é inalcançável.  | Não |
| Não agendamento | O serviço não pode ser implantado neste momento devido à falta de recursos. | Não |
| Falhou | O serviço não foi implantado devido a um erro ou falha. | Sim |
| Healthy | O serviço é saudável e o ponto final está disponível. | Sim |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Serviço web de instância de cálculo (dev/teste)

Consulte [Implantar um modelo para a instância de cálculo do Azure Machine Learning](how-to-deploy-local-container-notebook-vm.md).

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Instâncias do contêiner Azure (dev/teste)

Consulte [Implantar em instâncias de contêiner do Azure](how-to-deploy-azure-container-instance.md).

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Serviço Azure Kubernetes (dev/teste e produção)

Consulte [Implantar no Serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>Teste A/B (implantação controlada)
Consulte [A implantação controlada de modelos ML](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview) para obter mais informações.

## <a name="consume-web-services"></a>Consumir serviços Web

Cada serviço web implantado fornece um ponto final REST, para que você possa criar aplicativos clientes em qualquer linguagem de programação.
Se você habilitou a autenticação baseada em chaves para o seu serviço, você precisa fornecer uma chave de serviço como um token no cabeçalho de solicitação.
Se você habilitou a autenticação baseada em token satisfatodia para o seu serviço, você precisa fornecer um JSON Web Token (JWT) de aprendizado de máquina do Azure como um token portador no cabeçalho de solicitação. 

A principal diferença é que **as teclas são estáticas e podem ser regeneradas manualmente,** e **os tokens precisam ser atualizados após o vencimento**. O auth baseado em chaves é suportado para a instância de contêiner azure e o Azure Kubernetes Service implantou serviços web, e o auth baseado em **tokens só** está disponível para implantações do Azure Kubernetes Service. Consulte [a autenticação de como fazer](how-to-setup-authentication.md#web-service-authentication) para obter mais informações e amostras de código específicas.

> [!TIP]
> Você pode recuperar o documento JSON do esquema depois de implantar o serviço. Use a [propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) do serviço web `service.swagger_uri`implantado (por exemplo, ) para obter o URI para o arquivo Swagger do serviço web local.

### <a name="request-response-consumption"></a>Consumo de solicitação-resposta

Aqui está um exemplo de como invocar seu serviço em Python:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Para obter mais informações, consulte [Criar aplicativos clientes para consumir serviços web](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Esquema de serviço web (especificação OpenAPI)

Se você usou a geração automática de esquemas com a sua implantação, você pode obter o endereço da especificação OpenAPI para o serviço usando a [propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Por exemplo, `print(service.swagger_uri)`.) Use uma solicitação GET ou abra o URI em um navegador para recuperar a especificação.

O seguinte documento JSON é um exemplo de um esquema (especificação OpenAPI) gerado para uma implantação:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Para obter mais informações, consulte [a especificação OpenAPI](https://swagger.io/specification/).

Para obter um utilitário que possa criar bibliotecas de clientes a partir da especificação, consulte [swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Inferência em lote
Os alvos do Azure Machine Learning Compute são criados e gerenciados pelo Azure Machine Learning. Eles podem ser usados para previsão de lotes de dutos de machine learning do Azure.

Para obter um passo a passo da inferência em lote com o Azure Machine Learning Compute, consulte [Como executar previsões em lote](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Inferência de borda IoT
O suporte para implantação na borda está na pré-visualização. Para obter mais informações, consulte [Deploy Azure Machine Learning como um módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="update-web-services"></a><a id="update"></a>Atualizar serviços web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Implantar continuamente modelos

Você pode implantar continuamente modelos usando a extensão Machine Learning para [DevOps Azure](https://azure.microsoft.com/services/devops/). Você pode usar a extensão machine learning para DevOps do Azure para ativar um pipeline de implantação quando um novo modelo de aprendizado de máquina é registrado em um espaço de trabalho de Aprendizado de Máquina do Azure.

1. Inscreva-se [no Azure Pipelines,](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)que torna possível a integração e entrega contínua de seu aplicativo em qualquer plataforma ou nuvem. (Observe que o Azure Pipelines não é o mesmo que [os pipelines de Machine Learning](concept-ml-pipelines.md#compare).)

1. [Crie um projeto Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Instale a [extensão Machine Learning para azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Use conexões de serviço para configurar uma conexão principal de serviço ao seu espaço de trabalho azure Machine Learning para que você possa acessar seus artefatos. Vá para as configurações do projeto, selecione **conexões de serviço**e selecione **O Gerenciador de recursos do Azure**:

    [![Selecione o Gerenciador de Recursos do Azure](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Na lista **de nível de escopo,** selecione **AzureMLWorkspace**e digite o resto dos valores:

    ![Selecione AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Para implantar continuamente seu modelo de aprendizado de máquina usando o Azure Pipelines, sob pipelines, selecione **a versão**. Adicione um novo artefato e selecione o artefato **Modelo AzureML** e a conexão de serviço que você criou anteriormente. Selecione o modelo e a versão para ativar uma implantação:

    [![Selecione o modelo AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Habilite o gatilho do modelo no seu artefato modelo. Quando você liga o gatilho, sempre que a versão especificada (ou seja, a versão mais recente) desse modelo é registrada no seu espaço de trabalho, um pipeline de liberação do Azure DevOps é acionado.

    [![Habilite o gatilho do modelo](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Para obter mais projetos e exemplos de exemplo, consulte esses repositórios de amostras no GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Baixe um modelo
Se você quiser baixar seu modelo para usá-lo em seu próprio ambiente de execução, você pode fazê-lo com os seguintes comandos SDK / CLI:

Sdk:
```python
model_path = Model(ws,'mymodel').download()
```

CLI:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(Visualização) Implantação de modelo sem código

A implantação do modelo sem código está atualmente em visualização e suporta as seguintes estruturas de aprendizado de máquina:

### <a name="tensorflow-savedmodel-format"></a>Formato Tensorflow SavedModel
Os modelos tensorflow precisam ser registrados no **formato SavedModel** para funcionar com a implantação de modelos sem código.

Consulte [este link](https://www.tensorflow.org/guide/saved_model) para obter informações sobre como criar um SavedModel.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>Modelos ONNX

O registro e a implantação do modelo ONNX são suportados para qualquer gráfico de inferência ONNX. As etapas de pré-processo e pós-processo não são suportadas no momento.

Aqui está um exemplo de como registrar e implantar um modelo MNIST ONNX:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Se você estiver usando pytorch, [exportar modelos de PyTorch para ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) tem os detalhes sobre conversão e limitações. 

### <a name="scikit-learn-models"></a>Modelos do Scikit-learn

Nenhuma implantação de modelo de código é suportada para todos os tipos de modelos de scikit-learn incorporados.

Aqui está um exemplo de como registrar e implantar um modelo sklearn sem código extra:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

NOTA: Os modelos que suportam predict_proba usarão esse método por padrão. Para anular isso para usar prever você pode modificar o corpo POST como abaixo:
```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

NOTA: Essas dependências estão incluídas no recipiente de inferência de sklearn pré-construído:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Modelos de pacotes

Em alguns casos, você pode querer criar uma imagem Docker sem implantar o modelo (se, por exemplo, você planeja [implantar no Azure App Service](how-to-deploy-app-service.md)). Ou você pode querer baixar a imagem e executá-la em uma instalação local do Docker. Você pode até querer baixar os arquivos usados para construir a imagem, inspecioná-los, modificá-los e construir a imagem manualmente.

A embalagem do modelo permite que você faça essas coisas. Ele empacota todos os ativos necessários para hospedar um modelo como um serviço web e permite que você baixe uma imagem Docker totalmente construída ou os arquivos necessários para construir um. Existem duas maneiras de usar a embalagem do modelo:

**Baixe um modelo embalado:** Baixe uma imagem do Docker que contenha o modelo e outros arquivos necessários para hospedá-lo como um serviço web.

**Gerar um arquivo Docker:** Baixe o arquivo Docker, modelo, script de entrada e outros ativos necessários para construir uma imagem Docker. Em seguida, você pode inspecionar os arquivos ou fazer alterações antes de construir a imagem localmente.

Ambos os pacotes podem ser usados para obter uma imagem local do Docker.

> [!TIP]
> Criar um pacote é semelhante à implantação de um modelo. Você usa um modelo registrado e uma configuração de inferência.

> [!IMPORTANT]
> Para baixar uma imagem totalmente construída ou construir uma imagem localmente, você precisa ter [o Docker](https://www.docker.com) instalado em seu ambiente de desenvolvimento.

### <a name="download-a-packaged-model"></a>Baixe um modelo embalado

O exemplo a seguir constrói uma imagem, que está registrada no registro de contêiner do Azure para o seu espaço de trabalho:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Depois de criar um pacote, você pode usar `package.pull()` para puxar a imagem para o ambiente Docker local. A saída deste comando exibirá o nome da imagem. Por exemplo:  

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Depois de baixar o `docker images` modelo, use o comando para listar as imagens locais:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Para iniciar um contêiner local com base nesta imagem, use o seguinte comando para iniciar um contêiner nomeado a partir da shell ou da linha de comando. Substitua `<imageid>` o valor pelo ID `docker images` de imagem retornado pelo comando.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Este comando inicia a versão `myimage`mais recente da imagem nomeada . Ele mapeia a porta local 6789 para o porto no contêiner em que o serviço web está ouvindo (5001). Ele também atribui `mycontainer` o nome ao recipiente, o que torna o recipiente mais fácil de parar. Depois que o contêiner é iniciado, você pode enviar solicitações para `http://localhost:6789/score`.

### <a name="generate-a-dockerfile-and-dependencies"></a>Gerar um arquivo Docker e dependências

O exemplo a seguir mostra como baixar o Arquivo Docker, o modelo e outros ativos necessários para construir uma imagem localmente. O `generate_dockerfile=True` parâmetro indica que você quer os arquivos, não uma imagem totalmente construída.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Este código baixa os arquivos necessários `imagefiles` para construir a imagem para o diretório. O arquivo Docker incluído nos arquivos salvos faz referência a uma imagem base armazenada em um registro de contêiner do Azure. Quando você constrói a imagem na instalação local do Docker, você precisa usar o endereço, o nome do usuário e a senha para autenticar no registro. Use as seguintes etapas para construir a imagem usando uma instalação docker local:

1. A partir de uma sessão de shell ou linha de comando, use o seguinte comando para autenticar o Docker com o registro de contêiner Dozure. Substituir `<address>` `<username>`, `<password>` e com os `package.get_container_registry()`valores recuperados por .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Para construir a imagem, use o seguinte comando. Substitua pelo `<imagefiles>` caminho do `package.save()` diretório onde salvaram os arquivos.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Este comando define o `myimage`nome da imagem para .

Para verificar se a imagem `docker images` foi construída, use o comando. Você deve `myimage` ver a imagem na lista:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Para iniciar um novo contêiner com base nesta imagem, use o seguinte comando:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Este comando inicia a versão `myimage`mais recente da imagem nomeada . Ele mapeia a porta local 6789 para o porto no contêiner em que o serviço web está ouvindo (5001). Ele também atribui `mycontainer` o nome ao recipiente, o que torna o recipiente mais fácil de parar. Depois que o contêiner é iniciado, você pode enviar solicitações para `http://localhost:6789/score`.

### <a name="example-client-to-test-the-local-container"></a>Exemplo de cliente para testar o contêiner local

O código a seguir é um exemplo de um cliente Python que pode ser usado com o contêiner:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Por exemplo, clientes em outras linguagens de programação, consulte [Consumir modelos implantados como serviços web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Pare o recipiente Docker

Para parar o contêiner, use o seguinte comando de uma shell ou linha de comando diferente:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para excluir um serviço Web implantado, use `service.delete()`.
Para excluir um modelo registrado, use `model.delete()`.

Para obter mais informações, consulte a documentação [de WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Autor de script de entrada avançado

<a id="binary"></a>

### <a name="binary-data"></a>Dados binários

Se o seu modelo aceitar dados binários, `score.py` como uma imagem, você deve modificar o arquivo usado para sua implantação para aceitar solicitações HTTP brutas. Para aceitar dados brutos, use a `AMLRequest` classe `@rawhttp` no seu `run()` script de entrada e adicione o decorador à função.

Aqui está um exemplo `score.py` de um que aceita dados binários:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> A classe `AMLRequest` está no namespace `azureml.contrib`. As entidades neste namespace mudam com freqüência à medida que trabalhamos para melhorar o serviço. Qualquer coisa neste namespace deve ser considerada uma pré-visualização que não é totalmente suportada pela Microsoft.
>
> Se você precisar testar isso em seu ambiente de desenvolvimento local, você pode instalar os componentes usando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Compartilhamento de recursos de origem cruzada (CORS)

O compartilhamento de recursos de origem cruzada é uma maneira de permitir que recursos em uma página web sejam solicitados de outro domínio. O CORS funciona através de cabeçalhos HTTP enviados com a solicitação do cliente e devolvidos com a resposta do serviço. Para obter mais informações sobre cors e cabeçalhos válidos, consulte [compartilhamento de recursos de origem cruzada](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) na Wikipédia.

Para configurar sua implantação de modelo para `AMLResponse` suportar cors, use a classe em seu script de entrada. Esta classe permite definir os cabeçalhos no objeto de resposta.

O exemplo a `Access-Control-Allow-Origin` seguir define o cabeçalho para a resposta do script de entrada:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> A classe `AMLResponse` está no namespace `azureml.contrib`. As entidades neste namespace mudam com freqüência à medida que trabalhamos para melhorar o serviço. Qualquer coisa neste namespace deve ser considerada uma pré-visualização que não é totalmente suportada pela Microsoft.
>
> Se você precisar testar isso em seu ambiente de desenvolvimento local, você pode instalar os componentes usando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> O Azure Machine Learning irá encaminhar apenas solicitações DE POST e GET para os contêineres que executam o serviço de pontuação. Isso pode causar erros devido aos navegadores que usam solicitações OPTIONS para solicitações cors pré-vôo.
> 

## <a name="next-steps"></a>Próximas etapas

* [Como implantar um modelo usando uma imagem Docker personalizada](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Use o TLS para garantir um serviço web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um modelo de Azure Machine Learning implantado como um serviço web](how-to-consume-web-service.md)
* [Monitore seus modelos de Aprendizado de Máquina do Azure com insights de aplicativos](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [Crie alertas de eventos e gatilhos para implantações de modelos](how-to-use-event-grid.md)

