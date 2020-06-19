---
title: Registrar em log experimentos e métricas de ML
titleSuffix: Azure Machine Learning
description: Monitore seus experimentos do Azure ML e monitore as métricas de execução para aprimorar o processo de criação de modelo. Adicione o registro em log ao script de treinamento e exiba os resultados registrados de uma execução.  Use execute.log, Run.start_logging ou ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: 9613b74b727d27bd47a05fadc1398bf898f667a5
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835709"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitorar métricas e execuções de experimento do Azure ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprimore o processo de criação de modelo acompanhando seus experimentos e monitorando métricas de execução. Neste artigo, saiba como adicionar o registro em log do código ao script de treinamento, enviar uma execução de experimento, monitorar essa execução e inspecionar os resultados no Azure Machine Learning.

> [!NOTE]
> O Azure Machine Learning também pode registrar em log informações de outras fontes durante o treinamento, como execuções automatizadas do Machine Learning ou o contêiner do Docker que executa o trabalho de treinamento. Esses logs não estão documentados. Se você encontrar problemas e entrar em contato com o suporte da Microsoft, eles poderão usar esses logs na solução.

> [!TIP]
> Este documento oferece informações principalmente para cientistas de dados e desenvolvedores que querem monitorar o processo de treinamento de modelo. Se você for um administrador interessado em monitorar o uso de recursos e os eventos do Azure Machine Learning, como cotas, execuções de treinamento concluídas ou implantações de modelo concluídas, consulte [Monitoramento do Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Métricas disponíveis para acompanhar

As métricas a seguir podem ser adicionadas a uma execução durante o treinamento de um experimento. Para exibir uma lista mais detalhada do que pode ser acompanhado em uma execução, veja a [Documentação de referência de classe de execução](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Type| Função do Python | Observações|
|----|:----|:----|
|Valores escalares |Função:<br>`run.log(name, value, description='')`<br><br>Exemplo:<br>run.log("accuracy", 0.95) |Registre um valor numérico ou de string para a execução com o nome dado. Registrar uma métrica em log para uma execução faz essa métrica ser armazenada no registro de execução no experimento.  Você pode registrar em log a mesma métrica várias vezes dentro de uma execução, sendo o resultado considerado um vetor dessa métrica.|
|Listas|Função:<br>`run.log_list(name, value, description='')`<br><br>Exemplo:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Faça uma lista de valores para a execução com o nome fornecido.|
|Linha|Função:<br>`run.log_row(name, description=None, **kwargs)`<br>Exemplo:<br>run.log_row("Y over X", x=1, y=0.4) | O uso de *log_row* cria uma métrica com várias colunas, conforme descrito em kwargs. Cada parâmetro nomeado gera uma coluna com o valor especificado.  *log_row* pode ser chamado uma vez para registrar em log uma tupla arbitrária ou várias vezes em loop para gerar uma tabela completa.|
|Tabela|Função:<br>`run.log_table(name, value, description='')`<br><br>Exemplo:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Registre um objeto de dicionário na execução com o nome dado. |
|Imagens|Função:<br>`run.log_image(name, path=None, plot=None)`<br><br>Exemplo:<br>`run.log_image("ROC", plot=plt)` | Faça logon uma imagem ao registro de execução. Use log_image para registrar em log um arquivo de imagem .PNG ou um gráfico matplotlib para a execução.  Essas imagens serão visíveis e comparáveis no registro de execução.|
|Marcar uma execução|Função:<br>`run.tag(key, value=None)`<br><br>Exemplo:<br>run.tag("selected", "yes") | Marque a execução com uma chave de cadeia de caracteres e um valor de cadeia de caracteres opcional.|
|Carregar arquivo ou diretório|Função:<br>`run.upload_file(name, path_or_stream)`<br> <br> Exemplo:<br>Run.upload_file ("best_model.pkl", ". / pkl") | Carregar um arquivo para o registro de execução. Execuções de capturam automaticamente os arquivos no diretório de saída especificado, cujo padrão é ". /outputs" para a maioria dos tipos de execução.  Use upload_file somente quando arquivos adicionais precisarem ser carregados ou um diretório de saída não for especificado. Sugerimos adicionar `outputs` ao nome para que ele seja carregado para o diretório de saídas. Você pode listar todos os arquivos associados a esse registro de execução pelo `run.get_file_names()` chamado|

> [!NOTE]
> Métricas para escalares, listas, linhas e tabelas podem ter o tipo: flutuante, inteiro ou cadeia de caracteres.

## <a name="choose-a-logging-option"></a>Escolher uma opção de registro em log

Se você quiser acompanhar ou monitorar seu experimento, deverá adicionar código para iniciar o log ao enviar a execução. Estas são maneiras de disparar o envio de execução:
* __Run.start_logging__ – adicione funções de registro em log ao seu script de treinamento e inicie uma sessão de registro em log interativa no experimento especificado. **start_logging** cria uma execução interativa para uso em cenários como blocos de anotações. Qualquer métrica registrada em log durante a sessão é adicionada ao registro de execução no experimento.
* __ScriptRunConfig__ – adicione funções de registro em log ao seu script de treinamento e carregue toda a pasta de script com a execução.  **ScriptRunConfig** é uma classe para definir as configurações para execuções do script. Com essa opção, você pode adicionar código de monitoramento para ser notificado da conclusão ou obter um widget visual para monitorar.
* __Registro em log do Designer__ - adicione funções de registro em log a um pipeline do designer arrastar e soltar usando o módulo __Executar script Python__. Adicione código Python a experimentos de registro em log do designer. 

## <a name="set-up-the-workspace"></a>Configurar o workspace
Antes de adicionar o registro em log e enviar um experimento, você deve configurar o workspace.

1. Carregar o workspace. Para saber mais sobre como definir a configuração do workspace, confira [arquivo de configuração do workspace](how-to-configure-environment.md#workspace).

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_ws)]


## <a name="option-1-use-start_logging"></a>Opção 1: Usar o start_logging

**start_logging** cria uma execução interativa para uso em cenários como blocos de anotações. Qualquer métrica registrada em log durante a sessão é adicionada ao registro de execução no experimento.

O exemplo a seguir treina um modelo simples de sklearn Ridge localmente em um Jupyter Notebook local. Para saber mais sobre o envio de experimentos em ambientes diferentes, confira [Configurar destinos de computação para treinamento de modelo com o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets).

### <a name="load-the-data"></a>Carregar os dados

Este exemplo usa o conjunto de dados de diabetes, um pequeno conjunto de dados conhecido fornecido scikit-learn. Esta célula carrega o conjunto de dados e divide-o em conjuntos aleatórios de treinamento e teste.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_data)]

### <a name="add-tracking"></a>Adicionar acompanhamento
Adicione o acompanhamento de experimento usando o SDK do Azure Machine Learning e carregue um modelo persistente para o registro de execução do experimento. O código a seguir adiciona marcas, logs e carrega um arquivo de modelo para a execução do experimento.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

O script termina com ```run.complete()```, que marca a execução como concluída.  Normalmente, essa função é usada em cenários interativos de notebook.

## <a name="option-2-use-scriptrunconfig"></a>Opção 2: Usar ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) é uma classe de definição de configurações das execuções de script. Com essa opção, você pode adicionar código de monitoramento para ser notificado da conclusão ou obter um widget visual para monitorar.

Este exemplo expande o modelo básico do sklearn Ridge acima. Ele faz uma limpeza simples de parâmetro dos valores alfa do modelo para capturar métricas e modelos treinados nas execuções sob o experimento. O exemplo é executado localmente em um ambiente gerenciado pelo usuário. 

1. Crie um script de treinamento `train.py`.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. O script `train.py` referencia `mylib.py`, o que permite que você obtenha a lista de valores alfa para usar no modelo ridge.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Configure um ambiente local gerenciado pelo usuário.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=user_managed_env)]


4. Envie o script ```train.py``` para execução no ambiente gerenciado pelo usuário. Toda essa pasta de script é enviada para treinamento, incluindo o arquivo ```mylib.py```.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

## <a name="option-3-log-designer-experiments"></a>Opção 3: Registrar em log experimentos do designer

Use o módulo __Executar script Python__ para adicionar o registro em log lógico aos experimentos do designer. Você pode registrar em log qualquer valor usando esse fluxo de trabalho, mas é especialmente útil para registrar em log métricas do módulo __Avaliar Modelo__ para acompanhar o desempenho do modelo entre diferentes execuções.

1. Conecte um módulo __Executar script Python__ à saída do módulo __Avaliar Modelo__.

    ![Conectar o módulo Executar script Python ao módulo Avaliar Modelo](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Cole o código a seguir no editor de código __Executar script Python__ para registrar em log o erro absoluto médio do modelo treinado:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1 = None, dataframe2 = None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')

        from azureml.core import Run

        run = Run.get_context()

        # Log the mean absolute error to the current run to see the metric in the module detail pane.
        run.log(name='Mean_Absolute_Error', value=dataframe1['Mean_Absolute_Error'])

        # Log the mean absolute error to the parent run to see the metric in the run details page.
        run.parent.log(name='Mean_Absolute_Error', value=dataframe1['Mean_Absolute_Error'])
    
        return dataframe1,
    ```

## <a name="manage-a-run"></a>Gerenciar uma execução

O artigo [Iniciar, monitorar e cancelar execuções de treinamento](how-to-manage-runs.md) destaca fluxos de trabalho específicos do Azure Machine Learning para gerenciamento dos seus experimentos.

## <a name="view-run-details"></a>Exibir detalhes de execução

### <a name="view-activequeued-runs-from-the-browser"></a>Exibir execuções ativas/em fila no navegador

Os destinos de computação usados para treinar modelos são um recurso compartilhado. Assim, eles podem ter várias execuções em fila ou ativas em um determinado momento. Para ver as execuções de um destino de computação específico do navegador, use as seguintes etapas:

1. No [estúdio do Azure Machine Learning](https://ml.azure.com/), selecione o workspace e escolha __Computação__, no lado esquerdo da página.

1. Selecione __Clusters de Treinamento__ para exibir uma lista de destinos de computação usados para treinamento. Em seguida, selecione o cluster.

    ![Selecionar o cluster de treinamento](./media/how-to-track-experiments/select-training-compute.png)

1. Selecione __Execuções__. A lista de execuções que usam esse cluster é exibida. Para exibir detalhes de uma execução específica, use o link na coluna __Executar__. Para exibir detalhes do experimento, use o link na coluna __Experimento__.

    ![Selecionar execuções do cluster de treinamento](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Uma execução pode conter execuções filhas. Portanto, um trabalho de treinamento pode resultar em várias entradas.

Quando uma execução é concluída, ela deixa de ser exibida nessa página. Para exibir informações sobre execuções concluídas, visite a seção __Experimentos__ do estúdio e selecione o experimento e a execução. Para saber mais, confira a seção [Métricas de execução de consulta](#queryrunmetrics).

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Monitorar execução com widgets do Jupyter Notebook
Quando você usa o método **ScriptRunConfig** para enviar execuções, pode inspecionar o progresso da execução com um [widget do Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Como o envio de execução, o widget é assíncrono e fornece atualizações ao vivo a cada 10 a 15 segundos até que o trabalho seja concluído.

1. Exiba o widget do Jupyter enquanto aguarda a execução ser concluída.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Captura de tela do widget do Jupyter Notebook](./media/how-to-track-experiments/run-details-widget.png)

   Você também pode obter um link para a mesma exibição no seu workspace.

   ```python
   print(run.get_portal_url())
   ```

2. **[Para execuções automatizadas do aprendizado de máquina]**  Para acessar os gráficos a partir de uma execução anterior. Substitua `<<experiment_name>>` pelo nome do experimento apropriado:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget do notebook Jupyter para Machine Learning automatizado](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Para exibir mais detalhes de um pipeline, clique naquele que você gostaria de explorar na tabela e os gráficos serão renderizados em um pop-up do estúdio do Azure Machine Learning.

### <a name="get-log-results-upon-completion"></a>Obter resultados de log após a conclusão

O treinamento e o monitoramento do modelo ocorrem em segundo plano para que você possa executar outras tarefas enquanto aguarda. Você também pode esperar até o modelo ter concluído o treinamento antes de executar mais código. Quando você usa **ScriptRunConfig**, pode usar ```run.wait_for_completion(show_output = True)``` para mostrar quando o treinamento do modelo está concluído. O sinalizador ```show_output``` fornece saída detalhada. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Métricas de execução de consulta

Você pode exibir as métricas de um modelo treinado usando ```run.get_metrics()```. Agora você pode obter todas as métricas registradas no exemplo anterior para determinar o melhor modelo.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Exibir o experimento no workspace, no [estúdio do Azure Machine Learning](https://ml.azure.com)

Quando a execução de um experimento estiver concluída, você poderá navegar até o registro de execução do experimento gravado. Você pode acessar o histórico no [estúdio do Azure Machine Learning](https://ml.azure.com).

Navegue até a guia Experimentos e selecione seu experimento. O painel de execução do experimento será exibido. Nele você verá as métricas e os gráficos acompanhados cujas execuções estão registradas em log. Neste caso, registramos em log MSE e os valores alfa.

  ![Detalhes da execução no estúdio do Azure Machine Learning](./media/how-to-track-experiments/experiment-dashboard.png)

Você pode fazer uma busca detalhada em uma execução específica para ver suas saídas ou logs, ou pode baixar o instantâneo do experimento que enviou para poder compartilhar a pasta do experimento com outras pessoas.

### <a name="viewing-charts-in-run-details"></a>Exibindo gráficos nos detalhes de execução

Há várias maneiras de usar as APIs para tipos diferentes de registro de métricas de log durante uma execução e exibi-los como gráficos no estúdio do Azure Machine Learning.

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
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

Experimente estas próximas etapas para aprender a usar o SDK do Azure Machine Learning para Python:

* Veja um exemplo de como registrar o melhor modelo e implantá-lo no tutorial [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

* Saiba como [Treinar modelos PyTorch com o Azure Machine Learning](how-to-train-pytorch.md).
