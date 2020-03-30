---
title: Interpretável de modelos para corridas locais e remotas
titleSuffix: Azure Machine Learning
description: Saiba como obter explicações de como seu modelo de aprendizado de máquina determina a importância dos recursos e faz previsões ao usar o Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a479982eeac325c9774e3858ec51643e8ba699c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064051"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Interpretável de modelos para corridas locais e remotas

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende a usar o pacote de interpretação do Azure Machine Learning Python SDK para entender por que seu modelo fez suas previsões. Você aprenderá como:

* Interprete modelos de aprendizagem de máquina treinados localmente e em recursos de computação remota.
* Armazene explicações locais e globais sobre a História da Corrida do Azure.
* Veja visualizações de interpretabilidade no [estúdio Azure Machine Learning](https://ml.azure.com).
* Implante um explicador de pontuação com o seu modelo.

Para obter mais informações, consulte [a interpretável do modelo no Azure Machine Learning](how-to-machine-learning-interpretability.md).

## <a name="local-interpretability"></a>Interpretabilidade local

O exemplo a seguir mostra como usar o pacote de interpretabilidade localmente sem entrar em contato com os serviços do Azure.

1. Se necessário, `pip install azureml-interpret` use para obter o pacote de interpretação.

1. Treine um modelo de amostra em um caderno jupyter local.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

1. Ligue para o explicador localmente.
   * Para inicializar um objeto explicor, passe seu modelo e alguns dados de treinamento para o construtor do explicador.
   * Para tornar suas explicações e visualizações mais informativas, você pode optar por passar em nomes de recursos e nomes de classe de saída se estiver fazendo a classificação.

   Os seguintes blocos de código mostram `TabularExplainer`como `MimicExplainer`instanciar um objeto explicor com , e `PFIExplainer` localmente.
   * `TabularExplainer`chama um dos três explicadores`TreeExplainer`SHAP por baixo ( , `DeepExplainer`ou `KernelExplainer`).
   * `TabularExplainer`seleciona automaticamente o mais apropriado para o seu caso de uso, mas você pode chamar cada um de seus três explicadores subjacentes diretamente.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    ou

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    ou

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>No geral, valores globais de importância de características

Consulte o exemplo a seguir para ajudá-lo a obter os valores globais de importância do recurso.

```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>Valores de importância de recursos locais em nível de instância

Obtenha os valores locais de importância do recurso chamando explicações para uma instância individual ou um grupo de instâncias.
> [!NOTE]
> `PFIExplainer`não suporta explicações locais.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Interpretabilidade para corridas remotas

O exemplo a seguir mostra `ExplanationClient` como você pode usar a classe para permitir a interpretabilidade do modelo para corridas remotas. É conceitualmente semelhante ao processo local, exceto você:

* Use `ExplanationClient` o na execução remota para carregar o contexto de interpretação.
* Baixe o contexto mais tarde em um ambiente local.

1. Se necessário, `pip install azureml-contrib-interpret` use para obter o pacote necessário.

1. Crie um script de treinamento em um Jupyter Notebook local. Por exemplo, `train_explain.py`.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Configure um Azure Machine Learning Compute como seu alvo de computação e envie sua corrida de treinamento. Consulte a configuração de [metas de computação para treinamento de modelos](how-to-set-up-training-targets.md#amlcompute) para instruções. Você também pode achar útil o exemplo dos [notebooks.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)

1. Baixe a explicação em seu caderno Jupyter local.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>Transformações de recursos brutos

Você pode optar por obter explicações em termos de recursos crus e não transformados em vez de recursos projetados. Para esta opção, você passa seu pipeline `train_explain.py`de transformação de recursos para o explicador em . Caso contrário, o explicador fornece explicações em termos de recursos projetados.

O formato das transformações suportadas é o mesmo descrito em [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Em geral, todas as transformações são suportadas desde que operem em uma única coluna, de modo que esteja claro que são de um para muitos.

Obtenha uma explicação para características `sklearn.compose.ColumnTransformer` brutas usando um ou com uma lista de tuplas de transformadores instalados. O exemplo `sklearn.compose.ColumnTransformer`a seguir usa .

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

No caso de você querer executar o exemplo com a lista de tuplas do transformador instalado, use o seguinte código:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>Visualizações

Depois de baixar as explicações em seu notebook Jupyter local, você pode usar o painel de visualização para entender e interpretar o seu modelo.

### <a name="global-visualizations"></a>Visualizações globais

As tramas a seguir fornecem uma visão global do modelo treinado, juntamente com suas previsões e explicações.

|Enredo|Descrição|
|----|-----------|
|Exploração de dados| Exibe uma visão geral do conjunto de dados junto com os valores de previsão.|
|Importância Global|Mostra recursos importantes de K (Configurável K) em todo o mundo. Ajuda a entender o comportamento global do modelo subjacente.|
|Exploração de Explicação|Demonstra como um recurso afeta uma mudança nos valores de previsão do modelo, ou a probabilidade de valores de predição. Mostra o impacto da interação com os recursos.|
|Importância do Resumo|Usa valores locais de importância de recursos em todos os pontos de dados para mostrar a distribuição do impacto de cada recurso no valor da previsão.|

[![Painel de visualização Global](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Visualizações locais

Você pode carregar o gráfico local de importância de recurso para qualquer ponto de dados, selecionando o ponto de dados individual na trama.

|Enredo|Descrição|
|----|-----------|
|Importância Local|Mostra os principais recursos k (configuráveis K) globalmente. Ajuda a ilustrar o comportamento local do modelo subjacente em um ponto de dados específico.|
|Exploração de Perturbação|Permite alterações para apresentar valores do ponto de dados selecionado e observar alterações resultantes no valor da previsão.|
|Expectativa Condicional Individual (ICE)| Permite que o valor do recurso mude de um valor mínimo para um valor máximo. Ajuda a ilustrar como a previsão do ponto de dados muda quando um recurso muda.|

[![Importância do recurso local do painel de visualização](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![Perturbação do recurso do painel de visualização](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![Gráficos ICE do painel de visualização](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> Antes de iniciar o kernel Jupyter, certifique-se de ativar extensões de widget para o painel de visualização.

* Notebooks Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Para carregar o painel de visualização, use o seguinte código.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualização no estúdio Azure Machine Learning

Se você completar as etapas [de interpretação remota,](#interpretability-for-remote-runs) poderá visualizar o painel de visualização no [estúdio Azure Machine Learning](https://ml.azure.com). Este painel é uma versão mais simples do painel de visualização explicado acima. Ele só suporta duas guias:

|Enredo|Descrição|
|----|-----------|
|Importância Global|Mostra recursos importantes de K (Configurável K) em todo o mundo. Ajuda a entender o comportamento global do modelo subjacente.|
|Importância do Resumo|Usa valores locais de importância de recursos em todos os pontos de dados para mostrar a distribuição do impacto de cada recurso no valor da previsão.|

Se as explicações globais e locais estiverem disponíveis, os dados preencherão ambas as guias. Se apenas uma explicação global estiver disponível, a guia Importância sumária será desativada.

Siga um desses caminhos para acessar o painel de visualização no estúdio Azure Machine Learning:

* **Painel de experimentos** (Visualização)
  1. Selecione **Experimentos** no painel esquerdo para ver uma lista de experimentos que você executou no Azure Machine Learning.
  1. Selecione um experimento específico para ver todas as corridas desse experimento.
  1. Selecione uma execução e, em seguida, a guia **Explicações** para o painel de visualização de explicações.

   [![Importância do recurso local do painel de visualização](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **Painel de modelos**
  1. Se você registrou seu modelo original seguindo as etapas em [Implantar modelos com o Azure Machine Learning,](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)você pode selecionar **Modelos** no painel esquerdo para visualizá-lo.
  1. Selecione um modelo e, em seguida, a guia **Explicações** para visualizar o painel de visualização de explicações.

## <a name="interpretability-at-inference-time"></a>Interpretabilidade no tempo de inferência

Você pode implantar o explicador junto com o modelo original e usá-lo na hora da inferência para fornecer as informações de explicação local. Também oferecemos explicadores de pontuação de peso mais leve para melhorar o desempenho da interpretabilidade no momento da inferência. O processo de implantação de um explicador de pontuação de peso mais leve é semelhante à implantação de um modelo e inclui as seguintes etapas:

1. Crie um objeto de explicação. Por exemplo, você `TabularExplainer`pode usar:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Crie um explicador de pontuação com o objeto de explicação.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Configure e registre uma imagem que use o modelo de explicador de pontuação.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Como um passo opcional, você pode recuperar o explicador de pontuação da nuvem e testar as explicações.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Implante a imagem em um alvo de computação, seguindo estas etapas:

   1. Se necessário, registre seu modelo de previsão original seguindo as etapas em [Implantar modelos com O Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

   1. Crie um arquivo de pontuação.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. Defina a configuração de implantação.

         Essa configuração depende dos requisitos do seu modelo. O exemplo a seguir define uma configuração que usa um núcleo de CPU e um GB de memória.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Crie um arquivo com dependências de ambiente.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Crie um arquivo docker personalizado com g++ instalado.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Implante a imagem criada.
   
         Esse processo leva aproximadamente cinco minutos.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. Teste a implantação.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Limpar.

   Para excluir um serviço Web implantado, use `service.delete()`.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre a interpretância do modelo](how-to-machine-learning-interpretability.md)