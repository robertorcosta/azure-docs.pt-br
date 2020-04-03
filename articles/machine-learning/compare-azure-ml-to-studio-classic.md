---
title: Azure Machine Learning vs. Machine Learning Studio (clássico)
description: Qual é a diferença entre o Azure Machine Learning e o Machine Learning Studio (clássico)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 5577a9847ff405397c553028a6dfdf2df80d03fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371831"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning versus Machine Learning Studio (clássico)

Neste artigo, você aprende a diferença entre o Azure Machine Learning e o Machine Learning Studio (clássico). 

O Azure Machine Learning fornece SDKs do Python e do R **e** o designer do tipo "arrastar e soltar" para criar e implantar modelos de machine learning. O Studio (clássico) oferece apenas uma experiência autônoma do tipo "arrastar e soltar".

Recomendamos que novos usuários escolham o Azure Machine Learning para maior variedade de ferramentas de aprendizado de máquina de ponta.

## <a name="quick-comparison"></a>Comparação rápida

A seguinte tabela resume algumas das principais diferenças entre o Azure Machine Learning e o Studio (clássico):

| | Machine Learning Studio (clássico) | Azure Machine Learning |
|---| --- | --- |
| Interface de "arrastar e soltar" | Com suporte | Compatível – [designer do Azure Machine Learning (versão prévia)](concept-designer.md) | 
| Experimento | Escalável (limite de 10 GB para dados de treinamento) | Escala com destino de computação |
| Destinos de computação de treinamento | Destino de computação proprietário, apenas suporte à CPU | Ampla gama de [destinos de computação de treinamento](concept-compute-target.md#train) personalizáveis. Inclui suporte à GPU e à CPU | 
| Destinos de computação de implantação | Formato do serviço Web proprietário, não personalizável | Ampla gama de [destinos de computação de implantação](concept-compute-target.md#deploy) personalizáveis. Inclui suporte à GPU e à CPU |
| Pipeline de ML | Sem suporte | Crie [pipelines](concept-ml-pipelines.md) flexíveis e modulares para automatizar fluxos de trabalho |
| MLOps | Gerenciamento e implantação de modelos básicos | Controle de versão de entidade (modelo, dados, fluxos de trabalho), automação de fluxo de trabalho, integração com ferramentas CICDs [e muito mais](concept-model-management-and-deployment.md) |
| Formato do modelo | Formato proprietário, somente Studio (clássico) | Vários formatos compatíveis dependendo do tipo de trabalho de treinamento |
| Treinamento de modelo automatizado e ajuste de hiperparâmetro |  Sem suporte | [Compatível com o SDK e com o workspace visual](concept-automated-ml.md) | 
| Detecção de descompasso de dados | Sem suporte | [Compatível com o SDK e no workspace visual](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migrar do Machine Learning Studio (clássico)

No momento, não há como migrar ativos do Studio (clássico) para o designer do Azure Machine Learning (versão prévia). Os usuários atuais do Studio (clássico) podem continuar usando os ativos de aprendizado de máquina. No entanto, incentivamos todos os usuários a considerar o uso do designer, que proporciona uma experiência do tipo "arrastar e soltar" familiar com melhor fluxo de trabalho, **além de** escalabilidade, controle de versão e segurança empresarial.

## <a name="get-started-with-azure-machine-learning"></a>Introdução ao Azure Machine Learning

Os recursos a seguir podem ajudar você a começar a usar o Azure Machine Learning. 

- Leia a [visão geral do Azure Machine Learning](overview-what-is-azure-ml.md).

- Crie seu [primeiro experimento com o SDK do Python](tutorial-1st-experiment-sdk-setup.md).

- [Crie seu primeiro pipeline de designer](tutorial-designer-automobile-price-train-score.md) para prever preços automáticos.

![Exemplo do Azure Machine Learning Designer](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Próximas etapas

Além das funcionalidade do tipo "arrastar e soltar" do designer, o Azure Machine Learning tem outras ferramentas disponíveis:  
  + [Usar notebooks do Python para treinar e implantar modelos de Machine Learning](tutorial-1st-experiment-sdk-setup.md)
  + [Usar R Markdown para treinar e implantar modelos de Machine Learning](tutorial-1st-r-experiment.md) 
  + [Usar machine learning automatizado para treinar e implantar modelos de Machine Learning](tutorial-first-experiment-automated-ml.md)  
  + [Usar a interface de linha de comando de aprendizado de máquina para treinar e implantar um modelo](tutorial-train-deploy-model-cli.md)

