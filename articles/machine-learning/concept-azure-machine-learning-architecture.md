---
title: Arquitetura & conceitos-chave
titleSuffix: Azure Machine Learning
description: Conheça a arquitetura, os termos, conceitos e os fluxos de trabalho que compõem o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/17/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: f779781eee81bf85f6420e5bae6b0feb62680b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064183"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Como funciona o Azure Machine Learning: Arquitetura e conceitos

Conheça a arquitetura, os conceitos e o fluxo de trabalho do Azure Machine Learning. Os principais componentes do serviço e o fluxo de trabalho geral ao usar o serviço que são mostrados no diagrama:

![Arquitetura e fluxo de trabalho do Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Fluxo de trabalho

O fluxo de trabalho do modelo de aprendizagem de máquina geralmente segue essa seqüência:

1. **Trem**
    + Desenvolver scripts de treinamento de aprendizagem de máquina em **Python**, **R**, ou com o designer visual.
    + Criar e configurar um **destino de computação**.
    + **Envie os scripts** para um alvo de computação configurado para ser executado nesse ambiente. Durante o treinamento, os scripts podem ler ou gravar em **datastores**. Os registros e a saída produzidos durante o treinamento são salvos como **executados** no **espaço de trabalho** e agrupados em **experimentos**.

1. **Pacote** - Após uma execução satisfatória, registre o modelo persistido no **registro do modelo**.

1. **Valide** - **consultar o experimento** para métricas registradas das corridas atuais e passadas. Se as métricas não indicarem um resultado desejado, volte para a etapa 1 e itere em seus scripts.

1. **Implantar** - Desenvolver um script de pontuação que use o modelo e **implantar o modelo** como um serviço **web** no Azure, ou em um **dispositivo IoT Edge**.

1. **Monitor** - Monitor para **deriva de dados** entre o conjunto de dados de treinamento e os dados de inferência de um modelo implantado. Quando necessário, volte ao passo 1 para retreinar o modelo com novos dados de treinamento.

## <a name="tools-for-azure-machine-learning"></a>Ferramentas para aprendizado de máquina do Azure

Use essas ferramentas para o Azure Machine Learning:

+  Interaja com o serviço em qualquer ambiente Python com o [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interaja com o serviço em qualquer ambiente R com o [Azure Machine Learning SDK for R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatize suas atividades de aprendizado de máquina com a [CLI de machine learning do Azure.](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)
+ Use [o designer de machine learning do Azure (visualização)](concept-designer.md) para executar as etapas do fluxo de trabalho sem escrever código.


> [!NOTE]
> Embora este artigo defina termos e conceitos usados pelo Azure Machine Learning, ele não define termos e conceitos para a plataforma Azure. Para obter mais informações sobre a terminologia da plataforma do Azure, consulte o [Glossário do Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Glossário

* [Atividade](#activities)
* [Espaço](#workspaces)
    * [Testes](#experiments)
        * [Executar](#runs) 
            * [Configuração de execução](#run-configurations)
            * [Instantâneo](#snapshots)
            * [Rastreamento git](#github-tracking-and-integration)
            * [Registrando em log](#logging)
    * [Gasodutos ML](#ml-pipelines)
    * [Modelos](#models)
        * [Ambientes](#environments)
        * [Script de treinamento](#training-scripts)
        * [Estimadores](#estimators)
    * [Extremidade](#endpoints)
        * [Serviço Web](#web-service-endpoint)
        * [Módulos IoT](#iot-module-endpoints)
    * [Conjunto de dados & datastores](#datasets-and-datastores)
    * [Metas de computação](#compute-targets)

### <a name="activities"></a>Atividades

Uma atividade representa uma operação de execução demorada. As operações a seguir estão exemplos de atividades:

* Criar ou excluir um destino de computação
* Executar um script em um destino de computação

As atividades podem fornecer notificações por meio do SDK ou da IU da Web, portanto, você pode facilmente monitorar o progresso dessas operações.

### <a name="workspaces"></a>Workspaces

[O espaço de trabalho](concept-workspace.md) é o recurso de alto nível para o Azure Machine Learning. Ele fornece um lugar centralizado para trabalhar com todos os artefatos que você cria quando você usa o Azure Machine Learning. Você pode compartilhar um espaço de trabalho com outros. Para obter uma descrição detalhada dos espaços de trabalho, [consulte O que é um espaço de trabalho azure Machine Learning?](concept-workspace.md).

### <a name="experiments"></a>Testes

Um experimento é um agrupamento de diversas execuções de um determinado script. Ele sempre pertence a um workspace. Quando você envia uma execução, você pode fornecer um nome de experimento. As informações para a execução são armazenadas nesse experimento. Se você enviar uma execução e especificar um nome de experimento que não existe, um novo experimento com esse nome recém-especificado é criado automaticamente.

Para um exemplo de uso de um experimento, consulte [Tutorial: Treine seu primeiro modelo](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Execuções

Uma corrida é uma única execução de um roteiro de treinamento. Um experimento normalmente contém várias corridas.

O Azure Machine Learning registra todas as corridas e armazena as seguintes informações no experimento:

* Metadados sobre a execução (carimbo de hora, duração, etc.)
* Métricas registradas pelo seu script
* Arquivos de saída coletados automaticamente pelo experimento ou carregados explicitamente por você
* Um instantâneo do diretório que contém seus scripts, antes da execução

Uma execução é produzida quando você envia um script para fazer o treinamento de um modelo. Uma execução pode ter zero ou mais execuções filho. Por exemplo, a execução de nível superior pode ter duas execuções filho, cada uma delas pode ter sua próprias execuções filho.

### <a name="run-configurations"></a>Executar configurações

Uma configuração de execução é um conjunto de instruções que define como um script deve ser executado em um destino de computação especificado. Ela inclui um amplo conjunto de definições de comportamento, como se deseja usar um ambiente de Python existente ou usar um ambiente de Conda criado a partir de uma especificação.

Uma configuração de execução pode ser persistida em um arquivo dentro do diretório que contém o script de treinamento ou construída como um objeto na memória e usada para enviar uma execução.

Para obter um exemplo das configurações de execução, consulte [Selecionar e usar um destino de computação para fazer o treinamento do seu modelo](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Instantâneos

Ao enviar uma execução, o Azure Machine Learning compacta o diretório que contém o script como um arquivo zip e o envia para o destino de computação. O arquivo zip é expandido e o script é executado lá. O Azure Machine Learning também armazena o arquivo zip como um instantâneo como parte do registro de execução. Qualquer pessoa com acesso ao workspace pode procurar um registro de execução e baixar o instantâneo.

> [!NOTE]
> Para evitar que arquivos desnecessários sejam incluídos no snapshot, faça um arquivo ignorar (.gitignore ou .amlignore). Coloque este arquivo no diretório Snapshot e adicione os nomes de arquivo para ignorar nele. O arquivo .amlignore usa a mesma [sintaxe e padrões do arquivo .gitignore](https://git-scm.com/docs/gitignore). Se ambos os arquivos existirem, o arquivo .amlignore tem precedência.

### <a name="github-tracking-and-integration"></a>Rastreamento e integração do GitHub

Quando você inicia uma corrida de treinamento onde o diretório de origem é um repositório git local, as informações sobre o repositório são armazenadas no histórico de execução. Isso funciona com executões enviados usando um estimador, pipeline ML ou script run. Ele também funciona para corridas enviadas do SDK ou Machine Learning CLI.

Para obter mais informações, consulte [a integração Git para Aprendizado de Máquina do Azure](concept-train-model-git-integration.md).

### <a name="logging"></a>Registrando em log

Ao desenvolver sua solução, use o SDK do Python do Azure Machine Learning em seu script de Python para registrar métricas arbitrárias. Após a execução, consulte as métricas para determinar se a execução produziu o modelo que você deseja implantar.

### <a name="ml-pipelines"></a>Pipelines de ML

Os pipelines de aprendizado de máquina são usados para criar e gerenciar fluxos de trabalho que unem as fases de aprendizado da máquina. Por exemplo, um pipeline pode incluir preparação de dados, treinamento de modelo, implantação de modelos e fases de inferência/pontuação. Cada fase pode incluir várias etapas, cada uma delas pode ser executada de modo autônomo em vários destinos de computação. 

As etapas do pipeline são reutilizáveis e podem ser executadas sem refazer as etapas anteriores se a saída dessas etapas não tiver sido alterada. Por exemplo, você pode retreinar um modelo sem reexecutar etapas caras de preparação de dados se os dados não forem alterados. Os pipelines também permitem que os cientistas de dados colaborem enquanto trabalham em áreas separadas de um fluxo de trabalho de aprendizado de máquina.

Para obter mais informações sobre os pipelines de aprendizado de máquina com esse serviço, consulte [Pipelines e Azure Machine Learning](concept-ml-pipelines.md).

### <a name="models"></a>Modelos

Em sua forma mais simples, um modelo é um trecho de código que usa uma entrada e produz uma saída. Criar um modelo de aprendizado de máquina envolve selecionar um algoritmo, fornecer dados a ele e ajustar hiperparâmetros. O treinamento é um processo iterativo que produz um modelo treinado, que encapsula o modelo aprendido durante o processo de treinamento.

Um modelo é produzido por uma execução no Azure Machine Learning. Você também pode usar um modelo de treinamento fora do Azure Machine Learning. Você pode registrar um modelo em um espaço de trabalho de Aprendizado de Máquina do Azure.

O Azure Machine Learning é independente do framework. Quando você cria um modelo, você pode usar qualquer estrutura popular de aprendizado de máquina, como Scikit-learn, XGBoost, PyTorch, TensorFlow e Chainer.

Para um exemplo de treinamento de um modelo usando scikit-learn e um estimador, consulte Tutorial: Treine um modelo de classificação de [imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

O **registro de modelos** acompanha todos os modelos em seu espaço de trabalho Azure Machine Learning.

Os modelos são identificados por nome e versão. Cada vez que você registra um modelo com o mesmo nome de um já existente, o registro pressupõe que se trata de uma nova versão. A versão é incrementada e o novo modelo é registrado com o mesmo nome.

Você pode fornecer as marcas de metadados adicionais quando registrar o modelo e, em seguida, usar essas marcas ao procurar pelos modelos.

> [!TIP]
> Um modelo registrado é um recipiente lógico para um ou mais arquivos que compõem o seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, você pode registrá-los como um único modelo no seu espaço de trabalho Azure Machine Learning. Após o registro, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que foram registrados.

Você não pode excluir um modelo registrado que está sendo usado por uma implantação ativa.

Para obter um exemplo de registro de um modelo, consulte [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="environments"></a>Ambientes

Os ambientes Azure ML são usados para especificar a configuração (Docker / Python / Spark / etc.) usada para criar um ambiente reprodutível para preparação de dados, treinamento de modelo e serviço de modelo. Eles são entidades gerenciadas e versadas dentro do seu espaço de trabalho Azure Machine Learning que permitem fluxos de trabalho reprodutíveis, auditáveis e portáteis de aprendizado de máquina em diferentes alvos de computação.

Você pode usar um objeto de ambiente em sua computação local para desenvolver seu script de treinamento, reutilizar o mesmo ambiente no Azure Machine Learning Compute para treinamento de modelo em escala e até mesmo implantar seu modelo com esse mesmo ambiente. 

Aprenda [a criar e gerenciar um ambiente ML reutilizável](how-to-use-environments.md) para treinamento e inferência.

### <a name="training-scripts"></a>Scripts de treinamento

Para treinar um modelo, você deve especificar o diretório que contém o script de treinamento e os arquivos associados. Você também pode especificar um nome de experimento, que é usado para armazenar as informações obtidas durante o treinamento. Durante o treinamento, o diretório inteiro é copiado para o ambiente de treinamento (destino de computação) e o script especificado pela configuração de execução é iniciado. Um instantâneo do diretório também é armazenado no experimento no workspace.

Por exemplo, consulte [Tutorial: Treine um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="estimators"></a>Estimadores

Para facilitar o treinamento do modelo com frameworks populares, a classe estimador permite construir facilmente configurações de execução. Você pode criar e usar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para enviar scripts de treinamento que usam qualquer estrutura de aprendizado que você escolher (como scikit-learn).

Para as tarefas PyTorch, TensorFlow e Chainer, o Azure Machine Learning também fornece os respectivos estimadores [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)e [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar o uso dessas frameworks.

Para obter mais informações, consulte os seguintes artigos:

* [Treine modelos ML com estimadores](how-to-train-ml-models.md).
* [Treine modelos de aprendizagem profunda pytorch em escala com Azure Machine Learning](how-to-train-pytorch.md).
* [Treine e registre modelos TensorFlow em escala com o Azure Machine Learning](how-to-train-tensorflow.md).
* [Treinar e registrar modelos Chainer em escala com Azure Machine Learning](how-to-train-chainer.md).

### <a name="endpoints"></a>Pontos de extremidade

Um ponto final é uma instanciação do seu modelo em um serviço web que pode ser hospedado na nuvem ou um módulo IoT para implantações integradas de dispositivos.

#### <a name="web-service-endpoint"></a>Ponto final do serviço web

Ao implantar um modelo como serviço web, o ponto final pode ser implantado em Instâncias de Contêiner Do Zure, Serviço Azure Kubernetes ou FPGAs. Você cria o serviço a partir de seu modelo, script e arquivos associados. Estes são colocados em uma imagem de recipiente base que contém o ambiente de execução do modelo. A imagem tem um balanceamento de carga, ponto de extremidade HTTP que recebe as solicitações de pontuação enviadas para o serviço Web.

O Azure ajuda você a monitorar seu serviço web coletando telemetria do Application Insights ou telemetria de modelo, se você escolheu habilitar esse recurso. Os dados de telemetria estão acessíveis apenas para você e armazenados em suas instâncias de conta de armazenamento e Application Insights.

Se você tiver habilitado o dimensionamento automático, o Azure dimensionará automaticamente sua implantação.

Para um exemplo de implantação de um modelo como serviço web, consulte Implantar um modelo de [classificação de imagem em Instâncias de Contêiner Azure](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Pontos finais do módulo IoT

Um ponto final de módulo IoT implantado é um contêiner Docker que inclui seu modelo e script ou aplicativo associados e quaisquer dependências adicionais. Você implanta esses módulos usando o Azure IoT Edge em dispositivos de borda.

Se você tiver habilitado o monitoramento, o Azure coleta dados de telemetria do modelo de dentro do módulo do Azure IoT Edge. Os dados de telemetria estão acessíveis apenas para você e armazenados em sua instância de conta de armazenamento.

O Azure IoT Edge garantirá que seu módulo esteja em execução e monitorará o dispositivo que está hospedando.


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>Instância de cálculo (visualização)

Uma instância de computação de aprendizado de **máquina do Azure** (anteriormente Notebook VM) é uma estação de trabalho totalmente gerenciada baseada em nuvem que inclui várias ferramentas e ambientes instalados para aprendizado de máquina. As instâncias computacionais podem ser usadas como um alvo computacional para trabalhos de treinamento e inferência. Para tarefas grandes, [os clusters de computação do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) com recursos de dimensionamento de vários nós são uma melhor escolha de destino computacional.

Saiba mais sobre [as instâncias computacionais](concept-compute-instance.md).

### <a name="datasets-and-datastores"></a>Conjuntos de dados e datastores

Os conjuntos de dados de aprendizagem de máquina do **Azure** (visualização) facilitam o acesso e o trabalho com seus dados. Conjuntos de dados gerenciam dados em vários cenários, como treinamento de modelos e criação de pipeline. Usando o Azure Machine Learning SDK, você pode acessar o armazenamento subjacente, explorar dados e gerenciar o ciclo de vida de diferentes definições de dataset.

Os conjuntos de dados fornecem métodos para trabalhar `from_delimited_files()` `to_pandas_dataframe()`com dados em formatos populares, como o uso ou .

Para obter mais informações, consulte [Criar e registrar conjuntos de dados de aprendizado de máquina do Azure](how-to-create-register-datasets.md).  Para obter mais exemplos usando conjuntos de dados, consulte os [cadernos de amostra](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets).

Um **datastore** é uma abstração de armazenamento sobre uma conta de armazenamento DoZure. O repositório de dados pode usar um contêiner de blob do Azure ou um compartilhamento de arquivos do Azure como o armazenamento de back-end. Cada workspace tem um repositório de dados padrão e você poderá registrar repositórios de dados adicionais. Use a API do SDK do Python ou a CLI do Azure Machine Learning para armazenar e recuperar arquivos do repositório de dados.

### <a name="compute-targets"></a>Destinos de computação

Um [destino de computação](concept-compute-target.md) permite especificar o recurso de computação onde você executa seu script de treinamento ou hospeda sua implantação de serviço. Este local pode ser sua máquina local ou um recurso de computação baseado em nuvem.

Saiba mais sobre as [metas de computação disponíveis para treinamento e implantação.](concept-compute-target.md)

### <a name="next-steps"></a>Próximas etapas

Para começar com o Azure Machine Learning, veja:

* [O que é Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Crie um espaço de trabalho de aprendizado de máquina do Azure](how-to-manage-workspace.md)
* [Tutorial (parte 1): Treine um modelo](tutorial-train-models-with-aml.md)
