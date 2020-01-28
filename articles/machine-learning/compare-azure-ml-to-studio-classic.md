---
title: Azure Machine Learning vs. Machine Learning Studio (clássico)
description: Qual a diferença entre o Azure Machine Learning e o Machine Learning Studio (clássico)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: 064786de1f25c3d185984534b9a8fc61602826ee
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311454"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Em que o Azure Machine Learning difere do Machine Learning Studio (clássico)

Este artigo compara os recursos, as funcionalidades e a interface do Azure Machine Learning com os do Machine Learning Studio (clássico). 

## <a name="about-machine-learning-studio-classic"></a>Sobre o Machine Learning Studio (clássico)
O [Machine Learning Studio (clássico)](studio/what-is-ml-studio.md) é um workspace visual do tipo "arrastar e soltar" colaborativo em que é possível criar, testar e implantar soluções de aprendizado de máquina sem precisar escrever código. Ele usa algoritmos de aprendizado de máquina predefinidos e pré-configurados e módulos de manipulação de dados, assim como uma plataforma de computação proprietária.

## <a name="about-azure-machine-learning"></a>Sobre o Azure Machine Learning

Enquanto isso, o [Azure Machine Learning](overview-what-is-azure-ml.md) fornece uma interface Web chamada de designer **e** vários SDKs e uma CLI para preparar dados rapidamente e treinar e implantar modelos de machine learning. Com o Azure Machine Learning você obtém escala, suporte a vários frameworks, funcionalidades avançadas de ML, tais como o machine learning automatizado e o suporte a pipeline.

O designer do Azure Machine Learning oferece uma experiência do tipo "arrastar e soltar" semelhante para o Studio (clássico). No entanto, ao contrário da plataforma de computação proprietária do Studio, o designer (clássico) usa seus próprios recursos de computação, é escalonável e é totalmente integrado ao Azure Machine Learning.  

> [!TIP]
> Os clientes que usam ou avaliam atualmente o Machine Learning Studio (clássico) são incentivados a experimentar o [designer do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-designer), que fornece módulos de ML do tipo "arrastar e soltar", __além de__ escalabilidade, controle de versão e segurança empresarial.

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>Comparação: Azure Machine Learning vs. Machine Learning Studio (clássico)

Veja uma comparação rápida.

||  Azure Machine Learning Designer|Studio (clássico) |
|---| --- | --- | 
|Interface do tipo "arrastar e soltar"| Sim | Sim|
|Experimento| Escala com destino de computação|Escala (limite de 10 GB para dados de treinamento) | 
|Módulos de interface| [Muitos módulos populares](algorithm-module-reference/module-reference.md) | Muitos |
|Destinos de computação de treinamento| Computação do AML (GPU/CPU)|Destino de computação proprietário, somente CPU|
|Inferência de destinos de computação| Serviço de Kubernetes do Azure para inferência em tempo real <br/>Computação AML para inferência de lote|Formato do serviço Web proprietário, não personalizável | 
|Pipeline de ML| Criação de pipeline <br/> Pipeline publicado <br/> Ponto de extremidade de pipeline <br/> [Saiba mais sobre o pipeline do ML](concept-ml-pipelines.md)|Sem suporte | 
|Operações de ML| Implantação configurável, modelo e controle de versão do pipeline|Gerenciamento e implantação de modelos básicos | 
|Modelo| Formato padrão, vários, depende do trabalho de treinamento|Formato proprietário e não portátil.| 
|Treinamento de modelo automatizado|Ainda não no designer, mas possível por meio da interface e dos SDKs.| Não | 

## <a name="get-started-with-azure-machine-learning"></a>Introdução ao Azure Machine Learning

Os recursos a seguir podem ajudar você a começar a usar o Azure Machine Learning

- Leia a [visão geral do Azure Machine Learning](tutorial-first-experiment-automated-ml.md) 

- [Crie seu primeiro pipeline de designer](tutorial-designer-automobile-price-train-score.md) para prever preços automáticos.

![Exemplo do Azure Machine Learning Designer](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Próximas etapas

Além das funcionalidade do tipo "arrastar e soltar" do designer, o Azure Machine Learning tem outras ferramentas disponíveis:  
  + [Usar notebooks do Python para treinar e implantar modelos de Machine Learning](tutorial-1st-experiment-sdk-setup.md)
  + [Usar R Markdown para treinar e implantar modelos de Machine Learning](tutorial-1st-r-experiment.md) 
  + [Usar machine learning automatizado para treinar e implantar modelos de Machine Learning](tutorial-designer-automobile-price-train-score.md) 
  + [Usar a interface de linha de comando de aprendizado de máquina para treinar e implantar um modelo](tutorial-train-deploy-model-cli.md)

