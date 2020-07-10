---
title: Como e onde implantar modelos
titleSuffix: Azure Machine Learning
description: Saiba como e onde implantar seus modelos de Azure Machine Learning, incluindo instâncias de contêiner do Azure, serviço kubernetes do Azure, Azure IoT Edge e matrizes de portão programáveis por campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.custom: seoapril2019, tracking-python
ms.openlocfilehash: 57e1ecb080d816898b862951846b15a4b5709e38
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146558"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Implantar modelos com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como implantar o modelo de aprendizado de máquina como um serviço Web na nuvem do Azure ou para Azure IoT Edge dispositivos.

O fluxo de trabalho é semelhante [, independentemente de onde você implanta](#target) seu modelo:

1. Registre o modelo.
1. Prepare-se para implantar. (Especifique os ativos, o uso e o destino de computação.)
1. Implante o modelo no destino de computação.
1. Teste o modelo implantado, também chamado de serviço Web.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, consulte [gerenciar, implantar e monitorar modelos com Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).

- Um modelo. Se você não tiver um modelo treinado, poderá usar os arquivos de modelo e de dependência fornecidos neste [tutorial](https://aka.ms/azml-deploy-cloud).

- A [extensão CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a [extensão Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="connect-to-your-workspace"></a>Conectar-se ao workspace

O código a seguir mostra como se conectar a um espaço de trabalho Azure Machine Learning usando informações armazenadas em cache para o ambiente de desenvolvimento local:

+ **Usar o SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Para obter mais informações sobre como usar o SDK para se conectar a um espaço de trabalho, consulte a documentação [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

+ **Usando a CLI**

   Ao usar a CLI, use o `-w` `--workspace-name` parâmetro ou para especificar o espaço de trabalho para o comando.

+ **Usar o Visual Studio Code**

   Ao usar Visual Studio Code, você seleciona o espaço de trabalho usando uma interface gráfica. Para obter mais informações, consulte [implantar e gerenciar modelos](how-to-manage-resources-vscode.md#endpoints) na documentação de extensão de Visual Studio Code.

## <a name="register-your-model"></a><a id="registermodel"></a>Registrar seu modelo

Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo no espaço de trabalho. Depois de registrar os arquivos, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que você registrou.

> [!TIP]
> Ao registrar um modelo, você fornece o caminho de um local de nuvem (de uma execução de treinamento) ou um diretório local. Esse caminho é apenas para localizar os arquivos para upload como parte do processo de registro. Ele não precisa corresponder ao caminho usado no script de entrada. Para obter mais informações, consulte [Localizar arquivos de modelo em seu script de entrada](#load-model-files-in-your-entry-script).

Os modelos de aprendizado de máquina são registrados em seu espaço de trabalho do Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou de outro lugar. Ao registrar um modelo, você pode, opcionalmente, fornecer metadados sobre o modelo. Os `tags` `properties` dicionários e que você aplica a um registro de modelo podem ser usados para filtrar modelos.

Os exemplos a seguir demonstram como registrar um modelo.

### <a name="register-a-model-from-an-experiment-run"></a>Registrar um modelo de uma execução de experimento

Os trechos de código nesta seção demonstram como registrar um modelo de uma execução de treinamento:

> [!IMPORTANT]
> Para usar esses trechos de código, você precisa ter executado anteriormente uma execução de treinamento e precisa ter acesso ao `Run` objeto (exemplo de SDK) ou ao valor da ID de execução (exemplo de CLI). Para obter mais informações sobre modelos de treinamento, consulte [Configurar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md).

+ **Usar o SDK**

  Ao usar o SDK para treinar um modelo, você pode receber um objeto de [execução](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) ou um objeto [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) , dependendo de como você treinou o modelo. Cada objeto pode ser usado para registrar um modelo criado por uma execução de experimento.

  + Registrar um modelo de um `azureml.core.Run` objeto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    O `model_path` parâmetro refere-se ao local da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários arquivos no registro do modelo, defina `model_path` para o caminho de uma pasta que contém os arquivos. Para obter mais informações, consulte a documentação [executar. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Registrar um modelo de um `azureml.train.automl.run.AutoMLRun` objeto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    Neste exemplo, os `metric` parâmetros e `iteration` não são especificados, portanto, a iteração com a melhor métrica primária será registrada. O `model_id` valor retornado da execução é usado em vez de um nome de modelo.

    Para obter mais informações, consulte a documentação do [AutoMLRun. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .

+ **Usando a CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  O `--asset-path` parâmetro refere-se ao local da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários arquivos no registro do modelo, defina `--asset-path` para o caminho de uma pasta que contém os arquivos.

+ **Usar o Visual Studio Code**

  Registre modelos usando qualquer arquivo ou pasta de modelo usando a extensão [Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) .

### <a name="register-a-model-from-a-local-file"></a>Registrar um modelo de um arquivo local

Você pode registrar um modelo fornecendo o caminho local do modelo. Você pode fornecer o caminho de uma pasta ou de um único arquivo. Você pode usar esse método para registrar modelos treinados com Azure Machine Learning e, em seguida, baixados. Você também pode usar esse método para registrar modelos treinados fora do Azure Machine Learning.

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

  Para incluir vários arquivos no registro do modelo, defina `model_path` para o caminho de uma pasta que contém os arquivos.

+ **Usando a CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Para incluir vários arquivos no registro do modelo, defina `-p` para o caminho de uma pasta que contém os arquivos.

**Tempo estimado**: aproximadamente 10 segundos.

Para obter mais informações, consulte a documentação da [classe Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Para obter mais informações sobre como trabalhar com modelos treinados fora do Azure Machine Learning, consulte [como implantar um modelo existente](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Pontos de extremidade únicos versus vários modelos
O Azure ML dá suporte à implantação de modelos únicos ou múltiplos por trás de um único ponto de extremidade.

Os pontos de extremidade de vários modelos usam um contêiner compartilhado para hospedar vários modelos. Isso ajuda a reduzir os custos de sobrecarga, melhora a utilização e permite encadear os módulos em conjuntos. Os modelos que você especificar em seu script de implantação são montados e disponibilizados no disco do contêiner de serviço – você pode carregá-los na memória sob demanda e pontuar com base no modelo específico que está sendo solicitado no momento da pontuação.

Para um exemplo de E2E, que mostra como usar vários modelos atrás de um único ponto de extremidade em contêiner, consulte [Este exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model)

## <a name="prepare-to-deploy"></a>Preparar-se para implantar

Para implantar o modelo como um serviço, você precisa dos seguintes componentes:

* **Defina o ambiente de inferência**. Esse ambiente encapsula as dependências necessárias para executar seu modelo para a inferência.
* **Defina o código de Pontuação**. Esse script aceita solicitações, pontua as solicitações usando o modelo e retorna os resultados.
* **Defina a configuração de inferência**. A configuração de inferência especifica a configuração do ambiente, o script de entrada e outros componentes necessários para executar o modelo como um serviço.

Depois de ter os componentes necessários, você pode criar o perfil do serviço que será criado como resultado da implantação do modelo para entender seus requisitos de CPU e memória.

### <a name="1-define-inference-environment"></a>1. definir o ambiente de inferência

Uma configuração de inferência descreve como configurar o serviço Web que contém seu modelo. Ele é usado posteriormente, quando você implanta o modelo.

A configuração de inferência usa ambientes Azure Machine Learning para definir as dependências de software necessárias para sua implantação. Os ambientes permitem que você crie, gerencie e reutilize as dependências de software necessárias para treinamento e implantação. Você pode criar um ambiente a partir de arquivos de dependência personalizados ou usar um dos ambientes de Azure Machine Learning organizadas. O YAML a seguir é um exemplo de um arquivo de dependências Conda para inferência. Observe que você deve indicar os padrões do azureml com a versão >= 1.0.45 como uma dependência Pip, pois ele contém a funcionalidade necessária para hospedar o modelo como um serviço Web. Se você quiser usar a geração de esquema automática, o script de entrada também deverá importar os `inference-schema` pacotes.

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
> Se sua dependência estiver disponível por meio de Conda e PIP (de PyPi), a Microsoft recomenda usar a versão Conda, já que os pacotes Conda normalmente são fornecidos com binários pré-criados que tornam a instalação mais confiável.
>
> Para obter mais informações, consulte [Understanding Conda and Pip](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Para verificar se sua dependência está disponível por meio do Conda, use o `conda search <package-name>` comando ou use os índices de pacote em [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) e [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

Você pode usar o arquivo de dependências para criar um objeto de ambiente e salvá-lo em seu espaço de trabalho para uso futuro:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. definir código de Pontuação

O script de entrada recebe os dados enviados para um serviço Web implantado e os transmite ao modelo. Em seguida, ele envia de volta ao cliente a resposta retornada pelo modelo. *O script é específico para seu modelo*. Ele deve entender os dados esperados e retornados pelo modelo.

O script contém duas funções que carregam e executam o modelo:

* `init()`: Normalmente, essa função carrega o modelo em um objeto global. Essa função é executada apenas uma vez, quando o contêiner do Docker para o serviço Web é iniciado.

* `run(input_data)`: Essa função usa o modelo para prever um valor com base nos dados de entrada. As entradas e saídas da execução normalmente usam JSON para serialização e desserialização. Você também pode trabalhar com os dados binários brutos. Você pode transformar os dados antes de enviá-los ao modelo ou antes de retorná-los ao cliente.

#### <a name="load-model-files-in-your-entry-script"></a>Carregar arquivos de modelo em seu script de entrada

Há duas maneiras de localizar modelos em seu script de entrada:
* `AZUREML_MODEL_DIR`: Uma variável de ambiente que contém o caminho para o local do modelo.
* `Model.get_model_path`: Uma API que retorna o caminho para o arquivo de modelo usando o nome do modelo registrado.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR é uma variável de ambiente criada durante a implantação do serviço. Você pode usar essa variável de ambiente para localizar o local dos modelos implantados.

A tabela a seguir descreve o valor de AZUREML_MODEL_DIR dependendo do número de modelos implantados:

| Implantação | Valor da variável de ambiente |
| ----- | ----- |
| Modelo único | O caminho para a pasta que contém o modelo. |
| Vários modelos | O caminho para a pasta que contém todos os modelos. Os modelos estão localizados por nome e versão nesta pasta ( `$MODEL_NAME/$VERSION` ) |

Durante o registro e a implantação do modelo, os modelos são colocados no caminho de AZUREML_MODEL_DIR e seus nomes de filehorários originais são preservados.

Para obter o caminho para um arquivo de modelo em seu script de entrada, combine a variável de ambiente com o caminho de arquivo que você está procurando.

**Exemplo de modelo único**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Exemplo de vários modelos**

Nesse cenário, dois modelos são registrados com o espaço de trabalho:

* `my_first_model`: Contém um arquivo ( `my_first_model.pkl` ) e há apenas uma versão ( `1` ).
* `my_second_model`: Contém um arquivo ( `my_second_model.pkl` ) e há duas versões; `1` e `2` .

Quando o serviço foi implantado, ambos os modelos são fornecidos na operação de implantação:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

Na imagem do Docker que hospeda o serviço, a `AZUREML_MODEL_DIR` variável de ambiente contém o diretório em que os modelos estão localizados.
Nesse diretório, cada um dos modelos está localizado em um caminho de diretório de `MODEL_NAME/VERSION` . Em que `MODEL_NAME` é o nome do modelo registrado e `VERSION` é a versão do modelo. Os arquivos que compõem o modelo registrado são armazenados nesses diretórios.

Neste exemplo, os caminhos seriam `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` e `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Ao registrar um modelo, você fornece um nome de modelo que é usado para gerenciar o modelo no registro. Use esse nome com o método [Model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) para recuperar o caminho do arquivo de modelo ou arquivos no sistema de arquivos local. Se você registrar uma pasta ou uma coleção de arquivos, essa API retornará o caminho do diretório que contém esses arquivos.

Ao registrar um modelo, você lhe dá um nome. O nome corresponde ao local em que o modelo é colocado, seja localmente ou durante a implantação do serviço.

#### <a name="optional-define-model-web-service-schema"></a>Adicional Definir esquema de serviço Web de modelo

Para gerar automaticamente um esquema para o serviço Web, forneça um exemplo de entrada e/ou saída no construtor para um dos objetos de tipo definidos. O tipo e o exemplo são usados para criar automaticamente o esquema. Azure Machine Learning, em seguida, cria uma especificação de [openapi](https://swagger.io/docs/specification/about/) (Swagger) para o serviço Web durante a implantação.

Atualmente, esses tipos têm suporte:

* `pandas`
* `numpy`
* `pyspark`
* Objeto Python padrão

Para usar a geração de esquema, inclua o pacote de código-fonte aberto `inference-schema` no arquivo de dependências. Para obter mais informações sobre este pacote, consulte [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . Defina os formatos de exemplo de entrada e saída `input_sample` nas `output_sample` variáveis e, que representam os formatos de solicitação e resposta para o serviço Web. Use esses exemplos nos decoradores da função de entrada e saída na `run()` função. O exemplo a seguir scikit-Learn usa a geração de esquema.

##### <a name="example-entry-script"></a>Exemplo de script de entrada

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

##### <a name="power-bi-compatible-endpoint"></a>Power BI ponto de extremidade compatível 

O exemplo a seguir demonstra como definir os dados de entrada como um `<key: value>` dicionário usando um dataframe. Esse método tem suporte para o consumo do serviço Web implantado do Power BI. ([Saiba mais sobre como consumir o serviço Web do Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

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

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample, enforce_shape=False))
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
* [Dados binários](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. definir configuração de inferência
    
O exemplo a seguir demonstra como carregar um ambiente do seu espaço de trabalho e usá-lo com a configuração de inferência:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Para obter mais informações sobre ambientes, consulte [criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md).

Para obter mais informações sobre a configuração de inferência, consulte a documentação da classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Para obter informações sobre como usar uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

#### <a name="cli-example-of-inferenceconfig"></a>Exemplo de CLI de InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

O comando a seguir demonstra como implantar um modelo usando a CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Neste exemplo, a configuração especifica as seguintes configurações:

* Se o modelo requer Python.
* O [script de entrada](#script), que é usado para manipular solicitações da Web enviadas ao serviço implantado.
* O arquivo Conda que descreve os pacotes do python necessários para a inferência.

Para obter informações sobre como usar uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (opcional) criar o perfil de seu modelo para determinar a utilização de recursos

Depois de registrar seu modelo e preparar os outros componentes necessários para sua implantação, você pode determinar a CPU e a memória que o serviço implantado precisará. A criação de perfil testa o serviço que executa o modelo e retorna informações como uso da CPU, uso de memória e latência de resposta. Ele também fornece uma recomendação para a CPU e a memória com base no uso de recursos.

Para criar o perfil de seu modelo, será necessário:
* Um modelo registrado.
* Uma configuração de inferência com base em sua definição de ambiente de inferência e script de entrada.
* Um conjunto de dados de tabela de coluna única, em que cada linha contém uma cadeia de caracteres que representa um exemplo de dado de solicitação

> [!IMPORTANT]
> Neste ponto, damos suporte apenas à criação de perfil de serviços que esperam que seus dados de solicitação sejam uma cadeia de caracteres, por exemplo: JSON serializado, texto, imagem serializada de cadeia de caracteres, etc. O conteúdo de cada linha do conjunto de linhas (cadeia de caracteres) será colocado no corpo da solicitação HTTP e enviado ao serviço que encapsula o modelo para pontuação.

Abaixo está um exemplo de como você pode construir um conjunto de dados de entrada para criar o perfil de um serviço que espera que os seus serviços de solicitação de entrada contenham o JSON serializado. Nesse caso, criamos um conjunto de dados com base em instâncias 100 do mesmo conteúdo de dado de solicitação. Em cenários do mundo real, sugerimos que você use conjuntos de dados maiores contendo várias entradas, especialmente se o uso/comportamento do recurso de modelo for dependente de entrada.

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

Depois que você tiver o conjunto de dados que contém o exemplo de data de solicitação pronto, crie uma configuração de inferência. A configuração de inferência é baseada no score.py e na definição de ambiente. O exemplo a seguir demonstra como criar a configuração de inferência e executar a criação de perfil:

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

O comando a seguir demonstra como criar um perfil de um modelo usando a CLI:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Para manter as informações retornadas pela criação de perfil, use marcas ou propriedades para o modelo. O uso de marcas ou Propriedades armazena os dados com o modelo no registro de modelo. Os exemplos a seguir demonstram como adicionar uma nova marca contendo as `requestedCpu` `requestedMemoryInGb` informações e:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Implantar no destino

A implantação usa a configuração de implantação de configuração de inferência para implantar os modelos. O processo de implantação é semelhante, independentemente do destino de computação. A implantação em AKS é um pouco diferente, pois você deve fornecer uma referência ao cluster AKS.

### <a name="choose-a-compute-target"></a>Escolher um destino de computação

Você pode usar os seguintes destinos de computação ou recursos de computação para hospedar a implantação do serviço Web:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

> [!NOTE]
> * ACI é adequado apenas para modelos pequenos <1GB de tamanho. 
> * É recomendável usar AKS de nó único para desenvolvimento/teste de modelos maiores.

### <a name="define-your-deployment-configuration"></a>Definir sua configuração de implantação

Antes de implantar seu modelo, você deve definir a configuração de implantação. *A configuração de implantação é específica para o destino de computação que hospedará o serviço Web.* Por exemplo, ao implantar um modelo localmente, você deve especificar a porta onde o serviço aceita solicitações. A configuração de implantação não faz parte do script de entrada. Ele é usado para definir as características do destino de computação que hospedará o modelo e o script de entrada.

Talvez você também precise criar o recurso de computação, se, por exemplo, você ainda não tiver uma instância do AKS (serviço kubernetes do Azure) associada ao seu espaço de trabalho.

A tabela a seguir fornece um exemplo de criação de uma configuração de implantação para cada destino de computação:

| Destino de computação | Exemplo de configuração de implantação |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Instâncias de Contêiner do Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Serviço de Kubernetes do Azure | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As classes para local, instâncias de contêiner do Azure e serviços Web AKS podem ser importadas de `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>Protegendo implantações com TLS

Para obter mais informações sobre como proteger uma implantação de serviço Web, consulte [habilitar TLS e implantar](how-to-secure-web-service.md#enable).

### <a name="local-deployment"></a><a id="local"></a>Implantação local

Para implantar um modelo localmente, você precisa ter o Docker instalado no computador local.

#### <a name="using-the-sdk"></a>Usar o SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações, consulte a documentação de [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. Deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Usando a CLI

Para implantar um modelo usando a CLI, use o comando a seguir. Substituir `mymodel:1` pelo nome e versão do modelo registrado:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Para obter mais informações, consulte a documentação de [implantação do modelo AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a name="understanding-service-state"></a>Compreendendo o estado do serviço

Durante a implantação do modelo, você pode ver a alteração do estado do serviço durante a implantação completa.

A tabela a seguir descreve os diferentes Estados de serviço:

| Estado WebService | Descrição | Estado final?
| ----- | ----- | ----- |
| Transição | O serviço está em processo de implantação. | Não |
| Não Íntegro | O serviço foi implantado, mas está inacessível no momento.  | Não |
| Não agendável | O serviço não pode ser implantado no momento devido à falta de recursos. | Não |
| Com falha | O serviço falhou ao ser implantado devido a um erro ou falha. | Sim |
| Healthy | O serviço está íntegro e o ponto de extremidade está disponível. | Sim |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Serviço Web de instância de computação (desenvolvimento/teste)

Consulte [implantar um modelo para Azure Machine Learning instância de computação](how-to-deploy-local-container-notebook-vm.md).

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Instâncias de contêiner do Azure (desenvolvimento/teste)

Consulte [implantar em instâncias de contêiner do Azure](how-to-deploy-azure-container-instance.md).

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Serviço kubernetes do Azure (desenvolvimento/teste e produção)

Consulte [implantar no serviço kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>Teste A/B (distribuição controlada)
Consulte [distribuição controlada de modelos de ml](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview) para obter mais informações.

## <a name="consume-web-services"></a>Consumir serviços Web

Cada serviço Web implantado fornece um ponto de extremidade REST, para que você possa criar aplicativos cliente em qualquer linguagem de programação.
Se você habilitou a autenticação baseada em chave para seu serviço, precisará fornecer uma chave de serviço como um token no cabeçalho da solicitação.
Se você habilitou a autenticação baseada em token para seu serviço, será necessário fornecer um Azure Machine Learning JWT (token Web JSON) como um token de portador no cabeçalho da solicitação. 

A principal diferença é que **as chaves são estáticas e podem ser regeneradas manualmente**, e os **tokens precisam ser atualizados após a expiração**. Há suporte para a autenticação baseada em chave para a instância de contêiner do Azure e os serviços Web implantados pelo serviço kubernetes do Azure, e a autenticação baseada em token **só** está disponível para implantações do serviço kubernetes do Azure. Consulte a autenticação de [instruções](how-to-setup-authentication.md#web-service-authentication) para obter mais informações e exemplos de código específicos.

> [!TIP]
> Você pode recuperar o documento JSON do esquema depois de implantar o serviço. Use a [propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) do serviço Web implantado (por exemplo, `service.swagger_uri` ) para obter o URI para o arquivo Swagger do serviço Web local.

### <a name="request-response-consumption"></a>Consumo de solicitação-resposta

Aqui está um exemplo de como invocar seu serviço no Python:
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

Para obter mais informações, consulte [criar aplicativos cliente para consumir serviços Web](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Esquema de serviço Web (especificação OpenAPI)

Se você usou a geração de esquema automática com sua implantação, você pode obter o endereço da especificação OpenAPI para o serviço usando a [propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Por exemplo, `print(service.swagger_uri)` .) Use uma solicitação GET ou abra o URI em um navegador para recuperar a especificação.

O documento JSON a seguir é um exemplo de um esquema (especificação de OpenAPI) gerado para uma implantação:

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

Para obter mais informações, consulte [especificação de openapi](https://swagger.io/specification/).

Para um utilitário que pode criar bibliotecas de cliente a partir da especificação, consulte [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen).

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Inferência de lote
Azure Machine Learning destinos de computação são criados e gerenciados pelo Azure Machine Learning. Eles podem ser usados para previsão de lote de pipelines de Azure Machine Learning.

Para obter uma explicação sobre a inferência de lote com Azure Machine Learning computação, consulte [como executar previsões de lote](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Inferência de IoT Edge
O suporte para a implantação no Edge está em versão prévia. Para obter mais informações, consulte [implantar Azure Machine Learning como um módulo IOT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="update-web-services"></a><a id="update"></a>Atualizar serviços Web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Implantar modelos continuamente

Você pode implantar modelos continuamente usando a extensão Machine Learning para o [Azure DevOps](https://azure.microsoft.com/services/devops/). Você pode usar a extensão Machine Learning do Azure DevOps para disparar um pipeline de implantação quando um novo modelo de aprendizado de máquina for registrado em um espaço de trabalho do Azure Machine Learning.

1. Inscreva-se para [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), que torna a integração contínua e a entrega de seu aplicativo a qualquer plataforma ou nuvem possível. (Observe que Azure Pipelines não é o mesmo que [Machine Learning pipelines](concept-ml-pipelines.md#compare).)

1. [Crie um projeto DevOps do Azure.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Instale a [extensão de Machine Learning para Azure pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Use conexões de serviço para configurar uma conexão de entidade de serviço com seu espaço de trabalho Azure Machine Learning para que você possa acessar seus artefatos. Vá para configurações do projeto, selecione **conexões de serviço**e, em seguida, selecione **Azure Resource Manager**:

    [![Selecionar Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Na lista **nível de escopo** , selecione **AzureMLWorkspace**e, em seguida, insira o restante dos valores:

    ![Selecionar AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Para implantar continuamente o modelo de aprendizado de máquina usando Azure Pipelines, em pipelines, selecione **liberar**. Adicione um novo artefato e, em seguida, selecione o artefato do **modelo do AzureML** e a conexão de serviço que você criou anteriormente. Selecione o modelo e a versão para disparar uma implantação:

    [![Selecionar modelo do AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Habilite o gatilho de modelo em seu artefato de modelo. Quando você ativa o gatilho, toda vez que a versão especificada (ou seja, a versão mais recente) desse modelo é registrada em seu espaço de trabalho, um pipeline de versão do Azure DevOps é disparado.

    [![Habilitar o gatilho de modelo](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Para obter mais exemplos e projetos de exemplo, consulte estes repositórios de exemplo no GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Baixar um modelo
Se você quiser baixar seu modelo para usá-lo em seu próprio ambiente de execução, poderá fazer isso com os seguintes comandos SDK/CLI:

SDK
```python
model_path = Model(ws,'mymodel').download()
```

CLI:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>Apresentação Implantação de modelo sem código

No momento, a implantação de modelo sem código está em versão prévia e dá suporte às seguintes estruturas de Machine Learning:

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel Format
Os modelos Tensorflow precisam ser registrados no **formato SavedModel** para trabalhar com a implantação de modelo sem código.

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

O registro e a implantação do modelo de ONNX têm suporte para qualquer grafo de inferência ONNX. Atualmente, não há suporte para as etapas de pré-processamento e postprocess.

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

Se você estiver usando o Pytorch, a [exportação de modelos de Pytorch para ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) terá os detalhes de conversão e limitações. 

### <a name="scikit-learn-models"></a>Modelos do Scikit-learn

Não há suporte para implantação de modelo de código para todos os tipos de modelo scikit-Learn internos.

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

Observação: os modelos que dão suporte a predict_proba usarão esse método por padrão. Para substituir isso para usar Predict, você pode modificar o corpo da POSTAgem como abaixo:
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

Observação: essas dependências estão incluídas no contêiner de inferência scikit-Learn pré-criado:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```

## <a name="package-models"></a>Modelos de pacote

Em alguns casos, talvez você queira criar uma imagem do Docker sem implantar o modelo (se, por exemplo, você planeja [implantar no serviço Azure app](how-to-deploy-app-service.md)). Ou talvez você queira baixar a imagem e executá-la em uma instalação local do Docker. Talvez você queira até mesmo baixar os arquivos usados para criar a imagem, inspecioná-los, modificá-los e criar a imagem manualmente.

O empacotamento de modelo permite que você faça essas coisas. Ele empacota todos os ativos necessários para hospedar um modelo como um serviço Web e permite que você baixe uma imagem do Docker totalmente criada ou os arquivos necessários para criar um. Há duas maneiras de usar o empacotamento de modelo:

**Baixar um modelo empacotado:** Baixe uma imagem do Docker que contém o modelo e outros arquivos necessários para hospedá-lo como um serviço Web.

**Gerar um Dockerfile:** Baixe o Dockerfile, o modelo, o script de entrada e outros ativos necessários para criar uma imagem do Docker. Você pode inspecionar os arquivos ou fazer alterações antes de criar a imagem localmente.

Ambos os pacotes podem ser usados para obter uma imagem local do Docker.

> [!TIP]
> A criação de um pacote é semelhante à implantação de um modelo. Você usa um modelo registrado e uma configuração de inferência.

> [!IMPORTANT]
> Para baixar uma imagem totalmente criada ou criar uma imagem localmente, você precisa ter o [Docker](https://www.docker.com) instalado em seu ambiente de desenvolvimento.

### <a name="download-a-packaged-model"></a>Baixar um modelo empacotado

O exemplo a seguir cria uma imagem, que é registrada no registro de contêiner do Azure para seu espaço de trabalho:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Depois de criar um pacote, você pode usar `package.pull()` o para efetuar pull da imagem para o ambiente do Docker local. A saída desse comando exibirá o nome da imagem. Por exemplo: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Depois de baixar o modelo, use o `docker images` comando para listar as imagens locais:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Para iniciar um contêiner local com base nessa imagem, use o comando a seguir para iniciar um contêiner nomeado do Shell ou da linha de comando. Substitua o `<imageid>` valor pela ID da imagem retornada pelo `docker images` comando.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Esse comando inicia a versão mais recente da imagem chamada `myimage` . Ele mapeia a porta local 6789 para a porta no contêiner em que o serviço Web está escutando (5001). Ele também atribui o nome `mycontainer` ao contêiner, o que torna o contêiner mais fácil de parar. Depois que o contêiner é iniciado, você pode enviar solicitações para `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Gerar um Dockerfile e dependências

O exemplo a seguir mostra como baixar o Dockerfile, o modelo e outros ativos necessários para criar uma imagem localmente. O `generate_dockerfile=True` parâmetro indica que você deseja os arquivos, não uma imagem totalmente compilada.

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

Esse código baixa os arquivos necessários para criar a imagem no `imagefiles` diretório. O Dockerfile incluído nos arquivos salvos faz referência a uma imagem base armazenada em um registro de contêiner do Azure. Ao criar a imagem na instalação local do Docker, você precisa usar o endereço, o nome de usuário e a senha para se autenticar no registro. Use as etapas a seguir para criar a imagem usando uma instalação local do Docker:

1. Em uma sessão de linha de comando ou Shell, use o comando a seguir para autenticar o Docker com o registro de contêiner do Azure. Substitua `<address>` , `<username>` e `<password>` pelos valores recuperados por `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Para criar a imagem, use o comando a seguir. Substitua `<imagefiles>` pelo caminho do diretório em que `package.save()` os arquivos foram salvos.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Este comando define o nome da imagem como `myimage` .

Para verificar se a imagem foi criada, use o `docker images` comando. Você deve ver a `myimage` imagem na lista:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Para iniciar um novo contêiner com base nessa imagem, use o seguinte comando:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Esse comando inicia a versão mais recente da imagem chamada `myimage` . Ele mapeia a porta local 6789 para a porta no contêiner em que o serviço Web está escutando (5001). Ele também atribui o nome `mycontainer` ao contêiner, o que torna o contêiner mais fácil de parar. Depois que o contêiner é iniciado, você pode enviar solicitações para `http://localhost:6789/score` .

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

Por exemplo, clientes em outras linguagens de programação, consulte [consumir modelos implantados como serviços Web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Parar o contêiner do Docker

Para parar o contêiner, use o seguinte comando de um shell ou linha de comando diferente:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Limpar recursos

Para excluir um serviço Web implantado, use `service.delete()`.
Para excluir um modelo registrado, use `model.delete()`.

Para obter mais informações, consulte a documentação de [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) e [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Criação de script de entrada avançada

<a id="binary"></a>

### <a name="binary-data"></a>Dados binários

Se o modelo aceitar dados binários, como uma imagem, você deverá modificar o `score.py` arquivo usado para sua implantação para aceitar solicitações HTTP brutas. Para aceitar dados brutos, use a `AMLRequest` classe em seu script de entrada e adicione o `@rawhttp` decorador à `run()` função.

Aqui está um exemplo de um `score.py` que aceita dados binários:

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
> A classe `AMLRequest` está no namespace `azureml.contrib`. As entidades neste namespace são alteradas com frequência enquanto trabalhamos para melhorar o serviço. Qualquer coisa neste namespace deve ser considerada uma versão prévia que não tem suporte total da Microsoft.
>
> Se precisar testar isso em seu ambiente de desenvolvimento local, você poderá instalar os componentes usando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```

A `AMLRequest` classe só permite que você acesse os dados brutos postados no score.py, não há nenhum componente do lado do cliente. Em um cliente, você posta dados normalmente. Por exemplo, o código Python a seguir lê um arquivo de imagem e posta os dados:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>CORS (compartilhamento de recursos entre origens)

O compartilhamento de recursos entre origens é uma maneira de permitir que recursos em uma página da Web sejam solicitados de outro domínio. O CORS funciona por meio de cabeçalhos HTTP enviados com a solicitação do cliente e retornado com a resposta do serviço. Para obter mais informações sobre CORS e cabeçalhos válidos, consulte [compartilhamento de recursos entre origens](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) na Wikipédia.

Para configurar sua implantação de modelo para dar suporte a CORS, use a `AMLResponse` classe em seu script de entrada. Essa classe permite que você defina os cabeçalhos no objeto de resposta.

O exemplo a seguir define o `Access-Control-Allow-Origin` cabeçalho para a resposta do script de entrada:

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

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> A classe `AMLResponse` está no namespace `azureml.contrib`. As entidades neste namespace são alteradas com frequência enquanto trabalhamos para melhorar o serviço. Qualquer coisa neste namespace deve ser considerada uma versão prévia que não tem suporte total da Microsoft.
>
> Se precisar testar isso em seu ambiente de desenvolvimento local, você poderá instalar os componentes usando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning roteará apenas as solicitações POST e GET para os contêineres que executam o serviço de pontuação. Isso pode causar erros devido a navegadores que usam solicitações de opções para solicitações CORS de simulação.
> 

## <a name="next-steps"></a>Próximas etapas

* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um modelo de Azure Machine Learning implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas de eventos e gatilhos para implantações de modelo](how-to-use-event-grid.md)

