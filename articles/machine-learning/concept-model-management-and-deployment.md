---
title: 'MLOps: Gerenciamento de modelos ML'
titleSuffix: Azure Machine Learning
description: 'Aprenda sobre gerenciamento de modelos com MLOps (Azure Machine Learning). Implantar, gerenciar e monitorar seus modelos para melhorá-los continuamente. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 7857d11c625911cd1b49dfcf0e0d612fc6a3871e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314303"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: Gerenciamento de modelos, implantação e monitoramento com o Azure Machine Learning

Neste artigo, saiba como usar o Azure Machine Learning para gerenciar o ciclo de vida de seus modelos. O Azure Machine Learning usa uma abordagem MLOps (Machine Learning Operations, operações de aprendizagem de máquina). O MLOps melhora a qualidade e a consistência de suas soluções de aprendizado de máquina. 

## <a name="what-is-mlops"></a>O que é MLOps?

As Operações de Aprendizado de Máquina (MLOps) são baseadas em princípios e práticas [de DevOps](https://azure.microsoft.com/overview/what-is-devops/) que aumentam a eficiência dos fluxos de trabalho. Por exemplo, integração contínua, entrega e implantação. O MLOps aplica esses princípios ao processo de aprendizagem de máquina, com o objetivo de:

* Experimentação e desenvolvimento mais rápidos de modelos
* Implantação mais rápida de modelos na produção
* Garantia de qualidade

O Azure Machine Learning fornece os seguintes recursos de MLOps:

- **Criar gasodutos ML reprodutíveis**. Os pipelines de Aprendizado de Máquina permitem definir etapas repetíveis e reutilizáveis para seus processos de preparação, treinamento e pontuação de dados.
- **Crie ambientes de software reutilizáveis** para treinamento e implantação de modelos.
- **Registre, empacote e implante modelos de qualquer lugar.** Você também pode rastrear metadados associados necessários para usar o modelo.
- **Capture os dados de governança para o ciclo de vida ml de ponta a ponta**. As informações registradas podem incluir quem está publicando modelos, por que as alterações foram feitas e quando os modelos foram implantados ou usados na produção.
- **Notifique e alerte sobre eventos no ciclo de vida ML**. Por exemplo, conclusão de experimentos, registro de modelos, implantação de modelos e detecção de deriva de dados.
- **Monitore os aplicativos ML para problemas operacionais e relacionados ao ML.** Compare as entradas do modelo entre treinamento e inferência, explore métricas específicas do modelo e forneça monitoramento e alertas sobre sua infra-estrutura ML.
- **Automatize o ciclo de vida ml de ponta a ponta com o Azure Machine Learning e o Azure Pipelines**. O uso de pipelines permite atualizar modelos com freqüência, testar novos modelos e lançar continuamente novos modelos ML ao lado de seus outros aplicativos e serviços.

## <a name="create-reproducible-ml-pipelines"></a>Criar gasodutos ML reprodutíveis

Use os gasodutos ML do Azure Machine Learning para costurar todas as etapas envolvidas no seu processo de treinamento de modelo.

Um pipeline ML pode conter etapas desde a preparação dos dados até a extração de recursos até a sintonia do hiperparâmetro até a avaliação do modelo. Para obter mais informações, consulte [os gasodutos ML](concept-ml-pipelines.md).

Se você usar o [Designer](concept-designer.md) para criar seus pipelines ML, você pode a qualquer momento clicar no **"..."** no canto superior direito da página Do Designer e, em seguida, selecionar **Clone**. A clonagem do seu pipeline permite que você itera o design do pipeline sem perder suas versões antigas.  

## <a name="create-reusable-software-environments"></a>Crie ambientes de software reutilizáveis

Os ambientes de Machine Learning do Azure permitem que você rastreie e reproduza as dependências de software de seus projetos à medida que eles evoluem. Os ambientes permitem garantir que as compilações sejam reprodutíveis sem configurações manuais de software.

Ambientes descrevem as dependências pip e Conda para seus projetos, e podem ser usados tanto para treinamento quanto para implantação de modelos. Para obter mais informações, consulte [quais são os ambientes de Machine Learning do Azure](concept-environments.md).

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registre, empacote e implante modelos de qualquer lugar

### <a name="register-and-track-ml-models"></a>Registre-se e rastreie modelos ML

O registro de modelo permite que você armazene e controle a versão de seus modelos na nuvem do Azure em seu workspace. O registro de modelo torna mais fácil organizar e manter o controle de seus modelos treinados.

> [!TIP]
> Um modelo registrado é um recipiente lógico para um ou mais arquivos que compõem o seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, você pode registrá-los como um único modelo no seu espaço de trabalho Azure Machine Learning. Após o registro, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que foram registrados.

Modelos registrados são identificados por nome e versão. Cada vez que você registra um modelo com o mesmo nome de um já existente, o Registro incrementa a versão. Tags de metadados adicionais podem ser fornecidas durante o registro. Essas tags são então usadas ao procurar um modelo. O Azure Machine Learning suporta qualquer modelo que possa ser carregado usando Python 3.5.2 ou superior.

> [!TIP]
> Você também pode registrar modelos treinados fora do Azure Machine Learning.

Você não pode excluir um modelo registrado que está sendo usado em uma implantação ativa.
Para obter mais informações, consulte a seção sobre registro de modelos em [Implantar modelos](how-to-deploy-and-where.md#registermodel).

### <a name="profile-models"></a>Modelos de perfil

O Azure Machine Learning pode ajudá-lo a entender os requisitos de CPU e memória do serviço que serão criados quando você implantar seu modelo. O perfil testa o serviço que executa seu modelo e retorna informações como o uso da CPU, o uso da memória e a latência de resposta. Ele também fornece uma recomendação de CPU e memória com base no uso de recursos.
Para obter mais informações, consulte a seção de criação de perfil dos [modelos Deploy](how-to-deploy-and-where.md#profilemodel).

### <a name="package-and-debug-models"></a>Modelos de pacotes e depurações

Antes de implantar um modelo em produção, ele é embalado em uma imagem Docker. Na maioria dos casos, a criação de imagens acontece automaticamente em segundo plano durante a implantação. Você pode especificar manualmente a imagem.

Se você encontrar problemas com a implantação, você pode implantar em seu ambiente de desenvolvimento local para solução de problemas e depuração.

Para obter mais informações, consulte [Implantar modelos](how-to-deploy-and-where.md#registermodel) e [implantações de solução de problemas](how-to-troubleshoot-deployment.md).

### <a name="convert-and-optimize-models"></a>Converter e otimizar modelos

Converter seu modelo para [open neural network exchange](https://onnx.ai) (ONNX) pode melhorar o desempenho. Em média, a conversão para ONNX pode render um aumento de desempenho de 2x.

Para obter mais informações sobre o ONNX com o Azure Machine Learning, consulte o [artigo Criar e acelerar os modelos ML.](concept-onnx.md)

### <a name="use-models"></a>Usar modelos

Modelos de aprendizado de máquina treinados são implantados como serviços web na nuvem ou localmente. Você também pode implantar modelos em dispositivos Azure IoT Edge. As implantações usam cpu, GPU ou matrizes de portão programáveis em campo (FPGA) para inferência. Você também pode usar modelos do Power BI.

Ao usar um modelo como um serviço web ou dispositivo IoT Edge, você fornece os seguintes itens:

* Os modelos que são usados para pontuar dados submetidos ao serviço/dispositivo.
* Um script de entrada. Esse script aceita solicitações, usa os modelos para marcar os dados e retorna uma resposta.
* Um ambiente de aprendizado de máquina do Azure que descreve as dependências pip e Conda exigidas pelos modelos e script de entrada.
* Quaisquer ativos adicionais, como texto, dados, etc. que são exigidos pelo modelo e script de entrada.

Você também fornece a configuração da plataforma de implantação de destino. Por exemplo, o tipo de família VM, a memória disponível e o número de núcleos ao ser implantado no Azure Kubernetes Service.

Quando a imagem é criada, os componentes exigidos pelo Azure Machine Learning também são adicionados. Por exemplo, os ativos precisavam executar o serviço web e interagir com o IoT Edge.

#### <a name="batch-scoring"></a>Pontuação do lote
A pontuação em lote é suportada através de pipelines ML. Para obter mais informações, consulte [previsões em lote sobre big data](how-to-use-parallel-run-step.md).

#### <a name="real-time-web-services"></a>Serviços web em tempo real

Você pode usar seus modelos em **serviços web** com os seguintes alvos de computação:

* Azure Container Instance
* Serviço de Kubernetes do Azure
* Ambiente de desenvolvimento local

Para implantar o modelo como um serviço web, você deve fornecer os seguintes itens:

* O modelo ou conjunto de modelos.
* Dependências necessárias para usar o modelo. Por exemplo, um script que aceita solicitações e invoca o modelo, dependências de conda, etc.
* Configuração de implantação que descreve como e onde implantar o modelo.

Para obter mais informações, consulte [Implantar modelos](how-to-deploy-and-where.md).

#### <a name="controlled-rollout"></a>Implantação controlada

Ao implantar no Azure Kubernetes Service, você pode usar a implantação controlada para habilitar os seguintes cenários:

* Crie várias versões de um ponto final para uma implantação
* Execute os testes A/B roteando o tráfego para diferentes versões do ponto final.
* Alternar entre versões de ponto final atualizando a porcentagem de tráfego na configuração de ponto final.

Para obter mais informações, consulte [A distribuição controlada de modelos ML](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview).

#### <a name="iot-edge-devices"></a>Dispositivos IoT Edge

Você pode usar modelos com dispositivos IoT através **de módulos Azure IoT Edge**. Os módulos IoT Edge são implantados em um dispositivo de hardware, o que permite inferência, ou pontuação do modelo, no dispositivo.

Para obter mais informações, consulte [Implantar modelos](how-to-deploy-and-where.md).

### <a name="analytics"></a>Análise

O Microsoft Power BI suporta o uso de modelos de aprendizado de máquina para análise de dados. Para obter mais informações, consulte [a integração azure Machine Learning no Power BI (preview)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Capture os dados de governança necessários para capturar o ciclo de vida ml de ponta a ponta

O Azure ML oferece a você a capacidade de rastrear a trilha de auditoria de ponta a ponta de todos os seus ativos ML usando metadados.

- O Azure ML [integra-se ao Git](how-to-set-up-training-targets.md#gitintegration) para rastrear informações sobre de qual repositório / ramo / cometer seu código veio.
- [Os conjuntos de dados do Azure ML](how-to-create-register-datasets.md) ajudam a rastrear, perfilar e ver os dados da versão.
- [A interpretabilidade](how-to-machine-learning-interpretability.md) permite que você explique seus modelos, atenda à conformidade normativa e entenda como os modelos chegam a um resultado para determinada entrada.
- O histórico do Azure ML Run armazena um instantâneo do código, dados e cálculos usados para treinar um modelo.
- O Registro de Modelos Do Azure ML captura todos os metadados associados ao seu modelo (que experimento o treinou, onde está sendo implantado, se suas implantações forem saudáveis).
- [A integração com o Azure Event Grid](concept-event-grid-integration.md) permite que você atue em eventos no ciclo de vida ml. Por exemplo, registro de modelos, implantação, deriva de dados e eventos de treinamento (run).

> [!TIP]
> Embora algumas informações sobre modelos e conjuntos de dados sejam capturadas automaticamente, você pode adicionar informações adicionais usando __tags__. Ao procurar modelos e conjuntos de dados registrados em seu espaço de trabalho, você pode usar tags como filtro.
>
> Associar um conjunto de dados a um modelo registrado é um passo opcional. Para obter informações sobre como fazer referência a um conjunto de dados ao registrar um modelo, consulte a referência de classe [Modelo.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py)


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Notifique, automatize e alerte sobre eventos no ciclo de vida ml
O Azure ML publica os principais eventos do Azure EventGrid, que podem ser usados para notificar e automatizar eventos no ciclo de vida ml. Para obter mais informações, consulte [este documento](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Monitorpara problemas operacionais & ML

O monitoramento permite que você entenda quais dados estão sendo enviados para o seu modelo e as previsões que ele retorna.

Essas informações ajudam você a entender como seu modelo está sendo usado. Os dados de entrada coletados também podem ser úteis no treinamento de versões futuras do modelo.

Para obter mais informações, consulte [Como habilitar a coleta de dados de modelo](how-to-enable-data-collection.md).

## <a name="retrain-your-model-on-new-data"></a>Retreinar seu modelo em novos dados

Muitas vezes, você vai querer validar seu modelo, atualizá-lo ou até mesmo retreiná-lo do zero, à medida que você recebe novas informações. Às vezes, receber novos dados é uma parte esperada do domínio. Outras vezes, como discutido no [Detect data drift (visualização) em conjuntos](how-to-monitor-datasets.md)de dados, o desempenho do modelo pode degradar-se diante de coisas como alterações em um determinado sensor, mudanças naturais de dados, como efeitos sazonais ou mudanças de recursos em sua relação com outras características. 

Não há uma resposta universal para "Como sei se devo treinar novamente?" mas as ferramentas de eventos e monitoramento do Azure ML discutidas anteriormente são bons pontos de partida para automação. Uma vez que você decidiu retreinar, você deve: 

- Pré-processe seus dados usando um processo automatizado e repetível
- Treine seu novo modelo
- Compare as saídas do seu novo modelo com as do seu modelo antigo
- Use critérios predefinidos para escolher se quer substituir seu modelo antigo 

Um tema das etapas acima é que seu retreinamento deve ser automatizado, não ad hoc. [Os pipelines de Machine Learning do Azure](concept-ml-pipelines.md) são uma boa resposta para a criação de fluxos de trabalho relacionados à preparação, treinamento, validação e implantação de dados. Leia [modelos de retreinamento com o designer azure Machine Learning (preview)](how-to-retrain-designer.md) para ver como os pipelines e o designer de Machine Learning do Azure se encaixam em um cenário de retreinamento. 

## <a name="automate-the-ml-lifecycle"></a>Automatize o ciclo de vida ml 

Você pode usar o GitHub e o Azure Pipelines para criar um processo de integração contínua que treina um modelo. Em um cenário típico, quando um Cientista de Dados verifica uma alteração no repo do Git para um projeto, o Azure Pipeline iniciará uma corrida de treinamento. Os resultados da corrida podem então ser inspecionados para ver as características de desempenho do modelo treinado. Você também pode criar um pipeline que implanta o modelo como um serviço web.

A [extensão Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) facilita o trabalho com a Azure Pipelines. Ele fornece os seguintes aprimoramentos para o Azure Pipelines:

* Habilita a seleção do espaço de trabalho ao definir uma conexão de serviço.
* Permite que os gasodutos de liberação sejam acionados por modelos treinados criados em um pipeline de treinamento.

Para obter mais informações sobre como usar pipelines Azure com o Azure Machine Learning, consulte os seguintes links:

* [Integração contínua e implantação de modelos ML com a azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Repositório MLOps de aprendizado de máquina do Azure.](https://aka.ms/mlops)
* [Repositório MLOpsPython de aprendizado de máquina do Azure.](https://github.com/Microsoft/MLOpspython)

Você também pode usar a Fábrica de Dados Do Azure para criar um pipeline de ingestão de dados que prepara dados para uso com treinamento. Para obter mais informações, consulte [o pipeline de ingestão de dados](how-to-cicd-data-ingestion.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais lendo e explorando os seguintes recursos:

+ [Como & onde implantar modelos](how-to-deploy-and-where.md) com o Azure Machine Learning

+ [Tutorial: Implante um modelo de classificação de imagem na ACI](tutorial-deploy-models-with-aml.md).

+ [Exemplos de MLOps de ponta a ponta](https://github.com/microsoft/MLOps)

+ [CI/CD de modelos ML com Pipelines Azure](/azure/devops/pipelines/targets/azure-machine-learning)

+ Crie clientes que [consomem um modelo implantado](how-to-consume-web-service.md)

+ [Aprendizado de máquina em escala](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Arquiteturas de referência da IA do Azure & representante de práticas recomendadas](https://github.com/microsoft/AI)
