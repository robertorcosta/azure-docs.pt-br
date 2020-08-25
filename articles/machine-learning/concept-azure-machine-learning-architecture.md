---
title: Arquitetura e principais conceitos
titleSuffix: Azure Machine Learning
description: Saiba mais sobre a arquitetura, os termos e os conceitos que compõem Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: d7bad24510f74a7fadd74328e24ea22855e6fe02
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750835"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Como o Azure Machine Learning funciona: Arquitetura e conceitos

Saiba mais sobre a arquitetura e os conceitos para Azure Machine Learning.

> [!NOTE]
> Embora este artigo defina termos e conceitos usados pelo Azure Machine Learning, ele não define os termos e conceitos para a plataforma do Azure. Para obter mais informações sobre a terminologia da plataforma do Azure, consulte o [Glossário do Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a><a name="workspace"></a> Espaço

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Arquitetura de Azure Machine Learning":::

Um [espaço de trabalho do Machine Learning](concept-workspace.md) é o recurso de nível superior para Azure Machine Learning.  O espaço de trabalho é o local centralizado para:
* Gerencie os recursos que você usa para treinamento e implantação de modelos, como [cálculos](#compute-instance)
* Armazene ativos que você cria ao usar Azure Machine Learning, incluindo:
  * [Ambientes](#environments)
  * [Execuções](#runs)
  * [Pipelines](#ml-pipelines)
  * [Conjunto de dados](#datasets-and-datastores)
  * [Modelos](#models)
  * [Pontos de extremidade](#endpoints)

Um espaço de trabalho inclui outros recursos do Azure que são usados pelo espaço de trabalho:

+ [Registro de contêiner do Azure](https://azure.microsoft.com/services/container-registry/): registra os contêineres do Docker que você usa durante o treinamento e quando implanta um modelo. Para minimizar os custos, o ACR é **carregado com o preguiçoso** até que as imagens de implantação sejam criadas.
+ [Conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/): é usada como o repositório de armazenamento padrão para o espaço de trabalho.  Os blocos de anotações do Jupyter que são usados com suas instâncias de computação Azure Machine Learning também são armazenados aqui.
+ [Insights de aplicativo Azure](https://azure.microsoft.com/services/application-insights/): armazena informações de monitoramento sobre seus modelos.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): armazena segredos que são usados por destinos de computação e outras informações confidenciais que são necessárias para o espaço de trabalho.

Você pode compartilhar um workspace com outras pessoas.

## <a name="studio"></a>Estúdio

O [Azure Machine Learning Studio](https://ml.azure.com) fornece uma exibição da Web de todos os artefatos em seu espaço de trabalho.  Esse portal também é onde você acessa as ferramentas interativas que fazem parte do Azure Machine Learning:

+ [Designer de Azure Machine Learning (versão prévia)](concept-designer.md) para executar etapas de fluxo de trabalho sem escrever código
+ Experiência na Web para o [aprendizado de máquina automatizado](concept-automated-ml.md)
+ [Dados rotulando projetos](how-to-create-labeling-projects.md) para criar, gerenciar e monitorar projetos para rotular seus dados

##  <a name="computes"></a>Calcula

<a name="compute-targets"></a> Um [destino de computação](concept-compute-target.md) é o computador ou conjunto de computadores em que você executa o script de treinamento ou hospeda sua implantação de serviço. Esse local pode ser seu computador local ou um recurso de computação remota.

Azure Machine Learning apresenta dois recursos de computação baseados em nuvem totalmente gerenciados que são configurados para tarefas de aprendizado de máquina:

* <a name="compute-instance"></a>[Computeinstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance?view=azure-ml-py)( **instância de computação** ): uma instância de computação é uma VM (máquina virtual) que inclui várias ferramentas e ambientes instalados para o aprendizado de máquina. Use uma instância de computação como sua estação de trabalho de desenvolvimento para iniciar a execução de notebooks de exemplo sem a necessidade de configuração. Também pode ser usado como um destino de computação para trabalhos de treinamento e inferência.
* **Clusters de computação** ([Amlcompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)): cluster de VMs com recursos de dimensionamento de vários nós. Escala verticalmente automaticamente quando um trabalho é enviado. Mais adequado para destinos de computação para trabalhos grandes e produção. Use como um destino de computação de treinamento ou para implantação de desenvolvimento/teste.

Para obter mais informações sobre os destinos de computação de treinamento, consulte [Training Compute targets](concept-compute-target.md#train).  Para obter mais informações sobre destinos de computação de implantação, consulte [destinos de implantação](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Conjuntos de dados e armazenamentos de dados

[**Azure Machine Learning DataSets**](concept-data.md#datasets)  facilitam o acesso e o trabalho com seus dados. Os conjuntos de dados gerenciam dados em vários cenários, como treinamento de modelo e criação de pipeline. Ao usar o SDK do Azure Machine Learning, você pode acessar o armazenamento subjacente, explorar dados e gerenciar o ciclo de vida de diferentes definições do conjunto de dados.

Os conjuntos de dados fornecem métodos para trabalhar com dados em formatos conhecidos, como o uso de `from_delimited_files()` ou `to_pandas_dataframe()`.

Para obter mais informações, confira [Criar e registrar conjuntos de dados do Azure Machine Learning](how-to-create-register-datasets.md).  Para obter mais exemplos do uso de conjuntos de dados, confira os [exemplos de notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Um [**datastore**](concept-data.md#datastores) é uma abstração de armazenamento em uma conta de armazenamento do Azure. Cada workspace tem um repositório de dados padrão e você poderá registrar repositórios de dados adicionais. Use a API do SDK do Python ou a CLI do Azure Machine Learning para armazenar e recuperar arquivos do repositório de dados. 

## <a name="models"></a>Modelos

Em sua forma mais simples, um modelo é um trecho de código que usa uma entrada e produz uma saída. A criação de um modelo de aprendizado de máquina envolve a seleção de um algoritmo, o fornecimento de dados e o [ajuste de hiperparâmetros](how-to-tune-hyperparameters.md). O treinamento é um processo iterativo que produz um modelo treinado, que encapsula o modelo aprendido durante o processo de treinamento.

Um modelo é produzido por uma [execução](#runs) de um [experimento](#experiments) no Azure Machine Learning. Você também pode usar um modelo de treinamento fora do Azure Machine Learning. Em seguida, você [registra o modelo](#register-model) no espaço de trabalho.

O Azure Machine Learning é independente do framework. Quando você cria um modelo, pode usar qualquer estrutura de aprendizado de máquina conhecida, como Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Para obter um exemplo de treinamento de um modelo usando Scikit-learn, consulte [tutorial: treinar um modelo de classificação de imagem com Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="model-registry"></a><a name="register-model"></a> Registro de modelo
[Espaço de trabalho](#workspace)  >  **Registro de modelo**

O **registro de modelo** permite manter o controle de todos os modelos em seu espaço de trabalho Azure Machine Learning.

Os modelos são identificados por nome e versão. Cada vez que você registra um modelo com o mesmo nome de um já existente, o registro pressupõe que se trata de uma nova versão. A versão é incrementada e o novo modelo é registrado com o mesmo nome.

Você pode fornecer as marcas de metadados adicionais quando registrar o modelo e, em seguida, usar essas marcas ao procurar pelos modelos.

> [!TIP]
> Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo em seu workspace do Azure Machine Learning. Após o registro, você pode baixar ou implantar o modelo registrado e receber todos os arquivos registrados.

Não é possível excluir um modelo registrado que está sendo usado por uma implantação ativa.

Para obter um exemplo de registro de um modelo, consulte [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="environments"></a>Ambientes

[Espaço de trabalho](#workspace)  >  **Ambientes**

Um [ambiente](concept-environments.md) é o encapsulamento do ambiente em que ocorre o treinamento ou a Pontuação do seu modelo de aprendizado de máquina. O ambiente especifica os pacotes do Python, as variáveis de ambiente e as configurações de software em relação aos seus scripts de treinamento e pontuação.

Para obter exemplos de código, consulte a seção "gerenciar ambientes" de [como usar ambientes](how-to-use-environments.md#manage-environments).

### <a name="experiments"></a>Testes

[Espaço de trabalho](#workspace)  >  **Experimentos**

Um experimento é um agrupamento de diversas execuções de um determinado script. Ele sempre pertence a um workspace. Quando você envia uma execução, você pode fornecer um nome de experimento. As informações para a execução são armazenadas nesse experimento. Se o nome não existir quando você enviar um experimento, um novo experimento será criado automaticamente.

Para obter um exemplo do uso de um experimento, confira [Tutorial: treinar seu primeiro modelo](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Execuções

[Espaço de trabalho](#workspace)  >  [Experimentos](#experiments)  >  **Executar**

Uma execução é uma execução única de um script de treinamento. Um experimento normalmente conterá várias execuções.

O Azure Machine Learning registra todas as execuções e armazena as seguintes informações no experimento:

* Metadados sobre a execução (carimbo de hora, duração, etc.)
* Métricas registradas pelo seu script
* Arquivos de saída coletados automaticamente pelo experimento ou carregados explicitamente por você
* Um instantâneo do diretório que contém seus scripts, antes da execução

Uma execução é produzida quando você envia um script para fazer o treinamento de um modelo. Uma execução pode ter zero ou mais execuções filho. Por exemplo, a execução de nível superior pode ter duas execuções filho, cada uma delas pode ter sua próprias execuções filho.

### <a name="run-configurations"></a>Configurações de execução

[Espaço de trabalho](#workspace)  >  [Experimentos](#experiments)  >  [Executar](#runs)  >  **Configuração de execução**

Uma configuração de execução é um conjunto de instruções que define como um script deve ser executado em um destino de computação especificado. Ela inclui um amplo conjunto de definições de comportamento, como se deseja usar um ambiente de Python existente ou usar um ambiente de Conda criado a partir de uma especificação.

Uma configuração de execução pode ser persistida em um arquivo dentro do diretório que contém o script de treinamento.   Ou pode ser construído como um objeto na memória e usado para enviar uma execução.

Para obter um exemplo das configurações de execução, consulte [Selecionar e usar um destino de computação para fazer o treinamento do seu modelo](how-to-set-up-training-targets.md).

### <a name="estimators"></a>Avaliadores

Para facilitar o treinamento de modelo com estruturas conhecidas, a classe avaliadora permite que você construa facilmente configurações de execução. Você pode criar e usar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico de para enviar scripts de treinamento que usam qualquer estrutura de aprendizado que você escolher (como Scikit-learn).

Para tarefas de PyTorch, TensorFlow e Chainer, o Azure Machine Learning também oferece os respectivos avaliadores de [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar o uso dessas estruturas.

Para obter mais informações, consulte os seguintes artigos:

* [Treinar modelos de ML com avaliadores](how-to-train-ml-models.md).
* [Treine os modelos de aprendizado profundo do Pytorch em escala com o Azure Machine Learning](how-to-train-pytorch.md).
* [Treine e registre modelos do TensorFlow em escala com o Azure Machine Learning](how-to-train-tensorflow.md).
* [Treine e registre modelos do Chainer em escala com o Azure Machine Learning](how-to-train-ml-models.md).

### <a name="snapshots"></a>Instantâneos

[Espaço de trabalho](#workspace)  >  [Experimentos](#experiments)  >  [Executar](#runs)  >  **Instantâneo**

Ao enviar uma execução, o Azure Machine Learning compacta o diretório que contém o script como um arquivo zip e o envia para o destino de computação. O arquivo zip é expandido e o script é executado lá. O Azure Machine Learning também armazena o arquivo zip como um instantâneo como parte do registro de execução. Qualquer pessoa com acesso ao workspace pode procurar um registro de execução e baixar o instantâneo.


### <a name="logging"></a>Registro em log

Ao desenvolver sua solução, use o SDK do Python do Azure Machine Learning em seu script de Python para registrar métricas arbitrárias. Após a execução, consulte as métricas para determinar se a execução produziu o modelo que você deseja implantar.


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Acompanhamento e integração do Git

Quando você inicia uma execução de treinamento em que o diretório de origem é um repositório Git local, as informações sobre o repositório são armazenadas no histórico de execuções. Funciona com execuções enviadas usando um avaliador, pipeline de ML ou execução de script. Também funciona para execuções enviadas do SDK ou da CLI do Machine Learning.

Para obter mais informações, confira [Integração do Git com o Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="deployment"></a>Implantação

Você implanta um [modelo registrado](#register-model) como um ponto de extremidade de serviço. Você precisa dos seguintes componentes:

* **Ambiente**. Esse ambiente encapsula as dependências necessárias para executar seu modelo para a inferência.
* **Código de Pontuação**. Esse script aceita solicitações, pontua as solicitações usando o modelo e retorna os resultados.
* **Configuração de inferência**. A configuração de inferência especifica o ambiente, o script de entrada e outros componentes necessários para executar o modelo como um serviço.

Para obter mais informações sobre esses componentes, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Pontos de extremidade

[Espaço de trabalho](#workspace)  >  **Pontos de extremidade**

Um ponto de extremidade é uma instanciação do modelo em um serviço Web que pode ser hospedado na nuvem ou um módulo de IoT para implantações de dispositivos integrados.

#### <a name="web-service-endpoint"></a>Ponto de extremidade de serviço Web

Na implantação de um modelo como um serviço Web, o ponto de extremidade pode ser implantado em Instâncias de Contêiner do Azure, Serviço de Kubernetes do Azure ou FPGAs. O serviço é criado a partir de um modelo, script e arquivos associados. Eles são colocados em uma imagem de contêiner de base, que contém o ambiente de execução do modelo. A imagem tem um balanceamento de carga, ponto de extremidade HTTP que recebe as solicitações de pontuação enviadas para o serviço Web.

Você pode habilitar a telemetria Application Insights ou telemetria de modelo para monitorar seu serviço Web. Os dados de telemetria só podem ser acessados por você.  Ele é armazenado em suas instâncias de conta de armazenamento e Application Insights.

Se você tiver habilitado o dimensionamento automático, o Azure dimensionará automaticamente sua implantação.

Para obter um exemplo de implantação de um modelo como um serviço web, consulte [Implantar um modelo de classificação de imagem nas Instâncias de Contêiner do Azure](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Pontos de extremidade do módulo de IoT

Um ponto de extremidade do módulo de IoT implantado é um contêiner do Docker que inclui seu modelo, o script ou aplicativo associado e as dependências adicionais. Esses módulos são implantados usando o Azure IoT Edge em dispositivos de borda.

Se você tiver habilitado o monitoramento, o Azure coleta dados de telemetria do modelo de dentro do módulo do Azure IoT Edge. Os dados de telemetria estão acessíveis apenas para você e armazenados em sua instância de conta de armazenamento.

O Azure IoT Edge garantirá que seu módulo esteja em execução e monitorará o dispositivo que está hospedando.
. 
## <a name="automation"></a>Automação

### <a name="azure-machine-learning-cli"></a>CLI do Azure Machine Learning 

A [CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md) é uma extensão do CLI do Azure, uma interface de linha de comando de plataforma cruzada para a plataforma do Azure. Essa extensão fornece comandos para automatizar suas atividades de aprendizado de máquina.

### <a name="ml-pipelines"></a>Pipelines de ML

Você usa [pipelines de Machine Learning](concept-ml-pipelines.md) para criar e gerenciar fluxos de trabalho que unem as fases de aprendizado de máquina. Por exemplo, um pipeline pode incluir preparação de dados, treinamento de modelo, implantação de modelo e fases de inferência/pontuação. Cada fase pode incluir várias etapas, cada uma delas pode ser executada de modo autônomo em vários destinos de computação. 

As etapas do pipeline são reutilizáveis e poderão ser executadas sem realizar novamente as etapas anteriores se o resultado dessas etapas não tiver sido alterado. Por exemplo, você poderá treinar novamente um modelo sem executar pela segunda vez as caras etapas de preparação de dados se os dados não forem alterados. Os pipelines também permitem que cientistas de dados colaborem enquanto trabalham em áreas separadas de um fluxo de trabalho de aprendizado de máquina.

## <a name="interacting-with-machine-learning"></a>Interagindo com o Machine Learning

> [!IMPORTANT]
> As ferramentas marcadas (visualização) abaixo estão atualmente em visualização pública.
> A versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Interaja com o serviço em qualquer ambiente Python com o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interaja com o serviço em qualquer ambiente do R com o [SDK do Azure Machine Learning para R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (versão prévia).
+ Use o [designer do Azure Machine Learning (versão prévia)](concept-designer.md) para executar as etapas do fluxo de trabalho sem escrever código. (Um [espaço de trabalho corporativo](concept-workspace.md#upgrade)) é necessário para usar o designer.)
+ Use [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) para automação.
+ O [Acelerador de Solução de Muitos Modelos](https://aka.ms/many-models) (versão prévia) é baseado no Azure Machine Learning e permite treinar, operar e gerenciar centenas ou até milhares de modelos de machine learning.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Azure Machine Learning, confira:

* [O que é Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Criar um workspace de Azure Machine Learning](how-to-manage-workspace.md)
* [Tutorial (parte 1): Treinar um modelo](tutorial-train-models-with-aml.md)
