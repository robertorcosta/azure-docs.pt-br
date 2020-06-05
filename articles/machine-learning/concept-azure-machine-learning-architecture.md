---
title: Arquitetura e principais conceitos
titleSuffix: Azure Machine Learning
description: Saiba mais sobre a arquitetura, a terminologia, os conceitos e os fluxos de trabalho que compõem o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 05/13/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 49c23774fe16c24ba90daa02cdda1688b79b12d3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683050"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Como o Azure Machine Learning funciona: Arquitetura e conceitos

Saiba mais sobre a arquitetura, os conceitos e o fluxo de trabalho do Azure Machine Learning. Os principais componentes do serviço e o fluxo de trabalho geral ao usar o serviço que são mostrados no diagrama:

![Arquitetura e fluxo de trabalho do Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Fluxo de trabalho

O fluxo de trabalho do modelo de machine learning geralmente segue esta sequência:

1. **Treinamento**
    + Desenvolver scripts de treinamento de aprendizado de máquina no **Python**, **R** ou com o designer visual.
    + Criar e configurar um **destino de computação**.
    + **Enviar os scripts** a um destino de computação configurado para ser executado nesse ambiente. Durante o treinamento, os scripts podem ler ou gravar nos **armazenamentos de dados**. Os logs e a saída produzidos durante o treinamento são salvos como **execuções** no **workspace** e agrupados em **experimentos**.

1. **Pacote** – Depois que uma execução satisfatória for encontrada, registre o modelo persistente no **registro de modelo**.

1. **Validar** - **Consulte o experimento** quanto a métricas registradas nas execuções atuais e anteriores. Se as métricas não indicarem um resultado desejado, volte para a etapa 1 e itere em seus scripts.

1. **Implantar** – Desenvolva um script de pontuação que usa o modelo e **implante o modelo** como um **serviço Web** no Azure ou em um **dispositivo do IOT Edge**.

1. **Monitorar** – Monitore o **descompasso de dados** entre o conjunto de dados de treinamento e os dados de inferência de um modelo implantado. Quando necessário, volte à etapa 1 para treinar novamente o modelo com os novos dados de treinamento.

## <a name="tools-for-azure-machine-learning"></a>Ferramentas para o Azure Machine Learning

Use estas ferramentas para o Azure Machine Learning:

+  Interaja com o serviço em qualquer ambiente Python com o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interaja com o serviço em qualquer ambiente R com o [SDK do Azure Machine Learning para R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatize as atividades de aprendizado de máquina com a [CLI do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli).
+ Use o [designer do Azure Machine Learning (versão prévia)](concept-designer.md) para executar as etapas do fluxo de trabalho sem escrever código.
+ O [Acelerador de Solução de Muitos Modelos](https://aka.ms/many-models) (versão prévia) é baseado no Azure Machine Learning e permite treinar, operar e gerenciar centenas ou até milhares de modelos de machine learning.

> [!NOTE]
> Embora este artigo defina termos e conceitos usados pelo Azure Machine Learning, ele não define os termos e conceitos para a plataforma do Azure. Para obter mais informações sobre a terminologia da plataforma do Azure, consulte o [Glossário do Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Glossário

* [Atividade](#activities)
* [Workspace](#workspaces)
    * [Testes](#experiments)
        * [Executar](#runs) 
            * [Configuração de execução](#run-configurations)
            * [Instantâneo](#snapshots)
            * [Acompanhamento Git](#github-tracking-and-integration)
            * [Logging](#logging)
    * [Pipelines de ML](#ml-pipelines)
    * [Modelos](#models)
        * [Ambientes](#environments)
        * [Script de treinamento](#training-scripts)
        * [Avaliadores](#estimators)
    * [Pontos de extremidade](#endpoints)
        * [Serviço Web](#web-service-endpoint)
        * [Módulos de IoT](#iot-module-endpoints)
    * [Conjunto de dados e armazenamentos de dados](#datasets-and-datastores)
    * [Destinos de computação](#compute-targets)

### <a name="activities"></a>Atividades

Uma atividade representa uma operação de execução demorada. As operações a seguir estão exemplos de atividades:

* Criar ou excluir um destino de computação
* Executar um script em um destino de computação

As atividades podem fornecer notificações por meio do SDK ou da IU da Web, portanto, você pode facilmente monitorar o progresso dessas operações.

### <a name="workspaces"></a>Workspaces

[O workspace](concept-workspace.md) é o recurso de nível superior do Azure Machine Learning. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar ao usar o Azure Machine Learning. Você pode compartilhar um workspace com outras pessoas. Para obter uma descrição detalhada dos workspaces, confira [O que é um workspace do Azure Machine Learning?](concept-workspace.md).

### <a name="experiments"></a>Testes

Um experimento é um agrupamento de diversas execuções de um determinado script. Ele sempre pertence a um workspace. Quando você envia uma execução, você pode fornecer um nome de experimento. As informações para a execução são armazenadas nesse experimento. Se você enviar uma execução e especificar um nome de experimento que não existe, um novo experimento com esse nome recém-especificado é criado automaticamente.

Para obter um exemplo do uso de um experimento, confira [Tutorial: treinar seu primeiro modelo](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Execuções

Uma execução é uma execução única de um script de treinamento. Um experimento normalmente conterá várias execuções.

O Azure Machine Learning registra todas as execuções e armazena as seguintes informações no experimento:

* Metadados sobre a execução (carimbo de hora, duração, etc.)
* Métricas registradas pelo seu script
* Arquivos de saída coletados automaticamente pelo experimento ou carregados explicitamente por você
* Um instantâneo do diretório que contém seus scripts, antes da execução

Uma execução é produzida quando você envia um script para fazer o treinamento de um modelo. Uma execução pode ter zero ou mais execuções filho. Por exemplo, a execução de nível superior pode ter duas execuções filho, cada uma delas pode ter sua próprias execuções filho.

### <a name="run-configurations"></a>Configurações de execução

Uma configuração de execução é um conjunto de instruções que define como um script deve ser executado em um destino de computação especificado. Ela inclui um amplo conjunto de definições de comportamento, como se deseja usar um ambiente de Python existente ou usar um ambiente de Conda criado a partir de uma especificação.

Uma configuração de execução pode ser persistida em um arquivo dentro do diretório que contém o script de treinamento ou construída como um objeto na memória e usada para enviar uma execução.

Para obter um exemplo das configurações de execução, consulte [Selecionar e usar um destino de computação para fazer o treinamento do seu modelo](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Instantâneos

Ao enviar uma execução, o Azure Machine Learning compacta o diretório que contém o script como um arquivo zip e o envia para o destino de computação. O arquivo zip é expandido e o script é executado lá. O Azure Machine Learning também armazena o arquivo zip como um instantâneo como parte do registro de execução. Qualquer pessoa com acesso ao workspace pode procurar um registro de execução e baixar o instantâneo.

> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="github-tracking-and-integration"></a>Acompanhamento e integração do GitHub

Quando você inicia uma execução de treinamento em que o diretório de origem é um repositório Git local, as informações sobre o repositório são armazenadas no histórico de execuções. Funciona com execuções enviadas usando um avaliador, pipeline de ML ou execução de script. Também funciona para execuções enviadas do SDK ou da CLI do Machine Learning.

Para obter mais informações, confira [Integração do Git com o Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="logging"></a>Registro em log

Ao desenvolver sua solução, use o SDK do Python do Azure Machine Learning em seu script de Python para registrar métricas arbitrárias. Após a execução, consulte as métricas para determinar se a execução produziu o modelo que você deseja implantar.

### <a name="ml-pipelines"></a>Pipelines de ML

Os pipelines de aprendizado de máquina são usados para criar e gerenciar fluxos de trabalho que unem as fases de aprendizado da máquina. Por exemplo, um pipeline pode incluir preparação de dados, treinamento de modelo, implantação de modelo e fases de inferência/pontuação. Cada fase pode incluir várias etapas, cada uma delas pode ser executada de modo autônomo em vários destinos de computação. 

As etapas do pipeline são reutilizáveis e poderão ser executadas sem realizar novamente as etapas anteriores se o resultado dessas etapas não tiver sido alterado. Por exemplo, você poderá treinar novamente um modelo sem executar pela segunda vez as caras etapas de preparação de dados se os dados não forem alterados. Os pipelines também permitem que cientistas de dados colaborem enquanto trabalham em áreas separadas de um fluxo de trabalho de aprendizado de máquina.

Para obter mais informações sobre os pipelines de aprendizado de máquina com esse serviço, consulte [Pipelines e Azure Machine Learning](concept-ml-pipelines.md).

### <a name="models"></a>Modelos

Em sua forma mais simples, um modelo é um trecho de código que usa uma entrada e produz uma saída. Criar um modelo de aprendizado de máquina envolve selecionar um algoritmo, fornecer dados a ele e ajustar hiperparâmetros. O treinamento é um processo iterativo que produz um modelo treinado, que encapsula o modelo aprendido durante o processo de treinamento.

Um modelo é produzido por uma execução no Azure Machine Learning. Você também pode usar um modelo de treinamento fora do Azure Machine Learning. Você pode registrar um modelo em um workspace do Azure Machine Learning.

O Azure Machine Learning é independente do framework. Quando você cria um modelo, pode usar qualquer estrutura de aprendizado de máquina conhecida, como Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Para obter um exemplo de treinamento de um modelo usando Scikit-learn e um avaliador, confira [Tutorial: treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

O **registro de modelo** monitora todos os modelos no seu workspace do Azure Machine Learning.

Os modelos são identificados por nome e versão. Cada vez que você registra um modelo com o mesmo nome de um já existente, o registro pressupõe que se trata de uma nova versão. A versão é incrementada e o novo modelo é registrado com o mesmo nome.

Você pode fornecer as marcas de metadados adicionais quando registrar o modelo e, em seguida, usar essas marcas ao procurar pelos modelos.

> [!TIP]
> Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo em seu workspace do Azure Machine Learning. Após o registro, você pode baixar ou implantar o modelo registrado e receber todos os arquivos registrados.

Não é possível excluir um modelo registrado que está sendo usado por uma implantação ativa.

Para obter um exemplo de registro de um modelo, consulte [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="environments"></a>Ambientes

Os ambientes do Azure ML são usados para especificar a configuração (Docker/Python/Spark/etc.) usada na criação de um ambiente reproduzível para preparação de dados, treinamento de modelo e serviço de modelo. São entidades gerenciadas e com controle de versão dentro de seu workspace do Azure Machine Learning que habilitam fluxos de trabalho de aprendizado de máquina portáteis, reproduzíveis e auditáveis em diferentes destinos de computação.

Você pode usar um objeto de ambiente em sua computação local para desenvolver seu script de treinamento, reutilizar o mesmo ambiente em Computação do Azure Machine Learning para treinamento de modelo em escala e até mesmo implantar seu modelo com o mesmo ambiente. 

Saiba [como criar e gerenciar um ambiente de ML reutilizável](how-to-use-environments.md) para treinamento e inferência.

### <a name="training-scripts"></a>Scripts de treinamento

Para treinar um modelo, você deve especificar o diretório que contém o script de treinamento e os arquivos associados. Você também pode especificar um nome de experimento, que é usado para armazenar as informações obtidas durante o treinamento. Durante o treinamento, o diretório inteiro é copiado para o ambiente de treinamento (destino de computação) e o script especificado pela configuração de execução é iniciado. Um instantâneo do diretório também é armazenado no experimento no workspace.

Para um exemplo, veja [Tutorial: treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="estimators"></a>Avaliadores

Para facilitar o treinamento de modelo com estruturas conhecidas, a classe avaliadora permite que você construa facilmente configurações de execução. Você pode criar e usar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico de para enviar scripts de treinamento que usam qualquer estrutura de aprendizado que você escolher (como Scikit-learn).

Para tarefas de PyTorch, TensorFlow e Chainer, o Azure Machine Learning também oferece os respectivos avaliadores de [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar o uso dessas estruturas.

Para obter mais informações, consulte os seguintes artigos:

* [Treinar modelos de ML com avaliadores](how-to-train-ml-models.md).
* [Treine os modelos de aprendizado profundo do Pytorch em escala com o Azure Machine Learning](how-to-train-pytorch.md).
* [Treine e registre modelos do TensorFlow em escala com o Azure Machine Learning](how-to-train-tensorflow.md).
* [Treine e registre modelos do Chainer em escala com o Azure Machine Learning](how-to-train-ml-models.md).

### <a name="endpoints"></a>Pontos de extremidade

Um ponto de extremidade é uma instanciação do modelo em um serviço Web que pode ser hospedado na nuvem ou um módulo de IoT para implantações de dispositivos integrados.

#### <a name="web-service-endpoint"></a>Ponto de extremidade de serviço Web

Na implantação de um modelo como um serviço Web, o ponto de extremidade pode ser implantado em Instâncias de Contêiner do Azure, Serviço de Kubernetes do Azure ou FPGAs. O serviço é criado a partir de um modelo, script e arquivos associados. Eles são colocados em uma imagem de contêiner de base que contém o ambiente de execução do modelo. A imagem tem um balanceamento de carga, ponto de extremidade HTTP que recebe as solicitações de pontuação enviadas para o serviço Web.

O Azure ajudará a monitorar o serviço Web por meio da coleta de telemetria do Application Insights ou da telemetria de modelo, se você tiver optado por habilitar esse recurso. Os dados de telemetria estão acessíveis apenas para você e armazenados em suas instâncias de conta de armazenamento e Application Insights.

Se você tiver habilitado o dimensionamento automático, o Azure dimensionará automaticamente sua implantação.

Para obter um exemplo de implantação de um modelo como um serviço Web, confira [Implantar um modelo de classificação de imagem nas Instâncias de Contêiner do Azure](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Pontos de extremidade do módulo de IoT

Um ponto de extremidade do módulo de IoT implantado é um contêiner do Docker que inclui seu modelo, o script ou aplicativo associado e as dependências adicionais. Esses módulos são implantados usando o Azure IoT Edge em dispositivos de borda.

Se você tiver habilitado o monitoramento, o Azure coleta dados de telemetria do modelo de dentro do módulo do Azure IoT Edge. Os dados de telemetria estão acessíveis apenas para você e armazenados em sua instância de conta de armazenamento.

O Azure IoT Edge garantirá que seu módulo esteja em execução e monitorará o dispositivo que está hospedando.


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>Instância de computação (versão prévia)

Uma **instância de computação do Azure Machine Learning** (anteriormente chamada de VM do Notebook) é uma estação de trabalho baseada em nuvem totalmente gerenciada que inclui várias ferramentas e ambientes instalados para aprendizado de máquina. As instâncias de computação podem ser usadas como um destino de computação para trabalhos de treinamento e inferência. Para tarefas grandes, os [clusters de cálculo do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com recursos de dimensionamento de vários nós são uma melhor opção de destino de computação.

Saiba mais sobre [instâncias de computação](concept-compute-instance.md).

### <a name="datasets-and-datastores"></a>Conjuntos de dados e armazenamentos de dados

Os **conjuntos de dados do Azure Machine Learning** (versão prévia) facilitam o acesso aos dados e o trabalho com eles. Os conjuntos de dados gerenciam dados em vários cenários, como treinamento de modelo e criação de pipeline. Ao usar o SDK do Azure Machine Learning, você pode acessar o armazenamento subjacente, explorar dados e gerenciar o ciclo de vida de diferentes definições do conjunto de dados.

Os conjuntos de dados fornecem métodos para trabalhar com dados em formatos conhecidos, como o uso de `from_delimited_files()` ou `to_pandas_dataframe()`.

Para obter mais informações, confira [Criar e registrar conjuntos de dados do Azure Machine Learning](how-to-create-register-datasets.md).  Para obter mais exemplos do uso de conjuntos de dados, confira os [exemplos de notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Um **armazenamento de dados** é uma abstração de armazenamento de uma conta de armazenamento do Azure. O repositório de dados pode usar um contêiner de blob do Azure ou um compartilhamento de arquivos do Azure como o armazenamento de back-end. Cada workspace tem um repositório de dados padrão e você poderá registrar repositórios de dados adicionais. Use a API do SDK do Python ou a CLI do Azure Machine Learning para armazenar e recuperar arquivos do repositório de dados.

### <a name="compute-targets"></a>Destinos de computação

Um [destino de computação](concept-compute-target.md) permite especificar o recurso de computação usado para executar o script de treinamento ou hospedar a implantação do serviço. Esse local pode ser seu computador local ou um recurso de computação baseado em nuvem.

Saiba mais sobre os [destinos de computação disponíveis para treinamento e implantação](concept-compute-target.md).

### <a name="next-steps"></a>Próximas etapas

Para começar a usar o Azure Machine Learning, confira:

* [O que é Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Criar um workspace de Azure Machine Learning](how-to-manage-workspace.md)
* [Tutorial (parte 1): Treinar um modelo](tutorial-train-models-with-aml.md)
