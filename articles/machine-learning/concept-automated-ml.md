---
title: O que é o ML/AutoML automatizado
titleSuffix: Azure Machine Learning
description: Saiba como Azure Machine Learning pode escolher automaticamente um algoritmo para você e gerar um modelo a partir dele para poupar tempo usando os parâmetros e critérios que você fornece para selecionar o melhor algoritmo para seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: ce51a1b25453a5bbacbd268b37f2bd21cfe37fea
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983458"
---
# <a name="what-is-automated-machine-learning-automl"></a>O que é o AutoML (Machine Learning automatizado)?

O Machine Learning automatizado, também conhecido como ML ou AutoML automatizado, é o processo de automatizar as tarefas iterativas demoradas do desenvolvimento de modelos de aprendizado de máquina. Ele permite que cientistas de dados, analistas e desenvolvedores criem modelos de ML com alta escala, eficiência e produtividade, tudo ao mesmo tempo em que sustenta a qualidade do modelo. O ML automatizado se baseia em uma inovação da [divisão do Microsoft Research](https://arxiv.org/abs/1705.05355).

O desenvolvimento de modelos de aprendizado de máquina tradicional tem uso intensivo de recursos, exigindo conhecimento e tempo de domínio significativos para produzir e comparar dezenas de modelos. Com o machine learning automatizado, você vai acelerar o tempo que se leva para colocar modelos de ML prontos em produção com grande facilidade e eficiência.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Quando usar AutoML: classificar, regressão & previsão

Aplique ML automatizado quando desejar que Azure Machine Learning treine e ajuste um modelo para você usando a métrica de destino que você especificar. O ML automatizado democratiza o processo de desenvolvimento do modelo de machine learning e capacita seus usuários, não importando a qualificação em ciência de dados, a identificar um pipeline de aprendizado de máquina de ponta a ponta para qualquer problema.

Cientistas de dados, analistas e desenvolvedores em setores podem usar o ML automatizado para:
+ Implementar soluções de ML sem conhecimento abrangente de programação
+ Economizar tempo e recursos
+ Aproveitar melhores práticas da ciência de dados
+ Fornecer solução de problemas ágil

### <a name="classification"></a>classificação

A classificação é uma tarefa comum de aprendizado de máquina. A classificação é um tipo de aprendizado supervisionado no qual os modelos aprendem a usar dados de treinamento e aplicam esses aprendizados a novos dados. O Azure Machine Learning oferece featurizations especificamente para essas tarefas, como featurizers de texto de rede neural profunda para classificação. Saiba mais sobre [as opções de personalização](how-to-use-automated-ml-for-ml-models.md#featurization). 

A principal meta dos modelos de classificação é prever quais categorias novos dados se encaixarão com base em aprendizados de seus dados de treinamento. Exemplos de classificação comuns incluem detecção de fraudes, reconhecimento de manuscrito e detecção de objetos.  Saiba mais e veja um exemplo de [classificação com o Machine Learning automatizado](tutorial-train-models-with-aml.md).

Veja exemplos de classificação e aprendizado automático de máquina nestes notebooks Python: [detecção de fraudes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [previsão de marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)e [classificação de dados de grupo de notícias](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)

### <a name="regression"></a>Regressão
Semelhante à classificação, as tarefas de regressão também são uma tarefa de aprendizado supervisionada comum. O Azure Machine Learning oferece [featurizations especificamente para essas tarefas](how-to-use-automated-ml-for-ml-models.md#featurization).

Diferente da classificação em que os valores de saída previstos são categóricos, os modelos de regressão preveem valores de saída numéricos com base em previsões independentes. Na regressão, o objetivo é ajudar a estabelecer a relação entre as variáveis de previsão independentes, estimando como uma variável afeta as outras. Por exemplo, preço de automóvel com base em recursos como, quilometragem de gás, classificação de segurança etc. Saiba mais e veja um exemplo de [regressão com o aprendizado de máquina automatizado](tutorial-auto-train-models.md).

Veja exemplos de regressão e aprendizado automático de máquina para previsões nestes blocos de anotações do Python: [previsão de desempenho da CPU](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Previsão da série temporal

A criação de previsões é parte integrante de qualquer empresa, seja ela de receita, inventário, vendas ou demanda do cliente. Você pode usar o ML automatizado para combinar técnicas e abordagens e obter uma previsão de série temporal de alta qualidade e recomendada. Saiba mais com este como fazer: [Machine Learning automatizado para previsão de série temporal](how-to-auto-train-forecast.md). 

Um experimento de série temporal automatizado é tratado como um problema de regressão multivariado. Os valores de série temporal anteriores são "dinamizados" para se tornarem dimensões adicionais para o regressor junto com outros previsões. Essa abordagem, ao contrário dos métodos de série temporal clássica, tem uma vantagem de incorporar naturalmente várias variáveis contextuais e sua relação entre si durante o treinamento. O ML automatizado aprende um modelo único, mas geralmente ramificado internamente para todos os itens no conjunto de e horizontes de previsão. Por isso, mais dados estão disponíveis para estimar os parâmetros de modelo e a generalização para uma série não vista se torna possível.

A configuração de previsão avançada inclui:
* detecção de feriados e personalização
* série temporal e aprendizes DNN (auto-ARIMA, Prophet, ForecastTCN)
* muitos modelos dão suporte ao agrupamento
* validação cruzada de origem sem interrupção
* retardo configurável
* recursos agregados de janela sem interrupção


Veja exemplos de regressão e aprendizado automático de máquina para previsões nestes blocos de anotações do Python: [previsão de vendas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), previsão de [demanda](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)e [previsão de produção de bebidas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automl-works"></a>Como o AutoML funciona

Durante o treinamento, Azure Machine Learning cria um número de pipelines em paralelo que tentam algoritmos e parâmetros diferentes para você. O serviço é iterado por meio de algoritmos de ML emparelhados com seleções de recursos, em que cada iteração produz um modelo com uma pontuação de treinamento. Quanto maior a pontuação, melhor o modelo é considerado para "ajustar" seus dados.  Ele será interrompido quando atingir os critérios de saída definidos no experimento. 

Usando **Azure Machine Learning**, você pode projetar e executar seus experimentos de treinamento de ml automatizados com estas etapas:

1. **Identificar o problema de ml** a ser resolvido: classificação, previsão ou regressão

1. **Escolha se deseja usar o SDK do Python ou a experiência da Web do estúdio**: Saiba mais sobre a paridade entre a [experiência do SDK do Python e da Web do Studio](#parity).

   * Para ter uma experiência limitada ou sem código, experimente a experiência da Web do Azure Machine Learning Studio em[https://ml.azure.com](https://ml.azure.com/)  
   * Para desenvolvedores do Python, confira o [SDK do Azure Machine Learning Python](how-to-configure-auto-train.md) 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **Especifique a origem e o formato dos dados de treinamento rotulados**: matrizes de numpy ou pandas dataframe

1. **Configure o destino de computação para treinamento de modelo**, como seu [computador local, Azure Machine Learning computações, VMS remotas ou Azure Databricks](how-to-set-up-training-targets.md).  Saiba mais sobre o treinamento automatizado [em um recurso remoto](how-to-auto-train-remote.md).

1. **Configure os parâmetros automatizados de aprendizado de máquina** que determinam quantas iterações em diferentes modelos, configurações de hiperparâmetro, pré-processamento avançado/personalização e quais métricas examinar ao determinar o melhor modelo.  
1. **Envie a execução de treinamento.**

1. **Revise os resultados** 

O diagrama a seguir ilustra esse processo. 
![Machine Learning automatizado](./media/concept-automated-ml/automl-concept-diagram2.png)


Você também pode inspecionar as informações de execução registradas, que [contêm as métricas](how-to-understand-automated-ml.md) coletadas durante a execução. A execução de treinamento produz um objeto serializado do`.pkl` Python (arquivo) que contém o modelo e o pré-processamento de dados.

Embora a criação de modelos seja automatizada, você também pode [aprender como os recursos importantes ou relevantes são](how-to-configure-auto-train.md#explain) para os modelos gerados.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Pré-processamento

Em todos os experimentos de aprendizado de máquina automatizados, seus dados são pré-processados usando os métodos padrão e, opcionalmente, por meio de pré-processamento avançado.

> [!NOTE]
> As etapas de pré-processamento automatizado de machine learning (normalização de recursos, manipulação de dados ausentes, conversão de texto em números etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

### <a name="automatic-preprocessing-standard"></a>Pré-processamento automático (padrão)

Em todos os experimentos de aprendizado de máquina automatizados, seus dados são dimensionados e normalizados automaticamente para ajudar os algoritmos a executarem bem.  Durante o treinamento do modelo, uma das técnicas de dimensionamento ou normalização a seguir será aplicada a cada modelo.

|Dimensionamento&nbsp;&&nbsp;de normalização| Descrição |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Padronizar recursos removendo a média e dimensionamento para a variação de unidade  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transforma recursos dimensionando cada recurso pelo mínimo e máximo da coluna  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Dimensionar cada recurso por seu valor absoluto máximo |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Esses recursos de scaler por seu intervalo de Quantil |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redução de dimensionalidade linear usando a decomposição de valor singular dos dados para projetar a ti em um espaço dimensional inferior |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Esse transformador executa a redução de dimensionalidade linear por meio da decomposição de valor singular truncado (SVD). Ao contrário do PCA, esse estimador não centraliza os dados antes de calcular a decomposição do valor singular, o que significa que ele pode trabalhar com matrizes SciPy. esparsas com eficiência |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Cada exemplo (ou seja, cada linha da matriz de dados) com pelo menos um componente diferente de zero é redimensionado independentemente de outros exemplos para que sua norma (L1 ou L2) seja igual a uma |

### <a name="advanced-preprocessing--featurization"></a>Pré-processamento avançado & personalização

O pré-processamento avançado adicional e os personalização também estão disponíveis, como data guardrails, codificação e transformações. [Saiba mais sobre o que o personalização está incluído](how-to-use-automated-ml-for-ml-models.md#featurization). Habilite essa configuração com:

+ Azure Machine Learning Studio: habilite o **personalização automático** na seção **Exibir configuração adicional** [com estas etapas](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ SDK do Python: `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` especificando para a [ `AutoMLConfig` classe](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 



## <a name="ensemble-models"></a><a name="ensemble"></a>Modelos de Ensemble

O aprendizado de máquina automatizado dá suporte a modelos Ensemble, que são habilitados por padrão. O Ensemble Learning melhora os resultados do Machine Learning e o desempenho de previsão combinando vários modelos em oposição ao uso de modelos únicos. As iterações Ensemble são exibidas como as iterações finais da sua execução. O Machine Learning automatizado usa os métodos Ensemble de votação e empilhamento para combinar modelos:

* **Votação**: prevê com base na média ponderada das probabilidades de classe prevista (para tarefas de classificação) ou destinos de regressão previstos (para tarefas de regressão).
* **Empilhamento**: a empilhamento combina modelos heterogêneos e treina um meta-modelo com base na saída dos modelos individuais. Os metamodelos padrão atuais são LogisticRegression para tarefas de classificação e ElasticNet para tarefas de regressão/previsão.

O [algoritmo de seleção Caruana Ensemble](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) com inicialização Ensemble classificada é usado para decidir quais modelos usar dentro do Ensemble. Em um alto nível, esse algoritmo Inicializa o Ensemble com até cinco modelos com as melhores pontuações individuais e verifica se esses modelos estão dentro do limite de 5% da melhor pontuação para evitar uma Ensemble inicial inadequada. Em seguida, para cada iteração Ensemble, um novo modelo é adicionado ao Ensemble existente e a pontuação resultante é calculada. Se um novo modelo tiver melhorado a pontuação Ensemble existente, o Ensemble será atualizado para incluir o novo modelo.

Consulte a [instruções](how-to-configure-auto-train.md#ensemble) para alterar as configurações de Ensemble padrão no Machine Learning automatizado.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Diretrizes sobre destinos de computação local vs. gerenciados remotamente

A interface da Web para o ML automatizado sempre usa um [destino de computação](concept-compute-target.md)remota.  Mas, ao usar o SDK do Python, você escolherá uma computação local ou um destino de computação remota para treinamento de ML automatizado.

* **Computação local**: o treinamento ocorre em seu laptop local ou computação de VM. 
* **Computação remota**: o treinamento ocorre em clusters de computação Machine Learning.  

### <a name="choose-compute-target"></a>Escolher o destino de computação
Considere estes fatores ao escolher seu destino de computação:

 * **Escolha uma computação local**: se seu cenário for sobre explorações iniciais ou demonstrações usando pequenos dados e treinamentos curtos (ou seja, segundos ou alguns minutos por execução filho), o treinamento em seu computador local pode ser uma opção melhor.  Não há tempo de configuração, os recursos de infraestrutura (seu PC ou VM) estão disponíveis diretamente.
 * **Escolha um cluster de computação do ml remoto**: se você estiver treinando com grandes conjuntos de altos, como no treinamento de produção criando modelos que precisam de treinamentos mais longos, a computação remota fornecerá `AutoML` um desempenho de ponta a ponta muito melhor, pois colocará em paralelo as trens nos nós do cluster. Em uma computação remota, o tempo de inicialização para a infraestrutura interna será adicionado cerca de 1,5 minutos por execução de filho, além de minutos adicionais para a infraestrutura de cluster se as VMs ainda não estiverem em funcionamento.

### <a name="pros-and-cons"></a>Prós e contras
Considere esses prós e contras ao optar por usar o local versus remoto.

|  | Prós (vantagens)  |Contras (handicaps)  |
|---------|---------|---------|---------|
|**Destino de computação local** |  <li> Nenhum tempo de inicialização do ambiente   | <li>  Subconjunto de recursos<li>  Não é possível paralelizar execuções <li> Pior para dados grandes. <li>Não há streaming de dados durante o treinamento <li>  Nenhum personalização baseado em DNN <li> Somente SDK do Python |
|**Clusters de computação ML remotos**|  <li> Conjunto completo de recursos <li> Paralelizar execuções filhas <li>   Suporte a dados grandes<li>  Personalização baseado em DNN <li>  Escalabilidade dinâmica do cluster de computação sob demanda <li> Experiência sem código (interface do usuário da Web) também disponível  |  <li> Tempo de inicialização para nós de cluster <li> Tempo de inicialização para cada execução de filho    |

### <a name="feature-availability"></a>Disponibilidade de recursos 

 Mais recursos estão disponíveis quando você usa a computação remota, conforme mostrado na tabela a seguir. Alguns desses recursos estão disponíveis apenas em um espaço de trabalho corporativo.

| Recurso                                                    | Remoto | Local | Requisitos <br>Espaço de trabalho corporativo |
|------------------------------------------------------------|--------|-------|-------------------------------|
| Streaming de dados (suporte a dados grandes, até 100 GB)          | ✓      |       | ✓                             |
| Personalização de texto baseado em DNN-BERT e treinamento             | ✓      |       | ✓                             |
| Suporte de GPU pronto para uso (treinamento e inferência)        | ✓      |       | ✓                             |
| Classificação de imagem e suporte a rotulagem                  | ✓      |       | ✓                             |
| Modelos auto-ARIMA, Prophet e ForecastTCN para previsão | ✓      |       | ✓                             |
| Várias execuções/iterações em paralelo                       | ✓      |       | ✓                             |
| Criar modelos com interpretabilidade na interface do usuário da experiência Web do AutoML Studio      | ✓      |       | ✓                             |
| Personalização da engenharia de recursos na interface do usuário da experiência Web do estúdio                        | ✓      |       | ✓                              |
| Ajuste de hiperparâmetro do Azure ML                             | ✓      |       |                               |
| Suporte ao fluxo de trabalho de pipeline do Azure ML                         | ✓      |       |                               |
| Continuar uma execução                                             | ✓      |       |                               |
| Previsão                                                | ✓      | ✓     | ✓                             |
| Criar e executar experimentos em blocos de anotações                    | ✓      | ✓     |                               |
| Registrar e visualizar as informações e métricas do experimento na interface do usuário | ✓      | ✓     |                               |
| Guardrails de dados                                            | ✓      | ✓     |                               |


## <a name="automated-ml-in-azure-machine-learning"></a>ML automatizado no Azure Machine Learning

O Azure Machine Learning oferece duas experiências para trabalhar com ML automatizado

* Para clientes experientes em código, [Azure Machine Learning SDK do Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* Para clientes com experiência limitada/sem código, Azure Machine Learning Studio em[https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>Configurações do experimento 

As configurações a seguir permitem que você configure seu experimento de ML automatizado. 

| |O SDK do Python|A experiência Web do estúdio|
----|:----:|:----:
Dividir dados em conjuntos de treinamento/validação| ✓|✓
Dá suporte a tarefas de ML: classificação, regressão e previsão| ✓| ✓
Otimiza com base na métrica primária| ✓| ✓
Dá suporte à computação AML como destino de computação | ✓|✓
Configurar horizonte de previsão, atraso de destino & janela sem interrupção|✓|✓
Definir critérios de saída |✓|✓ 
Definir iterações simultâneas| ✓|✓
Remover colunas| ✓|✓
Algoritmos de bloco|✓|✓
Validação cruzada |✓|✓
Dá suporte ao treinamento em clusters Azure Databricks| ✓|
Exibir nomes de recursos com engenharia|✓|
Resumo de personalização| ✓|
Personalização para feriados|✓|
Níveis de detalhes do arquivo de log| ✓|

### <a name="model-settings"></a>Configurações de modelo

Essas configurações podem ser aplicadas ao melhor modelo como resultado de seu experimento de ML automatizado.

| |O SDK do Python|A experiência Web do estúdio|
|----|:----:|:----:|
|Melhor registro de modelo, implantação, explicabilidade| ✓|✓|
|Habilitar Ensemble de votação & os modelos de Ensemble de pilha| ✓|✓|
|Mostrar o melhor modelo com base na métrica não primária|✓||
|Habilitar/desabilitar a compatibilidade do modelo de ONNX|✓||
|Testar o modelo | ✓| |

### <a name="run-control-settings"></a>Executar configurações de controle

Essas configurações permitem que você examine e controle suas execuções de experimento e suas execuções filhas. 

| |O SDK do Python|A experiência Web do estúdio|
|----|:----:|:----:|
|Tabela de Resumo de execução| ✓|✓|
|Cancelar execuções & as execuções filhas| ✓|✓|
|Obter guardrails| ✓|✓|
|Pausar & retomar execuções| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Com Azure Machine Learning, você pode usar o ML automatizado para criar um modelo Python e fazer com que ele seja convertido no formato ONNX. Depois que os modelos estiverem no formato ONNX, eles poderão ser executados em uma variedade de plataformas e dispositivos. Saiba mais sobre [a aceleração de modelos de ml com o ONNX](concept-onnx.md).

Consulte Como converter para o formato ONNX [neste exemplo de notebook Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Saiba quais [algoritmos têm suporte no ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

O tempo de execução do ONNX também dá suporte a C#, de modo que você pode usar o modelo criado automaticamente em seus aplicativos em C# sem necessidade de recodificação ou qualquer uma das latências de rede que os pontos de extremidade REST introduzem. Saiba mais sobre [os modelos inferência ONNX com a API C# do ONNX Runtime](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 

## <a name="next-steps"></a>Próximas etapas

Veja exemplos e aprenda a criar modelos usando o aprendizado de máquina automatizado:

+ Defina as configurações para o teste de treinamento automático:
  + No Azure Machine Learning Studio, [Use estas etapas](how-to-use-automated-ml-for-ml-models.md).
  + Com o SDK do Python, [Use estas etapas](how-to-configure-auto-train.md).

+ Saiba como usar um [destino de computação remota](how-to-auto-train-remote.md)

+ Siga o [tutorial: treinar automaticamente um modelo de regressão com Azure Machine Learning](tutorial-auto-train-models.md) 

+ Saiba como treinar automaticamente usando dados de série temporal, [Use estas etapas](how-to-auto-train-forecast.md).

+ Experimente [Jupyter Notebook amostras para o aprendizado de máquina automatizado](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
* O ML automatizado também está disponível em outras soluções da Microsoft, como [ml.net](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-automated) e [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
