---
title: Converter um código de experimento de aprendizado de máquina em um código de produção
titleSuffix: Azure Machine Learning
description: Saiba como converter um código experimental de aprendizado de máquina em um código de produção usando o modelo de código MLOpsPython.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 1be97f9754733561111cedcf95fe237b7788583e
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122551"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Tutorial: Converter um código experimental de ML em um código de produção

Um projeto de aprendizado de máquina exige experimentação, em que as hipóteses são testadas com ferramentas Agile como o Jupyter Notebook usando conjuntos de dados reais. Depois que o modelo estiver pronto para produção, o código do modelo deverá ser colocado em um repositório de códigos de produção. Em alguns casos, o código do modelo precisará ser convertido em scripts Python para serem colocados no repositório de códigos de produção. Este tutorial apresenta uma abordagem recomendada sobre como exportar o código de experimentação para scripts Python.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Limpar o código não essencial
> * Refatorar o código do Jupyter Notebook em funções
> * Criar scripts Python para tarefas relacionadas
> * Criar testes de unidade

## <a name="prerequisites"></a>Pré-requisitos

- Gere o [modelo MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) e use os notebooks `experimentation/Diabetes Ridge Regression Training.ipynb` e `experimentation/Diabetes Ridge Regression Scoring.ipynb`. Esses notebooks são usados como um exemplo de conversão de experimentação para produção.
- Instale o nbconvert. Siga apenas as instruções de instalação da seção __Como instalar o nbconvert__ na página [Instalação](https://nbconvert.readthedocs.io/en/latest/install.html).

## <a name="remove-all-nonessential-code"></a>Remover todo o código não essencial

Um código escrito durante a experimentação é destinado apenas a fins exploratórios. Portanto, a primeira etapa para converter um código experimental em um código de produção é remover esse código não essencial. A remoção do código não essencial também facilitará a manutenção do código. Nesta seção, você removerá o código do notebook `experimentation/Diabetes Ridge Regression Training.ipynb`. As instruções que imprimem a forma de `X` e `y` e a chamada de célula `features.describe` servem apenas para exploração de dados e podem ser removidas. Após a remoção do código não essencial, `experimentation/Diabetes Ridge Regression Training.ipynb` deverá se parecer com o seguinte código sem markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
 
X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refatorar o código em funções

Em segundo lugar, o código do Jupyter precisa ser refatorado em funções. A refatoração do código em funções facilita o teste de unidade e torna o código mais sustentável. Nesta seção, você vai refatorar:
- O notebook Treinamento de Regressão de Sulcos Causados por Diabetes (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- O notebook Pontuação de Regressão de Sulcos Causados por Diabetes (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refatorar o notebook Treinamento de Regressão de Sulcos Causados por Diabetes em funções
Em `experimentation/Diabetes Ridge Regression Training.ipynb`, conclua as seguintes etapas:

1. Crie uma função chamada `train_model`, que usa os parâmetros `data` e `alpha` e retorna um modelo. 
1. Copie o código nos títulos “Treinar Modelo no Conjunto de Treinamento” e “Validar Modelo no Conjunto de Validação” para a função `train_model`.

A função `train_model` deverá ser semelhante ao seguinte código:

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

Depois que a função `train_model` for criada, substitua o código nos títulos “Treinar Modelo no Conjunto de Treinamento” e “Validar Modelo no Conjunto de Validação” pela seguinte instrução:

```python
reg = train_model(data, alpha)
```

A instrução anterior chama a função `train_model` transmitindo os parâmetros `data` e `alpha` e retorna o modelo.

Em `experimentation/Diabetes Ridge Regression Training.ipynb`, conclua as seguintes etapas:

1. Crie uma função chamada `main`, que não usa nenhum parâmetro e não retorna nada.
1. Copie o código nos títulos “Carregar Dados”, “Dividir Dados em Conjuntos de Treinamento e Validação” e “Salvar Modelo” na função `main`.
1. Copie a chamada recém-criada a `train_model` para a função `main`.

A função `main` deverá ser semelhante ao seguinte código:

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

Depois que a função `main` for criada, substitua todo o código nos títulos “Carregar Dados”, “Dividir Dados em Conjuntos de Treinamento e Validação” e “Salvar Modelo” junto com a chamada recém-criada para `train_model` pela seguinte instrução:

```python
main()
```

Após a refatoração, `experimentation/Diabetes Ridge Regression Training.ipynb` deverá ser semelhante ao seguinte código sem markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refatorar o notebook Pontuação de Regressão de Sulcos Causados por Diabetes em funções
Em `experimentation/Diabetes Ridge Regression Scoring.ipynb`, conclua as seguintes etapas:

1. Crie uma função chamada `init`, que não usa nenhum parâmetro e não retorna nada.
1. Copie o código no título “Carregar Modelo” na função `init`.

A função `init` deverá ser semelhante ao seguinte código:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Depois que a função `init` for criada, substitua todo o código no título “Carregar Modelo” por uma só chamada para `init` da seguinte maneira:

```python
init()
```

Em `experimentation/Diabetes Ridge Regression Scoring.ipynb`, conclua as seguintes etapas:

1. Crie uma função chamada `run`, que usa `raw_data` e `request_headers` como parâmetros e retorna um dicionário de resultados da seguinte maneira:

    ```python
    {"result": result.tolist()}
    ```

1. Copie o código nos títulos “Preparar Dados” e “Dados de Pontuação” na função `run`.

    A função `run` deverá ser semelhante ao seguinte código (lembre-se de remover as instruções que definem as variáveis `raw_data` e `request_headers`, que serão usadas posteriormente quando a função `run` for chamada):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Depois que a função `run` for criada, substitua todo o código nos cabeçalhos “Preparar Dados” e “Pontuar Dados” pelo seguinte código:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```
O código anterior define as variáveis `raw_data` e `request_header`, chama a função `run` com `raw_data` e `request_header` e imprime as previsões.

Após a refatoração, `experimentation/Diabetes Ridge Regression Scoring.ipynb` deverá ser semelhante ao seguinte código sem markdown:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Combinar funções relacionadas em arquivos Python
Em terceiro lugar, as funções relacionadas precisam ser mescladas em arquivos Python para ajudar a aprimorar a reutilização de código. Nesta seção, você criará arquivos Python para os seguintes notebooks:
- O notebook Treinamento de Regressão de Sulcos Causados por Diabetes (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- O notebook Pontuação de Regressão de Sulcos Causados por Diabetes (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Criar um arquivo Python para o notebook Treinamento de Regressão de Sulcos Causados por Diabetes
Converta o notebook em um script executável executando a seguinte instrução em um prompt de comando, que usa o pacote nbconvert e o caminho de `experimentation/Diabetes Ridge Regression Training.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Depois que o notebook for convertido em `train.py`, remova todos os comentários. O arquivo `train.py` deverá ser semelhante ao seguinte código:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

O arquivo `train.py` encontrado no diretório `diabetes_regression/training` no repositório MLOpsPython dá suporte a argumentos de linha de comando (a saber, `build_id`, `model_name` e `alpha`). O suporte a argumentos de linha de comando pode ser adicionado ao arquivo `train.py` para dar suporte a nomes de modelo dinâmico e valores `alpha`, mas não é necessário para que o código seja executado com êxito.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Criar um arquivo Python para o notebook Pontuação de Regressão de Sulcos Causados por Diabetes
Converta o notebook em um script executável executando a seguinte instrução em um prompt de comando que usa o pacote nbconvert e o caminho de `experimentation/Diabetes Ridge Regression Scoring.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Depois que o notebook for convertido em `score.py`, remova todos os comentários. O arquivo `score.py` deverá ser semelhante ao seguinte código:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

A função `train_model` precisa de uma modificação para criar uma instância de um modelo de variável global, de modo que fique visível em todo o script. Adicione a seguinte instrução ao início da função `init`:

```python
global model
```

Após a adição da instrução anterior, a função `init` deverá ser semelhante ao seguinte código:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Criar testes de unidade para cada arquivo Python
Em quarto lugar, testes de unidade precisam ser criados para cada arquivo Python, o que torna o código mais robusto e mais fácil de ser mantido. Nesta seção, você criará um teste de unidade para uma das funções de `train.py`.

`train.py` contém duas funções: `train_model` e `main`. Cada função precisa de um teste de unidade, mas criaremos apenas um só teste de unidade para a função `train_model` usando a estrutura Pytest neste tutorial.  O Pytest não é a única estrutura de teste de unidade do Python, mas é uma das mais comumente usadas. Para obter mais informações, visite [Pytest](https://pytest.org).

Um teste de unidade geralmente contém três ações principais:
- Organizar objeto: criação e configuração dos objetos necessários
- Executar uma ação em um objeto
- Declarar o que é esperado

Uma condição comum para `train_model` é quando `data` e um valor `alpha` são transmitidos. O resultado esperado é que as funções `Ridge.train` e `Ridge.predict` sejam chamadas. Como os métodos de treinamento de aprendizado de máquina geralmente não são de execução rápida, a chamada a `Ridge.train` será simulada. Como o valor retornado de `Ridge.train` é um objeto fictício, também simularemos `Ridge.predict`. O teste de unidade para `train_model` que testa a transmissão de `data` e um valor `alpha` com o resultado esperado de `Ridge.train` e as funções `Ridge.predict` que são chamadas com a simulação e a estrutura Pytest deverão ser semelhantes ao seguinte código:

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch(Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Usar o próprio modelo com o modelo de código MLOpsPython
Se você estiver seguindo as etapas neste guia, terá um conjunto de scripts correlacionados aos scripts de treinamento/pontuação/teste disponíveis no repositório MLOpsPython.  De acordo com a estrutura mencionada acima, as seguintes etapas descreverão o que é necessário para usar esses arquivos para o próprio projeto de aprendizado de máquina:  

1.  Seguir o guia de introdução
2.  Substituir o código de treinamento
3.  Substituir o código de pontuação
4.  Atualizar o código de avaliação

### <a name="follow-the-getting-started-guide"></a>Seguir o guia de introdução
É necessário seguir o guia de introdução para ter a infraestrutura de suporte e os pipelines para executar o MLOpsPython.  Recomendamos a implantação do código MLOpsPython no estado em que se encontra antes de colocá-lo no próprio código para garantir que a estrutura e o pipeline estejam funcionando corretamente.  Também é útil se familiarizar com a estrutura de código do repositório.

### <a name="replace-training-code"></a>Substituir o código de treinamento
É necessário substituir o código usado para treinar o modelo e remover ou substituir os testes de unidade correspondentes para que a solução funcione com o próprio código.  Siga estas etapas especificamente:

1. Substituir `diabetes_regression\training\train.py`. Esse script treina o modelo localmente ou na Computação do Azure ML.
1. Remover ou substituir os testes de unidade de treinamento encontrados em `tests/unit/code_test.py`

### <a name="replace-score-code"></a>Substituir o código de pontuação
Para que o modelo forneça funcionalidades de inferência em tempo real, o código de pontuação precisará ser substituído. O modelo MLOpsPython usa o código de pontuação para implantar o modelo a fim de fazer a pontuação em tempo real na ACI, no AKS ou em aplicativos Web.  Caso deseje manter a pontuação, substitua `diabetes_regression/scoring/score.py`.

### <a name="update-evaluation-code"></a>Atualizar o código de avaliação
O modelo MLOpsPython usa o script evaluate_model para comparar o desempenho do modelo recém-treinado e do modelo de produção atual com base no Erro Quadrático Médio. Se o desempenho do modelo recém-treinado for melhor do que o do modelo de produção atual, os pipelines continuarão. Caso contrário, os pipelines serão interrompidos. Para manter a avaliação, substitua todas as instâncias de `mse` em `diabetes_regression/evaluate/evaluate_model.py` pela métrica desejada. 

Para eliminar a avaliação, defina a variável de pipeline de DevOps `RUN_EVALUATION` em `.pipelines\diabetes_regression-variables` como `false`.

## <a name="next-steps"></a>Próximas etapas

Agora que você entendeu como fazer a conversão de um experimento para um código de produção, use os seguintes links para saber como monitorar execuções de experimento e modelos implantados como serviços Web:

> [!div class="nextstepaction"]
> [Monitorar execuções de experimento e métricas do Azure ML](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [Monitorar e coletar dados de pontos de extremidade de serviço Web do ML](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insight)
