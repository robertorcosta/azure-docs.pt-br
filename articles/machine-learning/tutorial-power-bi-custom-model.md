---
title: 'Tutorial: Criar o modelo preditivo com um Notebook (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Saiba como criar e implantar um modelo de machine learning usando um código em um Jupyter Notebook, de modo que você possa usá-lo para prever resultados no Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370551"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>Tutorial: Integração do Power BI – Criar o modelo preditivo com um Notebook (parte 1 de 2)

Na primeira parte deste tutorial, você treinará e implantará um modelo preditivo de machine learning usando um código em um Jupyter Notebook. Na segunda parte, você usará o modelo para prever resultados no Microsoft Power BI.

Neste tutorial, você:

> [!div class="checklist"]
> * Criará um Jupyter Notebook
> * Criar uma instância de computação do Azure Machine Learning
> * Treinar um modelo de regressão usando o Scikit-learn
> * Implantar o modelo em um ponto de extremidade de pontuação em tempo real

Há três maneiras diferentes de criar e implantar o modelo que você usará no Power BI.  Este artigo aborda a opção A: Treinar e implantar modelos usando Notebooks.  Essa opção mostra uma experiência de criação code-first usando Jupyter Notebooks hospedados no estúdio do Azure Machine Learning. 

Em vez disso, você poderá usar:

* [Opção B: Treinar e implantar modelos usando o designer](tutorial-power-bi-designer-model.md): uma experiência de criação com pouco código usando o Designer (uma interface do usuário do tipo "arrastar e soltar").
* [Opção C: Treinar e implantar modelos usando o ML automatizado](tutorial-power-bi-automated-model.md): uma experiência de criação sem código que automatiza totalmente a preparação de dados e o treinamento do modelo.


## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure ([uma avaliação gratuita está disponível](https://aka.ms/AMLFree)). 
- Um Workspace do Azure Machine Learning. Caso ainda não tenha um workspace, siga [Como criar um Workspace do Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Conhecimento introdutório da linguagem Python e dos fluxos de trabalho de machine learning.

## <a name="create-a-notebook-and-compute"></a>Criar um notebook e uma instância de computação

Na home page do [estúdio do Azure Machine Learning](https://ml.azure.com), selecione **Criar** seguido de **Notebook**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Captura de tela mostrando como criar um notebook":::
 
Você verá uma caixa de diálogo para **Criar um arquivo**. Insira:

1. Um nome de arquivo para seu notebook (por exemplo, `my_model_notebook`)
1. Altere o **Tipo de Arquivo** para **Notebook**

Selecione **Criar**. Em seguida, você precisará criar uma instância de computação e anexá-la ao notebook para executar células de código. Para fazer isso, selecione o ícone de adição na parte superior do notebook:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Captura de tela mostrando como criar uma instância de computação":::

Em seguida, na página **Criar instância de computação**:

1. Escolha um tamanho de máquina virtual de CPU. Para os fins deste tutorial, uma **Standard_D11_v2** (dois núcleos e 14 GB de RAM) será suficiente.
1. Selecione **Avançar**. 
1. Na página **Definir Configurações**, forneça um **Nome de instância de computação** válido (os caracteres válidos são letras maiúsculas e minúsculas, números e o caractere -).
1. Selecione **Criar**.

Você poderá observar no notebook que a cor do círculo ao lado de **Computação** passou a ser ciano, indicando que a instância de computação está sendo criada:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Captura de tela mostrando a instância de computação sendo criada":::

> [!NOTE]
> Pode levar cerca de 2 a 4 minutos para que a computação seja provisionada.

Depois que a instância de computação for provisionada, use o notebook para executar células de código. Por exemplo, digite na célula:

```python
import numpy as np

np.sin(3)
```

Seguido de **SHIFT-ENTER** (ou **Control-ENTER** ou selecione o botão Reproduzir ao lado da célula). A seguinte saída deve ser exibida:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Captura de tela mostrando a execução da célula":::

Agora você está pronto para criar um modelo de machine learning.

## <a name="build-a-model-using-scikit-learn"></a>Criar um modelo usando o Scikit-learn

Neste tutorial, você usará o [conjunto de dados Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), que é disponibilizado em [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/). 


### <a name="import-data"></a>Importar dados

Para importar seus dados, copie e cole o código abaixo em uma nova **célula de código** no seu notebook:

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

O quadro de dados do Pandas `X_df` contém 10 variáveis de entrada de linha de base (como idade, sexo, índice de massa corpórea, pressão arterial média e seis medições de soro sanguíneo). O quadro de dados do Pandas `y_df` é a variável de destino que contém uma medida quantitativa de progressão da doença um ano após a linha de base. Há um total de 442 registros.

### <a name="train-model"></a>Treinar um modelo

Crie uma **célula de código** no seu notebook e copie e cole o snippet de código abaixo, que constrói um modelo de regressão de Ridge e serializa o modelo usando o formato pickle do Python:

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Registre o modelo

Além do conteúdo do próprio arquivo de modelo, seu modelo registrado também armazenará os metadados do modelo (descrição do modelo, marcas e informações de estrutura) que serão úteis ao gerenciar e implantar modelos no workspace. Usando marcas, por exemplo, você pode categorizar seus modelos e aplicar filtros ao listar modelos no workspace. Além disso, marcar esse modelo com a estrutura Scikit-learn simplificará a implantação como um serviço Web, como veremos mais adiante.

Copie e cole o código abaixo em uma nova **célula de código** no seu notebook:

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

Veja também o modelo no estúdio do Azure Machine Learning navegando até **Pontos de extremidade** no menu à esquerda:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Captura de tela mostrando o modelo":::

### <a name="define-the-scoring-script"></a>Definir o script de pontuação

Ao implantar um modelo a ser integrado ao Microsoft Power BI, você precisará definir um *script de pontuação* do Python e um ambiente personalizado. O script de pontuação contém duas funções:

- `init()`: essa função é executada quando o serviço é iniciado. Ela carrega o modelo (observe que o modelo é baixado automaticamente do Registro do modelo) e o desserializa.
- `run(data)`: essa função é executada quando uma chamada é feita ao serviço com alguns dados de entrada que precisam de pontuação. 

>[!NOTE]
> Usamos decoradores do Python para definir o esquema dos dados de entrada e saída, que é importante para que a integração do Microsoft Power BI funcione.

Copie e cole o código abaixo em uma nova **célula de código** no seu notebook. O snippet de código abaixo tem um magic de célula que gravará o código em um arquivo chamado score.py.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definir o ambiente personalizado

Em seguida, precisamos definir o ambiente para pontuar o modelo. Nesse ambiente, precisamos definir os pacotes do Python necessários para o script de pontuação (score.py) definido acima, como o Pandas, o Scikit-learn etc.

Para definir o ambiente, copie e cole o código abaixo em uma nova **célula de código** no seu notebook:

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>Implantar o modelo

Para implantar o modelo, copie e cole o código abaixo em uma nova **célula de código** no seu notebook:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Pode levar de 2 a 4 minutos para que o serviço seja implantado.

Você verá a seguinte saída de um serviço implantado com êxito:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Veja também o serviço no estúdio do Azure Machine Learning navegando até **Pontos de extremidade** no menu à esquerda:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Captura de tela mostrando o ponto de extremidade":::

Recomendamos que você teste o serviço Web para verificar se ele funciona conforme o esperado. Volte ao notebook selecionando **Notebooks** no menu à esquerda do estúdio do Azure Machine Learning. Copie e cole o código abaixo em uma nova **célula de código** no seu notebook para testar o serviço:

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

A saída será semelhante à seguinte estrutura JSON: `{'predict': [[205.59], [68.84]]}`.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu como criar e implantar um modelo de modo que ele possa ser consumido pelo Microsoft Power BI. Na próxima parte, você aprenderá a consumir esse modelo em um relatório do Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumir um modelo no Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
