---
title: Log ML experimentos & métricas
titleSuffix: Azure Machine Learning
description: Monitore seus experimentos do Azure ML e monitore as métricas de execução para aprimorar o processo de criação de modelo. Adicione o registro em log ao script de treinamento e exiba os resultados registrados de uma execução.  Use execute. log, Run. start_logging ou ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.custom: seodec18
ms.openlocfilehash: 2d8bf44f5e5e7a3f8c328a47480599f9dd18b845
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489535"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitorar execuções e métricas de experimento do Azure ML
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprimore o processo de criação de modelo rastreando suas experiências e monitorando métricas de execução. Neste artigo, saiba como adicionar o código de registro em log ao script de treinamento, enviar uma execução de experimento, monitorar a execução e inspecionar os resultados em Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning também pode registrar informações de outras fontes durante o treinamento, como execuções automatizadas do Machine Learning ou o contêiner do Docker que executa o trabalho de treinamento. Esses logs não estão documentados. Se você encontrar problemas e entrar em contato com o suporte da Microsoft, eles podem ser capazes de usar esses logs durante a solução de problemas.

## <a name="available-metrics-to-track"></a>Métricas disponíveis para acompanhar

As métricas a seguir podem ser adicionadas a uma execução durante o treinamento de um experimento. Para exibir uma lista mais detalhada do que pode ser acompanhado em uma execução, veja a [Documentação de referência de classe de execução](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Tipo| Função do Python | Observações|
|----|:----|:----|
|Valores escalares |Função:<br>`run.log(name, value, description='')`<br><br>Exemplo:<br>run.log("accuracy", 0.95) |Registre um valor numérico ou de string para a execução com o nome dado. Registrar uma métrica em log para uma execução faz essa métrica ser armazenada no registro de execução no experimento.  Você pode registrar em log a mesma métrica várias vezes dentro de uma execução, sendo o resultado considerado um vetor dessa métrica.|
|Listas|Função:<br>`run.log_list(name, value, description='')`<br><br>Exemplo:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Faça uma lista de valores para a execução com o nome fornecido.|
|Linha|Função:<br>`run.log_row(name, description=None, **kwargs)`<br>Exemplo:<br>run.log_row("Y over X", x=1, y=0.4) | O uso de *log_row* cria uma métrica com várias colunas, conforme descrito em kwargs. Cada parâmetro nomeado gera uma coluna com o valor especificado.  *log_row* pode ser chamado uma vez para registrar em log uma tupla arbitrária ou várias vezes em loop para gerar uma tabela completa.|
|Tabela|Função:<br>`run.log_table(name, value, description='')`<br><br>Exemplo:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Registre um objeto de dicionário na execução com o nome dado. |
|Imagens|Função:<br>`run.log_image(name, path=None, plot=None)`<br><br>Exemplo:<br>`run.log_image("ROC", plt)` | Faça logon uma imagem ao registro de execução. Use log_image para registrar em log um arquivo de imagem ou um gráfico matplotlib para a execução.  Essas imagens serão visíveis e comparáveis no registro de execução.|
|Marcar uma execução|Função:<br>`run.tag(key, value=None)`<br><br>Exemplo:<br>run.tag("selected", "yes") | Marque a execução com uma chave de cadeia de caracteres e um valor de cadeia de caracteres opcional.|
|Carregar arquivo ou diretório|Função:<br>`run.upload_file(name, path_or_stream)`<br> <br> Exemplo:<br>Run.upload_file ("best_model.pkl", ". / pkl") | Carregar um arquivo para o registro de execução. Execuções de capturam automaticamente os arquivos no diretório de saída especificado, cujo padrão é ". /outputs" para a maioria dos tipos de execução.  Use upload_file somente quando arquivos adicionais precisarem ser carregados ou um diretório de saída não for especificado. Sugerimos adicionar `outputs` ao nome para que ele seja carregado para o diretório de saídas. Você pode listar todos os arquivos associados a esse registro de execução pelo `run.get_file_names()` chamado|

> [!NOTE]
> Métricas para escalares, listas, linhas e tabelas podem ter o tipo: flutuante, inteiro ou cadeia de caracteres.

## <a name="choose-a-logging-option"></a>Escolha uma opção de log

Se você quiser acompanhar ou monitorar seu experimento, deverá adicionar código para iniciar o log ao enviar a execução. Estas são maneiras de disparar o envio de execução:
* __Run.start_logging__ – adicione funções de registro em log ao seu script de treinamento e inicie uma sessão de registro em log interativa no experimento especificado. **start_logging** cria uma execução interativa para uso em cenários como blocos de anotações. Qualquer métrica registrada em log durante a sessão é adicionada ao registro de execução no experimento.
* __ScriptRunConfig__ – adicione funções de registro em log ao seu script de treinamento e carregue toda a pasta de script com a execução.  **ScriptRunConfig** é uma classe para definir as configurações para execuções do script. Com essa opção, você pode adicionar código de monitoramento para ser notificado da conclusão ou obter um widget visual para monitorar.

## <a name="set-up-the-workspace"></a>Configurar o workspace
Antes de adicionar o registro em log e enviar um experimento, você deve configurar o workspace.

1. Carregar o workspace. Para saber mais sobre como definir a configuração do espaço de trabalho, consulte [arquivo de configuração do espaço de trabalho](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Opção 1: Usar start_logging

**start_logging** cria uma execução interativa para uso em cenários como blocos de anotações. Qualquer métrica registrada em log durante a sessão é adicionada ao registro de execução no experimento.

O exemplo a seguir treina um modelo simples de sklearn Ridge localmente em um Jupyter Notebook local. Para saber mais sobre como enviar experimentos para ambientes diferentes, confira [Configurar destinos de computação para treinamento de modelo com o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Crie um script de treinamento em um Jupyter Notebook local. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Adicione o acompanhamento de experimento usando o SDK do Azure Machine Learning e carregue um modelo persistente no registro de execução do experimento. O código a seguir adiciona marcas, logs e carrega um arquivo de modelo para a execução do experimento.

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    O script termina com ```run.complete()```, que marca a execução como concluída.  Normalmente, essa função é usada em cenários interativos de notebook.

## <a name="option-2-use-scriptrunconfig"></a>Opção 2: Usar ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) é uma classe para configurar as configurações para execuções de script. Com essa opção, você pode adicionar código de monitoramento para ser notificado da conclusão ou obter um widget visual para monitorar.

Este exemplo expande o modelo básico do sklearn Ridge acima. Ele faz uma limpeza simples de parâmetro dos valores alfa do modelo para capturar métricas e modelos treinados nas execuções sob o experimento. O exemplo é executado localmente em um ambiente gerenciado pelo usuário. 

1. Crie um script de treinamento `train.py`.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. O script `train.py` referencia `mylib.py`, o que permite que você obtenha a lista de valores alfa para usar no modelo ridge.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Configure um ambiente local gerenciado pelo usuário.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Envie o script ```train.py``` para execução no ambiente gerenciado pelo usuário. Toda essa pasta de script é enviada para treinamento, incluindo o arquivo ```mylib.py```.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Gerenciar uma execução

O artigo [Iniciar, monitorar e cancelar execuções de treinamento](how-to-manage-runs.md) destaca Azure Machine Learning fluxos de trabalho específicos sobre como gerenciar seus experimentos.

## <a name="view-run-details"></a>Exibir detalhes de execução

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Monitor executar com o widget do Jupyter Notebook
Ao usar o método **ScriptRunConfig** para enviar execuções, você pode observar o progresso da execução com um [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Como o envio de execução, o widget é assíncrono e fornece atualizações ao vivo a cada 10 a 15 segundos até que o trabalho seja concluído.

1. Exiba o widget do Jupyter enquanto aguarda a execução ser concluída.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Captura de tela do widget do Jupyter Notebook](./media/how-to-track-experiments/run-details-widget.png)

Você também pode obter um link para a mesma exibição em seu espaço de trabalho.

```python
print(run.get_portal_url())
```

2. **[Para execuções automatizadas do aprendizado de máquina]**  Para acessar os gráficos a partir de uma execução anterior. Substitua `<<experiment_name>>` pelo nome de experimento apropriado:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget do notebook Jupyter para Machine Learning automatizado](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Para exibir mais detalhes de um pipeline, clique no pipeline que você gostaria de explorar na tabela e os gráficos serão renderizados em um pop-up do Azure Machine Learning Studio.

### <a name="get-log-results-upon-completion"></a>Obter resultados de log após a conclusão

O treinamento e o monitoramento do modelo ocorrem em segundo plano para que você possa executar outras tarefas enquanto aguarda. Você também pode esperar até o modelo ter concluído o treinamento antes de executar mais código. Quando você usa **ScriptRunConfig**, pode usar ```run.wait_for_completion(show_output = True)``` para mostrar quando o treinamento do modelo está concluído. O sinalizador ```show_output``` fornece saída detalhada. 


### <a name="query-run-metrics"></a>Métricas de execução de consulta

Você pode exibir as métricas de um modelo treinado usando ```run.get_metrics()```. Agora você pode obter todas as métricas registradas no exemplo anterior para determinar o melhor modelo.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studiohttpsmlazurecom"></a>Exibir o experimento em seu espaço de trabalho no [Azure Machine Learning Studio](https://ml.azure.com)

Quando a execução de um experimento estiver concluída, você poderá navegar até o registro de execução do experimento gravado. Você pode acessar o histórico do [Azure Machine Learning Studio](https://ml.azure.com).

Navegue até a guia experimentos e selecione seu experimento. Você é levado para o painel Executar teste, no qual você pode ver as métricas e os gráficos acompanhados que são registrados para cada execução. Neste caso, registramos em log MSE e os valores alfa.

  ![Detalhes da execução no Azure Machine Learning Studio](./media/how-to-track-experiments/experiment-dashboard.png)

Você pode fazer uma busca detalhada em uma execução específica para exibir suas saídas ou logs ou baixar o instantâneo do teste enviado para que você possa compartilhar a pasta experimento com outras pessoas.

### <a name="viewing-charts-in-run-details"></a>Exibindo gráficos nos detalhes de execução

Há várias maneiras de usar as APIs de log para registrar diferentes tipos de métricas durante uma execução e exibi-las como gráficos no Azure Machine Learning Studio.

|Valor conectado|Código de exemplo| Visualizar no portal|
|----|----|----|
|Registrar uma matriz de valores numéricos| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|gráfico de linhas de variável único|
|Registre um único valor numérico com o mesmo nome de métrica usado repetidamente (como em um loop for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Gráfico de linhas de variável-único|
|Faça uma linha com colunas numéricas 2 repetidamente|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Gráfico de linhas de duas variáveis|
|Tabela de log com 2 colunas numéricas|`run.log_table(name='Sine Wave', value=sines)`|Gráfico de linhas de duas variáveis|


## <a name="example-notebooks"></a>Blocos de anotações de exemplo
Os seguintes blocos de anotações demonstram conceitos neste artigo:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

Experimente estas próximas etapas para aprender a usar o SDK do Azure Machine Learning para Python:

* Veja um exemplo de como registrar o melhor modelo e implantá-lo no tutorial [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

* Saiba como [Treinar modelos PyTorch com o Azure Machine Learning](how-to-train-pytorch.md).
