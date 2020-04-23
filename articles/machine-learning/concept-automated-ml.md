---
title: O que é automatizado ML / AutoML
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning pode escolher automaticamente um algoritmo para você e gerar um modelo a partir dele para economizar tempo usando os parâmetros e critérios que você fornece para selecionar o melhor algoritmo para o seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: f592a7f5a4af38988bcf433f0adc89d9be7579cb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082002"
---
# <a name="what-is-automated-machine-learning-automl"></a>O que é aprendizado automático de máquina (AutoML)?

O aprendizado automatizado de máquina, também conhecido como ML automatizado ou AutoML, é o processo de automatização das tarefas demoradas e iterativas do desenvolvimento de modelos de aprendizagem de máquina. Ele permite que cientistas de dados, analistas e desenvolvedores construam modelos ML com alta escala, eficiência e produtividade, ao mesmo tempo em que sustentam a qualidade do modelo. O ML automatizado é baseado em um avanço da nossa [divisão de Pesquisa Microsoft.](https://arxiv.org/abs/1705.05355)

O desenvolvimento tradicional de modelos de aprendizado de máquina é intensivo em recursos, exigindo conhecimento de domínio significativo e tempo para produzir e comparar dezenas de modelos. Com o machine learning automatizado, você vai acelerar o tempo que se leva para colocar modelos de ML prontos em produção com grande facilidade e eficiência.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Quando usar o AutoML: classificar, regressão, & previsão

Aplique ML automatizado quando quiser que o Azure Machine Learning treine e ajuste um modelo para você usando a métrica de destino especificada. O ML automatizado democratiza o processo de desenvolvimento do modelo de machine learning e capacita seus usuários, não importando a qualificação em ciência de dados, a identificar um pipeline de aprendizado de máquina de ponta a ponta para qualquer problema.

Cientistas de dados, analistas e desenvolvedores de todas as indústrias podem usar ml automatizados para:
+ Implementar soluções ML sem amplo conhecimento de programação
+ Economizar tempo e recursos
+ Aproveitar melhores práticas da ciência de dados
+ Fornecer solução de problemas ágil

### <a name="classification"></a>classificação

A classificação é uma tarefa comum de aprendizado de máquina. Classificação é um tipo de aprendizado supervisionado no qual os modelos aprendem usando dados de treinamento e aplicam esses aprendizados a novos dados. O Azure Machine Learning oferece featurizações especificamente para essas tarefas, como featurizers de texto de rede neural profunda para classificação. Saiba mais sobre [as opções de featurização.](how-to-use-automated-ml-for-ml-models.md#featurization) 

O principal objetivo dos modelos de classificação é prever em quais categorias os novos dados se enquadrarão com base nos aprendizados de seus dados de treinamento. Exemplos de classificação comuns incluem detecção de fraude, reconhecimento de caligrafia e detecção de objetos.  Saiba mais e veja um exemplo de [classificação com aprendizado automático](tutorial-train-models-with-aml.md)de máquina.

Veja exemplos de classificação e aprendizado automatizado de máquina nestes notebooks Python: [Detecção de fraudes,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) [Predição de Marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)e [Classificação de Dados de Grupos de Notícias](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)

### <a name="regression"></a>Regressão
Semelhante à classificação, as tarefas de regressão também são uma tarefa de aprendizagem supervisionada comum. O Azure Machine Learning oferece [featurizações especificamente para essas tarefas.](how-to-use-automated-ml-for-ml-models.md#featurization)

Diferente da classificação onde os valores de saída previstos são categóricos, os modelos de regressão prevêem valores de saída numérica baseados em preditores independentes. Na regressão, o objetivo é ajudar a estabelecer a relação entre essas variáveis preditoras independentes, estimando como uma variável impacta as outras. Por exemplo, o preço do automóvel com base em características como, quilometragem de gás, classificação de segurança, etc. Saiba mais e veja um exemplo de [regressão com aprendizado automático](tutorial-auto-train-models.md)de máquina.

Veja exemplos de regressão e aprendizado de máquina automatizado para previsões nestes notebooks Python: [CPU Performance Forecast](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Previsão da série temporal

As previsões de construção são parte integrante de qualquer negócio, seja receita, estoque, vendas ou demanda do cliente. Você pode usar ML automatizado para combinar técnicas e abordagens e obter uma previsão recomendada de séries tempológicas de alta qualidade. Saiba mais com isso: aprendizado automático de [máquina para previsão de séries tempois.](how-to-auto-train-forecast.md) 

Um experimento automatizado de séries tempois é tratado como um problema de regressão multivariada. Os valores passados da série temporal são "pivotados" para se tornarem dimensões adicionais para o regressor, juntamente com outros preditores. Essa abordagem, ao contrário dos métodos clássicos das séries tempois, tem a vantagem de incorporar naturalmente múltiplas variáveis contextuais e sua relação entre si durante o treinamento. O ML automatizado aprende um único modelo, mas muitas vezes ramificado internamente para todos os itens no conjunto de dados e horizontes de previsão. Assim, mais dados estão disponíveis para estimar parâmetros do modelo e a generalização para séries invisíveis.

A configuração avançada de previsão inclui:
* detecção e featurização de feriados
* aprendizes de série sportiva e DNN (Auto-ARIMA, Prophet, ForecastTCN)
* muitos modelos de suporte através do agrupamento
* validação cruzada de origem de rolamento
* lags configuráveis
* rolando janelas características agregadas


Veja exemplos de regressão e aprendizado automatizado de máquina para previsões nestes notebooks Python: [Previsão de Vendas,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb) [Previsão de Demanda](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)e Previsão de Produção de [Bebidas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automl-works"></a>Como funciona o AutoML

Durante o treinamento, o Azure Machine Learning cria uma série de pipelines em paralelo que experimentam diferentes algoritmos e parâmetros para você. O serviço itera através de algoritmos ML emparelhados com seleções de recursos, onde cada iteração produz um modelo com uma pontuação de treinamento. Quanto maior a pontuação, melhor o modelo é considerado para "encaixar" seus dados.  Ele vai parar quando atingir os critérios de saída definidos no experimento. 

Usando **o Azure Machine Learning,** você pode projetar e executar seus experimentos automatizados de treinamento ML com estas etapas:

1. **Identifique o problema ml** a ser resolvido: classificação, previsão ou regressão

1. **Escolha se deseja usar o Python SDK ou a experiência web do estúdio**: Conheça a paridade entre o Python [SDK e](#parity)a experiência web do estúdio .

   * Para experiência limitada ou sem código, experimente a experiência web do estúdio Azure Machine Learning em[https://ml.azure.com](https://ml.azure.com/)  
   * Para desenvolvedores Python, confira o [Azure Machine Learning Python SDK](how-to-configure-auto-train.md) 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **Especifique a origem e o formato dos dados de treinamento rotulados**: matrizes numpy ou dataframe pandas

1. **Configure o alvo de computação para treinamento de modelos,** como seu computador [local, Azure Machine Learning Computes, VMs remotos ou Databricks Azure](how-to-set-up-training-targets.md).  Saiba mais sobre treinamento automatizado [em um recurso remoto.](how-to-auto-train-remote.md)

1. **Configure os parâmetros automatizados de aprendizado de máquina** que determinam quantas iterações em diferentes modelos, configurações de hiperparâmetros, pré-processamento/featurização avançada e quais métricas olhar ao determinar o melhor modelo.  
1. **Envie o treinamento.**

1. **Revise os resultados** 

O diagrama a seguir ilustra esse processo. 
![Aprendizado automatizado de máquina](./media/concept-automated-ml/automl-concept-diagram2.png)


Você também pode inspecionar as informações de execução [registradas, que contém métricas coletadas](how-to-understand-automated-ml.md) durante a execução. A execução do treinamento produz`.pkl` um objeto serializado Python (arquivo) que contém o modelo e o pré-processamento de dados.

Embora a construção de modelos seja automatizada, você também pode [aprender o quão importantes ou relevantes são](how-to-configure-auto-train.md#explain) os recursos para os modelos gerados.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Pré-processamento

Em todos os experimentos automatizados de aprendizado de máquina, seus dados são pré-processados usando os métodos padrão e, opcionalmente, através de pré-processamento avançado.

> [!NOTE]
> As etapas de pré-processamento automatizado de machine learning (normalização de recursos, manipulação de dados ausentes, conversão de texto em números etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

### <a name="automatic-preprocessing-standard"></a>Pré-processamento automático (padrão)

Em cada experimento automatizado de aprendizado de máquina, seus dados são automaticamente dimensionados ou normalizados para ajudar os algoritmos a ter um bom desempenho.  Durante o treinamento do modelo, uma das seguintes técnicas de dimensionamento ou normalização será aplicada em cada modelo.

|Normalização de dimensionamento&nbsp;&&nbsp;| Descrição |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Padronizar recursos removendo a média e o dimensionamento para a variância unitária  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transforma recursos dimensionando cada recurso pelo mínimo e máximo dessa coluna  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Dimensione cada característica pelo seu valor absoluto máximo |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Este Scaler apresenta por sua gama quantile |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redução da dimensionalidade linear usando a Decomposição de Valor Singular dos dados para projetá-lo para um espaço dimensional inferior |
| [TruncadoSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Este transformador realiza a redução da dimensionalidade linear por meio de decomposição de valor singular truncada (SVD). Ao contrário do PCA, este estimador não centrala os dados antes de calcular a decomposição singular do valor, o que significa que ele pode trabalhar com matrizes esparsas.esparsas eficientemente |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Cada amostra (ou seja, cada linha da matriz de dados) com pelo menos um componente não-zero é redimensionada independentemente de outras amostras de modo que sua norma (l1 ou l2) seja igual a uma |

### <a name="advanced-preprocessing--featurization"></a>Pré-processamento avançado & featurização

Também estão disponíveis pré-processamento avançado e featurização adicionais, como guardrails de dados, codificação e transformações. [Saiba mais sobre o que inclui a featurização.](how-to-use-automated-ml-for-ml-models.md#featurization) Habilite esta configuração com:

+ Estúdio azure Machine Learning: Habilite a **featurização automática** na seção **Exibir configuração adicional** com essas [etapas](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ Python SDK: `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` Especificando [ `AutoMLConfig` ](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)para a classe . 

<a name="parity"></a>

## <a name="the-studio-vs-sdk"></a>O estúdio vs SDK

Saiba mais sobre a paridade e as diferenças entre os recursos ml automatizados de alto nível disponíveis através do Python SDK e do estúdio no Azure Machine Learning. 

### <a name="experiment-settings"></a>Configurações do experimento 

As seguintes configurações permitem configurar seu experimento ML automatizado. 

| |O SDK do Python|A experiência web do estúdio|
----|:----:|:----:
Dividir dados em conjuntos de trem/validação| ✓|✓
Suporta tarefas ml: classificação, regressão e previsão| ✓| ✓
Otimiza com base na métrica primária| ✓| ✓
Suporta a computação AML como alvo de computação | ✓|✓
Configurar horizonte de previsão, meta defasa & janela de rolamento|✓|✓
Definir critérios de saída |✓|✓ 
Definir iterações simultâneas| ✓|✓
Colunas de queda| ✓|✓
Algoritmos de bloqueio|✓|✓
Validação cruzada |✓|✓
Suporta treinamento em clusters Azure Databricks| ✓|
Exibir nomes de recursos projetados|✓|
Resumo da featurização| ✓|
Featurização para feriados|✓|
Níveis de verbosidade de arquivos de log| ✓|

### <a name="model-settings"></a>Configurações do modelo

Essas configurações podem ser aplicadas ao melhor modelo como resultado do seu experimento ml automatizado.

| |O SDK do Python|A experiência web do estúdio|
|----|:----:|:----:|
|Melhor registro de modelo, implantação, explicabilidade| ✓|✓|
|Habilite o conjunto de votação & modelos de conjunto de pilha| ✓|✓|
|Mostrar o melhor modelo com base na métrica não primária|✓||
|Ativar/desativar a compatibilidade do modelo ONNX|✓||
|Testar o modelo | ✓| |

### <a name="run-control-settings"></a>Executar configurações de controle

Essas configurações permitem que você revise e controle suas corridas de experimento saem e seu filho é executado. 

| |O SDK do Python|A experiência web do estúdio|
|----|:----:|:----:|
|Executar tabela de resumo| ✓|✓|
|Cancelar é executado & criança corre| ✓|✓|
|Obter guardrails| ✓|✓|
|Pausa & currículo é executado| ✓| |

## <a name="ensemble-models"></a><a name="ensemble"></a>Modelos de conjunto

O aprendizado automatizado de máquina suporta modelos de conjunto, que são habilitados por padrão. A aprendizagem do conjunto melhora os resultados de aprendizado de máquina e o desempenho preditivo, combinando vários modelos em vez de usar modelos únicos. As iterações do conjunto aparecem como as iterações finais de sua corrida. O aprendizado automatizado de máquina utiliza métodos de conjunto de votação e empilhamento para combinar modelos:

* **Votação**: prevê com base na média ponderada das probabilidades de classe previstas (para tarefas de classificação) ou metas de regressão previstas (para tarefas de regressão).
* **Empilhamento**: o empilhamento combina modelos heterogêneos e treina um metamodelo baseado na saída dos modelos individuais. Os metamodelos padrão atuais são LogisticRegression para tarefas de classificação e ElasticNet para tarefas de regressão/previsão.

O [algoritmo de seleção de conjuntos caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) com inicialização de conjunto ordenado é usado para decidir quais modelos usar dentro do conjunto. Em alto nível, este algoritmo inicializa o conjunto com até cinco modelos com as melhores pontuações individuais, e verifica que esses modelos estão dentro do limiar de 5% da melhor pontuação para evitar um conjunto inicial ruim. Em seguida, para cada iteração do conjunto, um novo modelo é adicionado ao conjunto existente e a pontuação resultante é calculada. Se um novo modelo melhorou a pontuação do conjunto existente, o conjunto é atualizado para incluir o novo modelo.

Consulte [o "como fazer"](how-to-configure-auto-train.md#ensemble) para alterar as configurações padrão do conjunto no aprendizado automatizado de máquina.

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Com o Azure Machine Learning, você pode usar ml automatizado para construir um modelo Python e convertê-lo para o formato ONNX. Uma vez que os modelos estão no formato ONNX, eles podem ser executados em uma variedade de plataformas e dispositivos. Saiba mais sobre [como acelerar os modelos ML com ONNX](concept-onnx.md).

Veja como converter para o formato ONNX [neste exemplo de notebook Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Saiba quais [algoritmos são suportados no ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

O tempo de execução do ONNX também suporta C#, para que você possa usar o modelo construído automaticamente em seus aplicativos C# sem qualquer necessidade de recodificação ou qualquer uma das latências de rede que os pontos finais REST introduzem. Saiba mais sobre [a inferência de modelos ONNX com a API C# em tempo de execução DO ONNX.](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md) 



## <a name="next-steps"></a>Próximas etapas

Veja exemplos e aprenda a construir modelos usando aprendizado automático de máquina:

+ Siga o [Tutorial: Treine automaticamente um modelo de regressão com o Azure Machine Learning](tutorial-auto-train-models.md)

+ Configure as configurações para experimento de treinamento automático:
  + No estúdio Azure Machine Learning, [use essas etapas](how-to-use-automated-ml-for-ml-models.md).
  + Com o Python SDK, [use essas etapas](how-to-configure-auto-train.md).

+ Aprenda a treinar automaticamente usando dados de séries tempois, [use essas etapas](how-to-auto-train-forecast.md).

+ Experimente [amostras do Jupyter Notebook para aprendizado automático de máquina](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* O AUTOMATED ML também está disponível em outras soluções da Microsoft, como, [ML.NET,](https://docs.microsoft.com/dotnet/machine-learning/automl-overview) [HDInsight,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) e [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
