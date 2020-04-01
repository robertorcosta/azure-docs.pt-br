---
title: Interpretabilidade de modelo no machine learning automatizado
titleSuffix: Azure Machine Learning
description: Saiba como obter explicações de como seu modelo ML automatizado determina a importância dos recursos e faz previsões ao usar o Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 03/11/2020
ms.openlocfilehash: e2465a2df3fab736c8f118911da14ef23c8aec86
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437289"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>Interpretabilidade de modelo no machine learning automatizado

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende como habilitar os recursos de interpretação para aprendizado automatizado de máquina (ML) no Azure Machine Learning. O ML automatizado ajuda você a entender a importância dos recursos projetados. 

Todas as versões sdk após `model_explainability=True` 1.0.85 definidas por padrão. Na versão 1.0.85 do SDK e `model_explainability=True` versões anteriores os usuários precisam definir no `AutoMLConfig` objeto para usar a interpretação do modelo. 

Neste artigo, você aprenderá como:

- Realizar a interpretabilidade durante o treinamento para melhor modelo ou qualquer modelo.
- Habilite visualizações para ajudá-lo a ver padrões em dados e explicações.
- Implementar a interpretabilidade durante a inferência ou pontuação.

## <a name="prerequisites"></a>Pré-requisitos

- Recursos de interpretabilidade. Corra `pip install azureml-interpret azureml-contrib-interpret` para pegar os pacotes necessários.
- Conhecimento de construção de experimentos automatizados de ML. Para obter mais informações sobre como usar o Azure Machine Learning SDK, complete este [tutorial de modelo de regressão](tutorial-auto-train-models.md) ou veja como configurar [experimentos ml automatizados](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Interpretabilidade durante o treinamento para o melhor modelo

Recupere a `best_run`explicação do , que inclui explicações para características projetadas.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Baixe a importância do recurso projetado da loja de artefatos

Você pode `ExplanationClient` usar para baixar as explicações de `best_run`recursos projetados da loja de artefatos do . 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretabilidade durante o treinamento para qualquer modelo 

Quando você calcula as explicações do modelo e as visualiza, você não se limita a uma explicação de modelo existente para um modelo ML automatizado. Você também pode obter uma explicação para o seu modelo com diferentes dados de teste. As etapas desta seção mostram como calcular e visualizar a importância dos recursos projetados com base nos dados do teste.

### <a name="retrieve-any-other-automl-model-from-training"></a>Recupere qualquer outro modelo AutoML do treinamento

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Configure as explicações do modelo

Use `automl_setup_model_explanations` para obter as explicações projetadas. A `fitted_model` lata pode gerar os seguintes itens:

- Dados em destaque de amostras treinadas ou de teste
- Listas de nomes de recursos projetados
- Classes findable em sua coluna rotulada em cenários de classificação

O `automl_explainer_setup_obj` contém todas as estruturas da lista acima.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inicialize o Explicador Mimic para a importância do recurso

Para gerar uma explicação para os `MimicWrapper` modelos AutoML, use a classe. Você pode inicializar o MimicWrapper com estes parâmetros:

- O objeto de configuração explicor
- Seu espaço de trabalho
- Um modelo LightGBM, que atua `fitted_model` como um substituto para o modelo ML automatizado

O MimicWrapper também `automl_run` leva o objeto onde as explicações projetadas serão carregadas.

```python
from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Use mimicExplainer para calcular e visualizar a importância do recurso projetado

Você pode `explain()` chamar o método no MimicWrapper com as amostras de teste transformadas para obter a importância do recurso para os recursos gerados. Você também `ExplanationDashboard` pode usar para visualizar a visualização do painel de controle dos valores de importância dos recursos gerados pelos featurizers ML automatizados.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="interpretability-during-inference"></a>Interpretabilidade durante a inferência

Nesta seção, você aprende a operacionalizar um modelo ML automatizado com o explicador que foi usado para calcular as explicações na seção anterior.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registre o modelo e o explicador de pontuação

Use `TreeScoringExplainer` o para criar o explicador de pontuação que calculará os valores de importância do recurso projetado no momento da inferência. Você inicializa o explicador `feature_map` de pontuação com o que foi computado anteriormente. 

Salve o explicador de pontuação e registre o modelo e o explicador de pontuação com o Model Management Service. Execute o seguinte código:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Crie as dependências de conda para configurar o serviço

Em seguida, crie as dependências de ambiente necessárias no recipiente para o modelo implantado. Observe que os padrões azureml com a versão >= 1.0.45 devem ser listados como uma dependência de pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>Implantar o serviço

Implante o serviço usando o arquivo conda e o arquivo de pontuação das etapas anteriores.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Inferência com dados de teste

Inferência com alguns dados de teste para ver o valor previsto do modelo ML automatizado. Veja a importância do recurso projetado para o valor previsto.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualize para descobrir padrões em dados e explicações na hora do treinamento

Você pode visualizar o gráfico de importância do recurso em seu espaço de trabalho no [estúdio Azure Machine Learning](https://ml.azure.com). Depois que a execução automatizada do ML estiver concluída, selecione **Exibir detalhes do modelo** para exibir uma execução específica. Selecione a guia **Explicações** para ver o painel de visualização de explicações.

[![Arquitetura de interpretabilidade de aprendizado de máquina](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como você pode habilitar explicações de modelo e importância de recursos em áreas do Azure Machine Learning SDK além de aprendizado automático de máquina, consulte o [artigo conceitual sobre interpretação](how-to-machine-learning-interpretability.md).
