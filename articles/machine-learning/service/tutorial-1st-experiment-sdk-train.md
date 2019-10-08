---
title: 'Tutorial: Treinar seu primeiro modelo de ML'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você conhecerá os padrões de design básicos do Azure Machine Learning e treinará um modelo simples do Scikit-learn baseado no conjunto de dados de diabetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/03/2019
ms.openlocfilehash: c775b16eaa15ccd7115f4770bf197545a9de2500
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828029"
---
# <a name="tutorial-train-your-first-ml-model"></a>Tutorial: Treinar seu primeiro modelo de ML

Este tutorial é **parte dois de uma série de tutoriais de duas partes**. No tutorial anterior, você [criou um workspace e escolheu um ambiente de desenvolvimento](tutorial-1st-experiment-sdk-setup.md). Neste tutorial, você conhecerá os padrões de design básicos do Azure Machine Learning e treinará um modelo simples do Scikit-learn baseado no conjunto de dados de diabetes. Depois de concluir este tutorial, você terá o conhecimento prático do SDK para escalar verticalmente para o desenvolvimento de experimentos e fluxos de trabalho mais complexos.

Neste tutorial, você aprende as seguintes tarefas:

> [!div class="checklist"]
> * Conectar seu workspace e criar um experimento
> * Carregar dados e treinar modelos scikit-learn
> * Exibir resultados de treinamento no portal
> * Recuperar o melhor modelo

## <a name="prerequisites"></a>Pré-requisitos

O único pré-requisito é executar a parte um deste tutorial, [Configurar o ambiente e o workspace](tutorial-1st-experiment-sdk-setup.md).

Nesta parte do tutorial, você executa o código no Jupyter Notebook de exemplo `tutorials/tutorial-1st-experiment-sdk-train.ipynb` aberto no final da parte um. Este artigo percorre o mesmo código que está no notebook.

## <a name="open-the-notebook"></a>Abrir o notebook

1. Entre na [página de aterrissagem do workspace](https://ml.azure.com/).

1. Abra o **tutorial-1st-experiment-sdk-train.ipynb** em sua pasta, conforme mostrado na [parte um](tutorial-1st-experiment-sdk-setup.md#open).


> [!Warning]
> **Não** *crie* um notebook na interface do Jupyter! O notebook `tutorials/tutorial-1st-experiment-sdk-train.ipynb` inclui **todo o código e dados necessários** para este tutorial.

## <a name="connect-workspace-and-create-experiment"></a>Conectar o workspace e criar um experimento

> [!Important]
> O restante deste artigo contém o mesmo conteúdo que você vê no notebook.  
>
> Alterne para o Jupyter Notebook agora se quiser ler enquanto executa o código. 
> Para executar uma única célula de código em um notebook, clique na célula de código e pressione **Shift + Enter**. Ou execute o notebook inteiro escolhendo a **Executar tudo** na barra de ferramentas superior.

Importe a classe `Workspace` e carregue suas informações de assinatura do arquivo `config.json` usando a função `from_config().`. Isso busca o arquivo JSON no diretório atual por padrão, mas você também pode especificar um parâmetro de caminho para apontar para o arquivo usando `from_config(path="your/file/path")`. Em um servidor de notebook de nuvem, o arquivo fica automaticamente no diretório raiz.

Se o código a seguir solicitar autenticação adicional, basta colar o link em um navegador e inserir o token de autenticação.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Agora, crie um experimento no seu workspace. Um experimento é outro recurso de nuvem fundamental que representa uma coleção de avaliações (execuções de modelo individuais). Neste tutorial, você usa o experimento para criar execuções e acompanhar o treinamento do modelo no portal do Azure. Os parâmetros incluem sua referência de workspace e um nome de cadeia de caracteres para o experimento.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Carregar dados e preparar para o treinamento

Para esse tutorial, você usa o conjunto de dados de diabetes, que é um conjunto de dados previamente normalizado incluído no scikit-learn. Esse conjunto de dados usa recursos como idade, sexo e BMI para prever a progressão da doença diabetes. Carregue os dados da função estática `load_diabetes()` e divida-os em conjuntos de treinamento e de teste usando `train_test_split()`. Essa função separa os dados para que o modelo tenha dados não vistos a serem usados para testar o treinamento na sequência.


```python
from sklearn.datasets import load_diabetes
from sklearn.model_selection import train_test_split

X, y = load_diabetes(return_X_y = True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Treinar um modelo

O treinamento de um modelo simples de scikit-learn pode facilmente ser feito de forma local para o treinamento em pequena escala, mas ao treinar várias iterações com dezenas de permutações de recurso e configurações de parâmetro diferentes, é fácil perder o controle de quais modelos você treinou e como os treinou. O padrão de design a seguir mostra como aproveitar o SDK para controlar facilmente seu treinamento na nuvem.

Crie um script que treina os modelos Ridge em um loop por meio de valores alfa de hiperparâmetro diferentes.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

O código acima realiza o seguinte:

1. Para cada valor de hiperparâmetro alfa na matriz `alphas`, uma nova execução é criada dentro do experimento. O valor alfa é registrado em log para diferenciar entre cada execução.
1. Em cada execução, um modelo Ridge é instanciado, treinado e usado para executar previsões. O root-mean-squared-error é calculado para os valores reais vs previsto e, em seguida, registrado em log para a execução. Neste ponto, a execução tem metadados anexados para o valor alfa e a precisão de RMSE.
1. Em seguida, o modelo para cada execução é serializado e carregado para a execução. Isso permite que você baixe o arquivo de modelo da execução no portal.
1. Ao final de cada iteração, a execução é concluída chamando `run.complete()`.

Após a conclusão do treinamento, chame a variável `experiment` para buscar um link para o experimento no portal.

```python
experiment
```

<table style="width:100%"><tr><th>NOME</th><th>Workspace</th><th>Página de relatório</th><th>Página de documentos</th></tr><tr><td>diabetes-experiment</td><td>your-workspace-name</td><td>Link para o portal do Azure</td><td>Vincular à documentação</td></tr></table>

## <a name="view-training-results-in-portal"></a>Exibir resultados de treinamento no portal

Após o **Link para o portal do Azure**, você será levado para a página principal do experimento. Aqui você vê todas as execuções individuais no experimento. Todos os valores personalizados registrados (`alpha_value` e `rmse`, nesse caso) tornam-se campos para cada execução e também ficam disponíveis para os gráficos e blocos na parte superior da página do experimento. Para adicionar uma métrica registrada a um gráfico ou bloco, passe o mouse sobre ela, clique no botão Editar e localize sua métrica personalizada registrada em log.

Ao treinar modelos em escala em centenas e milhares de execuções separadas, essa página facilita a visualização de todos os modelos treinados, especificamente como eles foram treinados e como suas métricas exclusivas foram alteradas ao longo do tempo.

![Página principal do experimento no portal](./media/tutorial-quickstart/experiment-main.png)

Clicar em um link de número de execução na coluna `RUN NUMBER` leva você até a página para cada execução individual. A guia padrão **Detalhes** mostra informações mais detalhadas sobre cada execução. Navegue até a guia **Saídas** e você verá o arquivo `.pkl` para o modelo que foi carregado para a execução durante cada iteração de treinamento. Aqui você pode baixar o arquivo de modelo, em vez de precisar treiná-lo novamente de forma manual.

![Página de detalhes da execução no portal](./media/tutorial-quickstart/model-download.png)

## <a name="get-the-best-model"></a>Obter o melhor modelo

Além de poder baixar os arquivos de modelo do experimento no portal, você também pode baixá-los programaticamente. O código a seguir itera em cada execução no experimento e acessa as métricas de execução registradas e os detalhes de execução (que contém a run_id). Isso mantém o controle da melhor execução, nesse caso, a execução com o menor root-mean-squared-error.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Use a melhor ID de execução para buscar a execução individual usando o construtor `Run` junto com o objeto do experimento. Em seguida, chame `get_file_names()` para ver todos os arquivos disponíveis para download nesta execução. Nesse caso, você carregou apenas um arquivo para cada execução durante o treinamento.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Chame `download()` no objeto de execução, especificando o nome do arquivo de modelo a ser baixado. Por padrão, essa função é baixada para o diretório atual.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Limpar recursos

Não conclua esta seção se você planeja executar outros tutoriais do Azure Machine Learning.

### <a name="stop-the-notebook-vm"></a>Interromper a VM de notebook

Se você usou um servidor de notebook de nuvem, pare a VM quando não a estiver usando para reduzir o custo.

1. Em seu workspace, selecione **VMs de Notebook**.

   ![Interromper o servidor da VM](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. Selecione a VM na lista.

1. Selecione **Interromper**.

1. Quando estiver pronto para usar o servidor novamente, selecione **Iniciar**.

### <a name="delete-everything"></a>Excluir tudo

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Você também pode manter o grupo de recursos, mas excluir um único workspace. Exiba as propriedades do workspace e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você executou as seguintes tarefas:

> [!div class="checklist"]
> * Conectou seu workspace e criou um experimento
> * Carregou dados e treinou modelos scikit-learn
> * Exibiu resultados de treinamento no portal e recuperou modelos

[Implantar seu modelo](tutorial-deploy-models-with-aml.md) com o Azure Machine Learning.
Saiba como desenvolver experimentos de [aprendizado de máquina automatizado](tutorial-auto-train-models.md).
