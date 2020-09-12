---
title: Script de entrada de autor para cenários avançados
titleSuffix: Azure Machine Learning entry script authoring
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95d3570d93aa4966fcf6864838ec01735b8662db
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650289"
---
# <a name="advanced-entry-script-authoring"></a>Criação de scripts de entrada avançados

Este artigo mostra como gravar scripts de entrada para casos de uso especializados.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha um modelo de aprendizado de máquina treinado que pretende implantar com Azure Machine Learning. Para saber mais sobre a implantação de modelo, consulte [este tutorial](how-to-deploy-and-where.md).

## <a name="automatically-generate-a-swagger-schema"></a>Gerar automaticamente um esquema Swagger

Para gerar automaticamente um esquema para o serviço Web, forneça um exemplo de entrada e/ou saída no construtor para um dos objetos de tipo definidos. O tipo e o exemplo são usados para criar automaticamente o esquema. Azure Machine Learning, em seguida, cria uma especificação de [openapi](https://swagger.io/docs/specification/about/) (Swagger) para o serviço Web durante a implantação.

Atualmente, esses tipos têm suporte:

* `pandas`
* `numpy`
* `pyspark`
* Objeto Python padrão

Para usar a geração de esquema, inclua o pacote de código-fonte aberto `inference-schema` no arquivo de dependências. Para obter mais informações sobre este pacote, consulte [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . Defina os formatos de exemplo de entrada e saída `input_sample` nas `output_sample` variáveis e, que representam os formatos de solicitação e resposta para o serviço Web. Use esses exemplos nos decoradores da função de entrada e saída na `run()` função. O exemplo a seguir scikit-Learn usa a geração de esquema.


## <a name="power-bi-compatible-endpoint"></a>Power BI ponto de extremidade compatível 

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


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> Dados binários (ou seja, imagem)

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

## <a name="cross-origin-resource-sharing-cors"></a>CORS (compartilhamento de recursos entre origens)

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


## <a name="load-registered-models"></a>Carregar modelos registrados

Há duas maneiras de localizar modelos em seu script de entrada:
* `AZUREML_MODEL_DIR`: Uma variável de ambiente que contém o caminho para o local do modelo.
* `Model.get_model_path`: Uma API que retorna o caminho para o arquivo de modelo usando o nome do modelo registrado.

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

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

### <a name="get_model_path"></a>get_model_path

Ao registrar um modelo, você fornece um nome de modelo que é usado para gerenciar o modelo no registro. Use esse nome com o método [Model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) para recuperar o caminho do arquivo de modelo ou arquivos no sistema de arquivos local. Se você registrar uma pasta ou uma coleção de arquivos, essa API retornará o caminho do diretório que contém esses arquivos.

Ao registrar um modelo, você lhe dá um nome. O nome corresponde ao local em que o modelo é colocado, seja localmente ou durante a implantação do serviço.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de uma implantação com falha](how-to-troubleshoot-deployment.md)
* [Implantar no Serviço de Kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicativos cliente para consumir serviços Web](how-to-consume-web-service.md)
* [Atualizar serviço Web](how-to-deploy-update-web-service.md)
* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas de eventos e gatilhos para implantações de modelo](how-to-use-event-grid.md)
