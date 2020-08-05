---
title: Personalização em experimentos de AutoML
titleSuffix: Azure Machine Learning
description: Saiba o que as configurações de personalização Azure Machine Learning oferecem e como a engenharia de recursos tem suporte em experimentos de ML automatizados.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 8e3657128ddcff7f9436398ac4bcc6e220b86168
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552468"
---
# <a name="featurization-in-automated-machine-learning"></a>Personalização no Machine Learning automatizado

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste guia, você aprenderá a:

- O que as configurações de personalização Azure Machine Learning oferecem.
- Como personalizar esses recursos para seus [experimentos de aprendizado de máquina automatizados](concept-automated-ml.md).

A *engenharia de recursos* é o processo de usar o conhecimento de domínio dos dados para criar recursos que ajudem os algoritmos de ml (aprendizado de máquina) a aprender melhor. Em Azure Machine Learning, as técnicas de escalação de dados e normalização são aplicadas para facilitar a engenharia de recursos. Coletivamente, essas técnicas e essa engenharia de recursos são chamadas de *personalização* em experimentos de aprendizado automático de máquina ou *AutoML*.

Este artigo pressupõe que você já sabe como configurar um experimento do AutoML. Para obter informações sobre a configuração, consulte os seguintes artigos:

- Para uma experiência de primeiro código: [Configure os experimentos de ml automatizados usando o SDK do Azure Machine Learning para Python](how-to-configure-auto-train.md).
- Para uma experiência de baixo código ou sem código: [crie, revise e implante modelos de aprendizado de máquina automatizados usando o Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Configurar o personalização

Em cada experimento automatizado de aprendizado de máquina, [técnicas de dimensionamento automático e de normalização](#featurization) são aplicadas aos seus dados por padrão. Essas técnicas são tipos de personalização que ajudam *certos* algoritmos que são sensíveis a recursos em escalas diferentes. No entanto, você também pode habilitar personalização adicionais, como *valores ausentes de imputação*, *codificação*e *transformações*.

> [!NOTE]
> As etapas para personalização de Machine Learning automatizadas (como normalização de recursos, manipulação de dados ausentes ou conversão de texto em numeric) se tornam parte do modelo subjacente. Quando você usa o modelo para previsões, as mesmas etapas personalização aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

Para os experimentos que você configura com o SDK do Python, você pode habilitar ou desabilitar a configuração personalização e especificar ainda as etapas de personalização a serem usadas para o experimento. Se você estiver usando o Azure Machine Learning Studio, consulte as [etapas para habilitar o personalização](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

A tabela a seguir mostra as configurações aceitas para `featurization` na [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig):

|Configuração do personalização | Descrição|
------------- | ------------- |
|`"featurization": 'auto'`| Especifica que, como parte do pré-processamento, [as etapas de guardrails e personalização de dados](#featurization) são feitas automaticamente. Essa é a configuração padrão.|
|`"featurization": 'off'`| Especifica que as etapas do personalização não devem ser feitas automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Especifica que as etapas de personalização personalizadas devem ser usadas. [Saiba como personalizar a definição de recursos](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Personalização automática

A tabela a seguir resume as técnicas que são aplicadas automaticamente aos seus dados. Essas técnicas são aplicadas para experimentos que são configurados usando o SDK ou o estúdio. Para desabilitar esse comportamento, defina `"featurization": 'off'` em seu `AutoMLConfig` objeto.

> [!NOTE]
> Se você planeja exportar seus modelos AutoML para um [modelo ONNX](concept-onnx.md), somente as opções de personalização indicadas com um asterisco ("*") têm suporte no formato ONNX. Saiba mais sobre [conversão de modelos para ONNX](concept-automated-ml.md#use-with-onnx).

|Etapas de personalização &nbsp;| Descrição |
| ------------- | ------------- |
|**Descartar alta cardinalidade ou nenhum recurso de variação*** |Descartar esses recursos de conjuntos de treinamento e validação. Aplica-se a recursos com todos os valores ausentes, com o mesmo valor em todas as linhas ou com alta cardinalidade (por exemplo, hashes, IDs ou GUIDs).|
|**Imputar valores ausentes*** |Para recursos numéricos, imputar com a média de valores na coluna.<br/><br/>Para recursos categóricos, imputar com o valor mais frequente.|
|**Gerar recursos adicionais*** |Para recursos DateTime: Ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.<br><br> *Para tarefas de previsão,* esses recursos de DateTime adicionais são criados: ano ISO, semestre, mês como cadeia de caracteres, semana, dia da semana como cadeia de caracteres, dia do trimestre, dia do ano, AM/PM (0 se a hora for anterior ao meio-dia (12 PM), 1 caso), AM/PM como cadeia de caracteres, hora do dia (12HR)<br/><br/>Para recursos de texto: a frequência de termos com base em unigrams, bigrams e trigrams. Saiba mais sobre [como isso é feito com o Bert.](#bert-integration)|
|**Transformar e codificar***|Transforme recursos numéricos que têm poucos valores exclusivos em recursos categóricos.<br/><br/>A codificação One-Hot é usada para recursos categóricos de baixa cardinalidade. A codificação um-Hot-hash é usada para recursos categóricos de alta cardinalidade.|
|**Inserções de palavras**|Um texto featurizer converte vetores de tokens de texto em vetores de sentença usando um modelo pretreinado. O vetor de incorporação de cada palavra em um documento é agregado com o restante para produzir um vetor de recursos de documento.|
|**Codificações de destino**|Para recursos categóricos, essa etapa mapeia cada categoria com um valor de destino médio para problemas de regressão e para a probabilidade de classe de cada classe para problemas de classificação. O peso baseado em frequência e a validação cruzada de k-fold são aplicados para reduzir o superajuste do mapeamento e ruído causados por categorias de dados esparsas.|
|**Codificação de destino de texto**|Para uma entrada de texto, um modelo linear empilhado com um conjunto de palavras é usado para gerar a probabilidade de cada classe.|
|**Peso de evidência (WoE)**|Calcula o WoE como uma medida de correlação de colunas categóricas para a coluna de destino. WoE é calculado como o log da proporção de probabilidades de classe vs. fora de classe. Esta etapa produz uma coluna de recurso numérico por classe e remove a necessidade de imputar explicitamente os valores ausentes e o tratamento de exceção.|
|**Distância do cluster**|Treina um modelo de clustering k-means em todas as colunas numéricas. Produz novos recursos do *k* (um novo recurso numérico por cluster) que contém a distância de cada amostra para o centróide de cada cluster.|

## <a name="data-guardrails"></a>Verificadores de integridade dos dados

*Os data guardrails* ajudam a identificar possíveis problemas com seus dados (por exemplo, valores ausentes ou [desequilíbrio de classe](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)). Eles também ajudam você a tomar medidas corretivas para resultados aprimorados.

Os Guardrails de dados são aplicados:

- **Para experimentos do SDK**: quando os parâmetros `"featurization": 'auto'` ou `validation=auto` são especificados em seu `AutoMLConfig` objeto.
- **Para experimentos de estúdio**: quando a personalização automática está habilitada.

Você pode revisar os dados guardrails para seu experimento:

- Definindo `show_output=True` quando você envia um experimento usando o SDK.

- No estúdio, na guia **Data guardrails** da execução de ml automatizada.

### <a name="data-guardrail-states"></a>Estados de guardrail de dados

Os Guardrails de dados exibem um dos três Estados:

|Estado| Descrição |
|----|---- |
|**Aprovado**| Nenhum problema de dados foi detectado e nenhuma ação é exigida por você. |
|**Concluído**| As alterações foram aplicadas aos seus dados. Incentivamos você a examinar as ações corretivas que o AutoML levou, para garantir que as alterações se alinhem com os resultados esperados. |
|**Alertado**| Foi detectado um problema de dados, mas não foi possível remediar. Incentivamos você a revisar e corrigir o problema.|

### <a name="supported-data-guardrails"></a>Guardrails de dados com suporte

A tabela a seguir descreve os dados guardrails que têm suporte no momento e os status associados que você pode ver ao enviar seu experimento:

Verificador de integridade|Status|Condição&nbsp;para&nbsp;gatilho
---|---|---
**Imputação de valores de recurso ausente** |Aprovado <br><br><br> Concluído| Não foram detectados valores de recursos ausentes em seus dados de treinamento. Saiba mais sobre [imputação de valor ausente.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Valores de recursos ausentes foram detectados em seus dados de treinamento e foram imputadosdos.
**Tratamento de recursos de alta cardinalidade** |Aprovado <br><br><br> Concluído| Suas entradas foram analisadas e nenhum recurso de alta cardinalidade foi detectado. <br><br> Os recursos de alta cardinalidade foram detectados nas suas entradas e foram manipulados.
**Tratamento de divisão de validação** |Concluído| A configuração de validação foi definida como `'auto'` e os dados de treinamento continham *menos de 20.000 linhas*. <br> Cada iteração do modelo treinado foi validada usando a validação cruzada. Saiba mais sobre [os dados de validação](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data). <br><br> A configuração de validação foi definida como `'auto'` e os dados de treinamento continham *mais de 20.000 linhas*. <br> Os dados de entrada foram divididos em um conjunto de dados de treinamento e um conjunto de dados de validação para a validação do modelo.
**Detecção de equilíbrio de classe** |Aprovado <br><br><br><br><br> Alertado <br><br><br><br> Concluído| Suas entradas foram analisadas, e todas as classes estão equilibradas nos dados de treinamento. Um conjunto de um DataSet é considerado como balanceado se cada classe tem uma boa representação no DataSet, conforme medido por número e proporção de amostras. <br><br> Foram detectadas classes desequilibradas nas suas entradas. Para corrigir a tendência do modelo, corrija o problema de balanceamento. Saiba mais sobre [dados desequilibrados](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data). <br><br> As classes desbalanceadas foram detectadas nas suas entradas e a lógica de varredura determinou para aplicar o balanceamento.
**Detecção de problemas de memória** |Aprovado <br><br><br><br> Concluído |<br> Os valores selecionados (Horizonte, retardo, janela sem interrupção) foram analisados e nenhum problema potencial de memória insuficiente foi detectado. Saiba mais sobre [as configurações de previsão](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)de série temporal. <br><br><br>Os valores selecionados (Horizonte, retardo, janela sem interrupção) foram analisados e, potencialmente, farão com que o teste fique sem memória. As configurações de atraso ou janela de rolagem foram desativadas.
**Detecção de frequência** |Aprovado <br><br><br><br> Concluído |<br> A série temporal foi analisada e todos os pontos de dados estão alinhados com a frequência detectada. <br> <br> A série temporal foi analisada e os pontos de dados que não se alinham com a frequência detectada foram detectados. Esses pontos de dados foram removidos do conjunto de dados. Saiba mais sobre [a preparação de dados para a previsão de série temporal](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="customize-featurization"></a>Personalizar o personalização

Você pode personalizar suas configurações de personalização para garantir que os dados e recursos usados para treinar seu modelo de ML resultem em previsões relevantes.

Para personalizar o featurizations, especifique  `"featurization": FeaturizationConfig` em seu `AutoMLConfig` objeto. Se você estiver usando o Azure Machine Learning Studio para seu experimento, consulte o [artigo de instruções](how-to-use-automated-ml-for-ml-models.md#customize-featurization). Para personalizar o personalização para os tipos de tarefa previsão, consulte a [previsão de instruções](how-to-auto-train-forecast.md#customize-featurization).

As personalizações com suporte incluem:

|Personalização|Definição|
|--|--|
|**Atualização de finalidade de coluna**|Substituir o tipo de recurso detectado automaticamente para a coluna especificada.|
|**Atualização de parâmetro do transformador** |Atualize os parâmetros para o transformador especificado. Atualmente dá suporte a *imputer* (média, mais frequente e mediana) e *HashOneHotEncoder*.|
|**Remover colunas** |Especifica que as colunas a serem descartadas são destacados.|
|**Bloquear transformadores**| Especifica os transformadores de bloqueio a serem usados no processo personalização.|

Crie o `FeaturizationConfig` objeto usando chamadas de API:

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="bert-integration"></a>Integração do BERT 
[Bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) é usado na camada personalização do ml automatizado. Nessa camada, detectamos se uma coluna contém texto livre ou outros tipos de dados, como carimbos de data/hora ou números simples, e nós Personalizars de acordo. Para BERT, ajustamos/treinamos o modelo utilizando os rótulos fornecidos pelo usuário e, em seguida, geramos incorporações de documentos (por BERT, são o estado final oculto associado ao token [CLS] especial) como recursos junto com outros recursos, como recursos baseados em carimbo de data/hora (por exemplo, dia da semana) ou números que muitos conjuntos de resultados típicos têm. 

Para habilitar o BERT, você deve usar a computação de GPU para treinamento. Se uma computação de CPU for usada, em vez de BERT, AutoML habilitará BiLSTM DNN featurizer. Para invocar BERT, você precisa definir "enable_dnn: true" em automl_settings e usar a computação de GPU (por exemplo, vm_size = "STANDARD_NC6" ou uma GPU superior). Consulte [este bloco de anotações para obter um exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb).

O AutoML executa as seguintes etapas, para o caso de BERT (Observe que você precisa definir "enable_dnn: true" em automl_settings para que esses itens aconteçam):

1. Pré-processando, incluindo a geração de tokens de todas as colunas de texto (você verá o transformador "StringCast" no Resumo de personalização do modelo final. Visite [este bloco de anotações](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb) para ver um exemplo de como produzir o resumo de personalização do modelo usando o `get_featurization_summary()` método.

```python
text_transformations_used = []
for column_group in fitted_model.named_steps['datatransformer'].get_featurization_summary():
    text_transformations_used.extend(column_group['Transformations'])
text_transformations_used
```

2. Concatena todas as colunas de texto em uma única coluna de texto, portanto, você verá "StringConcatTransformer" no modelo final. 

> [!NOTE]
> Nossa implementação de BERT limita o tamanho de texto total de um exemplo de treinamento a tokens de 128. Isso significa que, de forma ideal, todas as colunas de texto quando concatenadas devem ter, no máximo, 128 tokens de comprimento. Idealmente, se várias colunas estiverem presentes, cada coluna deverá ser removida de modo que essa condição seja satisfeita. Por exemplo, se houver duas colunas de texto nos dados, ambas as colunas de texto deverão ser removidas para 64 tokens cada (supondo que ambas as colunas sejam representadas igualmente na coluna de texto concatenada final) antes de alimentar os dados para AutoML. Para colunas concatenadas de comprimento >tokens 128, a camada criador do BERT truncará essa entrada para 128 tokens.

3. Na etapa de limpeza do recurso, o AutoML compara o BERT com a linha de base (recursos de conjunto de palavras) em uma amostra dos dados e determina se o BERT forneceria melhorias de precisão. Se ele determinar que o BERT tem um desempenho melhor do que a linha de base, o AutoML usará BERT para Text personalização como a estratégia de personalização ideal e continuará com destacar os dados inteiros. Nesse caso, você verá o "PretrainedTextDNNTransformer" no modelo final.

BERT geralmente é executado por mais tempo do que a maioria das outras featurizers. Ele pode ser o acelerador, fornecendo mais computação em seu cluster. O AutoML distribuirá o treinamento do BERT em vários nós se eles estiverem disponíveis (até um máximo de 8 nós). Isso pode ser feito definindo [max_concurrent_iterations](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) como maior que 1. Para obter um melhor desempenho, é recomendável usar SKUs com recursos RDMA (como "STANDARD_NC24r" ou "STANDARD_NC24rs_V3")

O AutoML atualmente dá suporte a idiomas 100 e, dependendo do idioma do conjunto de um, o AutoML escolhe o modelo de BERT apropriado. Para dados em alemão, usamos o modelo alemão BERT. Para o inglês, usamos o modelo BERT em inglês. Para todas as outras linguagens, usamos o modelo BERT multilíngue.

No código a seguir, o modelo alemão BERT é disparado, uma vez que a linguagem do conjunto de dado é especificada como ' deu ', o código de idioma de três letras para o alemão de acordo com a [classificação ISO](https://iso639-3.sil.org/code/deu):

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>Próximas etapas

* Saiba como configurar seus experimentos de ML automatizados:

    * Para uma experiência de primeiro código: [Configure os experimentos de ml automatizados usando o SDK do Azure Machine Learning](how-to-configure-auto-train.md).
    * Para uma experiência de baixo código ou sem código: [crie seus experimentos de ml automatizados no Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

* Saiba mais sobre [como e onde implantar um modelo](how-to-deploy-and-where.md).

* Saiba mais sobre [como treinar um modelo de regressão usando o aprendizado de máquina automatizado](tutorial-auto-train-models.md) ou [como treinar usando o aprendizado de máquina automatizado em um recurso remoto](how-to-auto-train-remote.md).
