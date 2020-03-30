---
title: O que é um espaço de trabalho
titleSuffix: Azure Machine Learning
description: O espaço de trabalho é o recurso de alto nível para o Azure Machine Learning. Ele mantém um histórico de todas as corridas de treinamento, incluindo logs, métricas, saída e um instantâneo de seus scripts. Você usa essas informações para determinar qual corrida de treinamento produz o melhor modelo
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: 03bc49c24e3c2d32e97f3e5e03bd39da63428a6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505566"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>O que é um espaço de trabalho de Machine Learning do Azure?

O espaço de trabalho é o recurso de alto nível para o Azure Machine Learning, fornecendo um lugar centralizado para trabalhar com todos os artefatos que você cria quando você usa o Azure Machine Learning.  O espaço de trabalho mantém um histórico de todas as corridas de treinamento, incluindo logs, métricas, saída e um instantâneo de seus scripts. Essas informações são usadas para determinar quais execuções de treinamento produzem o melhor modelo.  

Uma vez que você tem um modelo que você gosta, você registrá-lo com o espaço de trabalho. Em seguida, você usa o modelo registrado e os scripts de pontuação para implantar no Azure Container Instances, no Azure Kubernetes Service ou em um conjunto de portão programável de campo (FPGA) como um ponto final HTTP baseado em REST. Você também pode implantar o modelo em um dispositivo Azure IoT Edge como um módulo.

Os preços e recursos disponíveis dependem se [a edição Básica ou Enterprise](overview-what-is-azure-ml.md#sku) é selecionada para o espaço de trabalho. Você seleciona a edição ao [criar o espaço de trabalho](#create-workspace).  Você também pode [atualizar](#upgrade) da edição Básica para a Enterprise.

## <a name="taxonomy"></a>Taxonomia 

O diagrama a seguir é uma taxonomia do workspace:

[![Taxonomia do espaço de trabalho](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

O diagrama mostra os seguintes componentes de um espaço de trabalho:

+ Um espaço de trabalho pode conter [instâncias computacionais do Azure Machine Learning,](concept-compute-instance.md)recursos em nuvem configurados com o ambiente Python necessário para executar o Azure Machine Learning.

+ [As funções do usuário](how-to-assign-roles.md) permitem que você compartilhe seu espaço de trabalho com outros usuários, equipes ou projetos.
+ [Alvos computacionais](concept-azure-machine-learning-architecture.md#compute-targets) são usados para executar seus experimentos.
+ Quando você cria o espaço de trabalho, [os recursos associados](#resources) também são criados para você.
+ [Experimentos](concept-azure-machine-learning-architecture.md#experiments) são corridas de treinamento que você usa para construir seus modelos.  
+ [Os gasodutos](concept-azure-machine-learning-architecture.md#ml-pipelines) são fluxos de trabalho reutilizáveis para treinamento e retreinamento do seu modelo.
+ [Conjuntos de dados](concept-azure-machine-learning-architecture.md#datasets-and-datastores) auxiliam na gestão dos dados que você usa para treinamento de modelos e criação de gasodutos.
+ Uma vez que você tem um modelo que deseja implantar, você cria um modelo registrado.
+ Use o modelo registrado e um script de pontuação para criar um [ponto final de implantação](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Ferramentas para interação no espaço de trabalho

Você pode interagir com seu espaço de trabalho das seguintes maneiras:

+ Na web:
    + [Estúdio de Aprendizado de Máquina Azure](https://ml.azure.com) 
    + [Designer de Machine Learning do Azure (pré-visualização)](concept-designer.md) - Disponível apenas em espaços de trabalho [da edição Enterprise.](overview-what-is-azure-ml.md#sku)
+ Em qualquer ambiente Python com o [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Em qualquer ambiente R com o [Azure Machine Learning SDK para R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Na linha de comando usando a [extensão CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) de aprendizado de máquina do Azure

## <a name="machine-learning-with-a-workspace"></a>Aprendizado de máquina com espaço de trabalho

Tarefas de aprendizado de máquina lêem e/ou escrevem artefatos em seu espaço de trabalho.

+ Executar um experimento para treinar um modelo - escreve resultados de execução de experimentos para o espaço de trabalho.
+ Use ML automatizado para treinar um modelo - escreve resultados de treinamento para o espaço de trabalho.
+ Registre um modelo no espaço de trabalho.
+ Implantar um modelo - usa o modelo registrado para criar uma implantação.
+ Crie e execute fluxos de trabalho reutilizáveis.
+ Veja artefatos de aprendizagem de máquina, como experimentos, dutos, modelos, implantações.
+ Rastrear e monitorar modelos.

## <a name="workspace-management"></a>Gerenciamento de espaço de trabalho

Você também pode executar as seguintes tarefas de gerenciamento de espaço de trabalho:

| Tarefa de gerenciamento de espaço de trabalho   | Portal              | Estúdio | Python SDK / R SDK       | CLI        |
|---------------------------|---------|---------|------------|------------|
| Criar um workspace        | **&check;**     | | **&check;** | **&check;** |
| Gerenciar o acesso ao espaço de trabalho    | **&check;**   || |  **&check;**    |
| Upgrade para edição Enterprise    | **&check;** | **&check;**  | |     |
| Criar e gerenciar recursos computacionais    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Criar um Notebook VM |   | **&check;** | |     |

> [!WARNING]
> A mudança do espaço de trabalho do Azure Machine Learning para uma assinatura diferente, ou a mudança da assinatura própria para um novo inquilino, não é suportada. Fazer isso pode causar erros.

## <a name="create-a-workspace"></a><a name='create-workspace'></a>Crie um espaço de trabalho

Quando você cria um espaço de trabalho, você decide se deve criá-lo com [edição Básica ou Enterprise](overview-what-is-azure-ml.md#sku). A edição determina os recursos disponíveis no espaço de trabalho. Entre outros recursos, a edição Enterprise dá acesso ao [designer azure Machine Learning](concept-designer.md) e à versão de estúdio da construção de [experimentos automatizados](tutorial-first-experiment-automated-ml.md)de aprendizado de máquina.  Para obter mais detalhes e informações sobre preços, consulte [os preços do Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Existem várias maneiras de criar um espaço de trabalho:  

* Use o [portal Azure](how-to-manage-workspace.md) para uma interface de ponto e clique para levá-lo através de cada etapa.
* Use o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) para criar um espaço de trabalho na mosca a partir de scripts Python ou notebooks Júpiter
* Use um [modelo do Azure Resource Manager](how-to-create-workspace-template.md) ou o CLI de aprendizado de máquina do [Azure](reference-azure-machine-learning-cli.md) quando precisar automatizar ou personalizar a criação com padrões de segurança corporativa.
* Se você trabalha no Visual Studio Code, use a [extensão VS Code](tutorial-setup-vscode-extension.md).

> [!NOTE]
> O nome do espaço de trabalho é insensível ao caso.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Upgrade para edição Enterprise

Você pode [atualizar seu espaço de trabalho da edição Básica para a Enterprise](how-to-manage-workspace.md#upgrade) usando o portal Azure. Não é possível fazer o downgrade de um espaço de trabalho de edição Enterprise para um espaço de trabalho de edição básica. 

## <a name="associated-resources"></a><a name="resources"></a>Recursos associados

Quando você cria um novo workspace, ele automaticamente cria vários recursos do Azure que são usados pelo workspace:

+ [Registro de contêineres do Azure](https://azure.microsoft.com/services/container-registry/): Registra recipientes docker que você usa durante o treinamento e quando você implanta um modelo. Para minimizar os custos, o ACR é **carregado com preguiça** até que as imagens de implantação sejam criadas.
+ [Conta de armazenamento azure](https://azure.microsoft.com/services/storage/): É usada como o armazenamento de dados padrão para o espaço de trabalho.  Os notebooks Jupyter que são usados com suas instâncias de cálculo do Azure Machine Learning também são armazenados aqui.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Armazena informações de monitoramento sobre seus modelos.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Armazena segredos que são usados por alvos de computação e outras informações confidenciais que são necessárias pelo espaço de trabalho.

> [!NOTE]
> Além de criar novas versões, você também pode usar os serviços existentes do Azure.

## <a name="next-steps"></a>Próximas etapas

Para começar com o Azure Machine Learning, veja:

+ [Visão geral do Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Crie um espaço de trabalho](how-to-manage-workspace.md)
+ [Gerencie um espaço de trabalho](how-to-manage-workspace.md)
+ [Tutorial: Comece a criar seu primeiro experimento ML com o Python SDK](tutorial-1st-experiment-sdk-setup.md)
+ [Tutorial: Comece com o Azure Machine Learning com o SDK R](tutorial-1st-r-experiment.md)
+ [Tutorial: Crie seu primeiro modelo de classificação com aprendizado de máquina automatizado](tutorial-first-experiment-automated-ml.md) (disponível apenas em espaços de trabalho da edição [Enterprise)](overview-what-is-azure-ml.md#sku)
+ [Tutorial: Prever o preço do automóvel com o designer](tutorial-designer-automobile-price-train-score.md) (Disponível apenas em espaços de trabalho da edição [Enterprise)](overview-what-is-azure-ml.md#sku)
