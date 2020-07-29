---
title: Avaliar a imparcialidade dos modelos de ML no Python (visualização)
titleSuffix: Azure Machine Learning
description: Saiba como avaliar a imparcialidade de seus modelos no Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: 5e6f241fcf30d4090a1af9a26441a5a10b939972
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87307056"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Usar Azure Machine Learning com o pacote Fairlearn Open-Source para avaliar a imparcialidade dos modelos de ML (versão prévia)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste guia de instruções, você aprenderá a usar o pacote Python de software livre [Fairlearn](https://fairlearn.github.io/) com Azure Machine Learning para executar as seguintes tarefas:

* Avalie a imparcialidade de suas previsões de modelo. Para saber mais sobre a imparcialidade no aprendizado de máquina, confira a [imparcialidade no artigo Machine Learning](concept-fairness-ml.md).
* Carregue, liste e baixe informações de avaliação de imparcialidade de/para o Azure Machine Learning Studio.
* Consulte um painel de avaliação de imparcialidade no Azure Machine Learning Studio para interagir com os insights de imparcial de seu (s) modelo.

>[!NOTE]
> A avaliação de imparcialidade não é um exercício puramente técnico. **Esse pacote pode ajudá-lo a avaliar a imparcialidade de um modelo de aprendizado de máquina, mas apenas você pode configurar e tomar decisões quanto à forma como o modelo é executado.**  Embora esse pacote ajude a identificar métricas quantitativas para avaliar a imparcialidade, os desenvolvedores de modelos de aprendizado de máquina também devem executar uma análise qualitativa para avaliar a imparcialidade de seus próprios modelos.

## <a name="azure-machine-learning-fairness-sdk"></a>SDK de imparcialidade Azure Machine Learning 

O SDK de imparcialidade Azure Machine Learning, `azureml-contrib-fairness` , integra o pacote Python de software livre, [Fairlearn](http://fairlearn.github.io), em Azure Machine Learning. Para saber mais sobre a integração do Fairlearn no Azure Machine Learning, confira estes [blocos de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). Para obter mais informações sobre o Fairlearn, consulte o [Guia de exemplo](https://fairlearn.github.io/auto_examples/notebooks/index.html) e os [notebooks de exemplo](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Use os seguintes comandos para instalar os `azureml-contrib-fairness` `fairlearn` pacotes e:
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```



## <a name="upload-fairness-insights-for-a-single-model"></a>Carregar informações de imparcialidade para um único modelo

O exemplo a seguir mostra como usar o pacote de imparcialidade para carregar informações de imparcialidade de modelo no Azure Machine Learning e ver o painel de avaliação de imparcialidade no Azure Machine Learning Studio.

1. Treine um modelo de exemplo em um notebook Jupyter. 

    Para o conjunto de um, usamos o conjunto de censo de conteúdo adulto conhecido, que carregamos usando `shap` (por conveniência). Para os fins deste exemplo, tratamos esse conjunto de testes como um problema de decisão de empréstimo e fingir que o rótulo indica se cada indivíduo pagou ou não um empréstimo no passado. Usaremos os dados para treinar um pregnóstico para prever se os indivíduos não vistos anteriormente indenizarão um empréstimo ou não. A suposição é que as previsões de modelo são usadas para decidir se uma pessoa deve receber um empréstimo.

    ```python
    from sklearn.model_selection import train_test_split
    from fairlearn.widget import FairlearnDashboard
    from sklearn.linear_model import LogisticRegression
    from sklearn.preprocessing import LabelEncoder, StandardScaler
    import pandas as pd
    import shap

    # Load the census dataset
    X_raw, Y = shap.datasets.adult()
    X_raw["Race"].value_counts().to_dict()
    

    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    A = X_raw[['Sex','Race']]
    X = X_raw.drop(labels=['Sex', 'Race'],axis = 1)
    X = pd.get_dummies(X)
    
    sc = StandardScaler()
    X_scaled = sc.fit_transform(X)
    X_scaled = pd.DataFrame(X_scaled, columns=X.columns)

    # Perform some standard data preprocessing steps to convert the data into a format suitable for the ML algorithms
    le = LabelEncoder()
    Y = le.fit_transform(Y)

    # Split data into train and test
    from sklearn.model_selection import train_test_split
    from sklearn.model_selection import train_test_split
    X_train, X_test, Y_train, Y_test, A_train, A_test = train_test_split(X_scaled, 
                                                        Y, 
                                                        A,
                                                        test_size = 0.2,
                                                        random_state=0,
                                                        stratify=Y)

    # Work around indexing issue
    X_train = X_train.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Improve labels
    A_test.Sex.loc[(A_test['Sex'] == 0)] = 'female'
    A_test.Sex.loc[(A_test['Sex'] == 1)] = 'male'


    A_test.Race.loc[(A_test['Race'] == 0)] = 'Amer-Indian-Eskimo'
    A_test.Race.loc[(A_test['Race'] == 1)] = 'Asian-Pac-Islander'
    A_test.Race.loc[(A_test['Race'] == 2)] = 'Black'
    A_test.Race.loc[(A_test['Race'] == 3)] = 'Other'
    A_test.Race.loc[(A_test['Race'] == 4)] = 'White'


    # Train a classification model
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Sex', 'Race'],
                       y_true=Y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Faça logon em Azure Machine Learning e Registre seu modelo.
   
    O painel de imparcialidade pode ser integrado a modelos registrados ou não registrados. Registre seu modelo no Azure Machine Learning com as seguintes etapas:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_linear_regression", unmitigated_predictor)
    ```

3. Métricas de imparcialidade pré-calcular.

    Crie um dicionário de painel usando o `metrics` pacote do Fairlearn. O `_create_group_metric_set` método tem argumentos semelhantes ao construtor do painel, exceto que os recursos confidenciais são passados como um dicionário (para garantir que os nomes estejam disponíveis). Também devemos especificar o tipo de previsão (classificação binária, neste caso) ao chamar esse método.

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex}
    ys_pred = unmitigated_predictor.predict(X_test)
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Carregue as métricas de integridade em computação.
    
    Agora, importe `azureml.contrib.fairness` o pacote para executar o upload:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Crie um experimento, execute uma execução e carregue o painel:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Verifique o painel de imparcialidade do Azure Machine Learning Studio

    Se você concluir as etapas anteriores (carregando informações de imparcialidade geradas para Azure Machine Learning), poderá exibir o painel de imparcialidade no [Azure Machine Learning Studio](https://ml.azure.com). Esse painel é o mesmo painel de visualização fornecido no Fairlearn, permitindo que você analise as diferenças entre os subgrupos de seus recursos confidenciais (por exemplo, masculino vs. fêmea).
    Siga um destes caminhos para acessar o painel de visualização no Azure Machine Learning Studio:

    * **Painel de experimentos (versão prévia)**
    1. Selecione **experimentos** no painel esquerdo para ver uma lista de experimentos que você executou em Azure Machine Learning.
    1. Selecione um experimento específico para exibir todas as execuções nesse experimento.
    1. Selecione uma execução e, em seguida, a guia **imparcialidade** para o painel de visualização da explicação.


    [![Painel de imparcialidade](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **Painel de modelos**
    1. Se você registrou seu modelo original seguindo as etapas anteriores, você pode selecionar **modelos** no painel esquerdo para exibi-lo.
    1. Selecione um modelo e, em seguida, a guia **imparcialidade** para exibir o painel de visualização de explicação.

    Para saber mais sobre o painel de visualização e o que ele contém, consulte o guia do [usuário](https://fairlearn.github.io/user_guide/assessment.html#fairlearn-dashboard)do Fairlearn.

s # # carregar informações de inintegridade para vários modelos

Se você estiver interessado em comparar vários modelos e ver como suas avaliações de imparcialidade diferem, você pode passar mais de um modelo para o painel de visualização e navegar por suas compensações de integridade de desempenho.

1. Treine seus modelos:
    
    Além do modelo de regressão logística anterior, agora criamos um segundo classificador, com base em um avaliador de máquina de vetor de suporte e carregamos um dicionário de painel de imparcialidade usando o pacote do Fairlearn `metrics` . Observe que, aqui, ignoramos as etapas para carregar e pré-processar dados e ir direto para o estágio de treinamento do modelo.


    ```python
    # Train your first classification model
    from sklearn.linear_model import LogisticRegression
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # Train your second classification model
    from sklearn import svm
    svm_predictor = svm.SVC()
    svm_predictor.fit(X_train, Y_train)
    ```

2. Registrar seus modelos

    Em seguida, registre os dois modelos dentro de Azure Machine Learning. Para sua conveniência em chamadas de método subsequentes, armazene os resultados em um dicionário, que mapeia o `id` do modelo registrado (uma cadeia de caracteres no `name:version` formato) para o próprio pregnóstico:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_linear_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Carregar o painel do Fairlearn localmente

    Antes de carregar as informações de imparcialidade no Azure Machine Learning, você pode examinar essas previsões em um painel Fairlearn invocado localmente. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Sex', 'Race'],
                    y_true=Y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Métricas de imparcialidade pré-calcular.

    Crie um dicionário de painel usando o `metrics` pacote do Fairlearn.

    ```python
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Carregue as métricas de integridade em computação.
    
    Agora, importe `azureml.contrib.fairness` o pacote para executar o upload:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Crie um experimento, execute uma execução e carregue o painel:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Semelhante à seção anterior, você pode seguir um dos caminhos descritos acima (por meio de **experimentos** ou **modelos**) no Azure Machine Learning Studio para acessar o painel de visualização e comparar os dois modelos em termos de imparcialidade e desempenho.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Carregar insights de inintegridades não mitigados e mitigados

Você pode usar [algoritmos de mitigação](https://fairlearn.github.io/user_guide/mitigation.html)do Fairlearn, comparar seus modelos mitigados gerados com o modelo não mitigado original e navegar pelas compensações de desempenho/imparcialidade entre modelos comparados.

Para ver um exemplo que demonstra o uso do algoritmo de mitigação da [pesquisa de grade](https://fairlearn.github.io/user_guide/mitigation.html#grid-search) (que cria uma coleção de modelos mitigados com diferentes interações de desempenho e imparcialidade), confira este [bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Carregar informações de imparcialção de vários modelos em uma única execução permitiria a comparação de modelos em relação à imparcialidade e ao desempenho. Você pode clicar em qualquer um dos modelos exibidos no gráfico de comparação de modelo para ver as informações de imparcialidade detalhadas do modelo específico.


[![Painel Fairlearn de comparação de modelo](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre a imparcialidade do modelo](concept-fairness-ml.md)

[Confira Azure Machine Learning notebooks de exemplo de imparcialidade](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
