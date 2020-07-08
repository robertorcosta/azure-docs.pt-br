---
title: Iterar e desenvolver pipelines de aprendizado de máquina
titleSuffix: Azure Machine Learning
description: Padrões, práticas e dicas para o desenvolvimento rápido
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82858186"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>Iterar e desenvolver pipelines de aprendizado de máquina

Azure Machine Learning pipelines fornecem uma maneira eficiente de modularizar seu código, reutilizar os resultados e otimizar seus recursos de computação. Aqui estão algumas dicas práticas e práticas para trabalhar com pipelines.

## <a name="how-do-you-get-started-with-pipelines"></a>Como começar a usar pipelines?

Há várias opções para começar se você for novo nos pipelines:

* Se você aprende melhor lendo e recriando o processo de construção, o artigo [criar e executar pipelines de Machine Learning com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md) é uma boa opção 
* Se você gosta de aprender interativamente em um notebook Jupyter, o pipeline desenvolvido nos [pipelines de Azure Machine Learning: introdução](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb) notebook pode ser ideal para você
* Se você preferir uma situação de primeiro código, clonar o repositório de [demonstração Azure Machine Learning pipelines](https://github.com/microsoft/aml-pipelines-demo) fornece um bom ponto de partida

## <a name="how-do-you-modularize-pipeline-code"></a>Como você modulariza o código do pipeline? 

Os módulos e a `ModuleStep` classe oferecem uma grande oportunidade para modularizar seu código ml. No entanto, é preciso ter em mente que a movimentação entre as etapas do pipeline é muito mais cara do que uma chamada de função. A pergunta que você deve fazer não é muito "são essas funções e dados conceitualmente diferentes daquelas nesta outra seção?" Mas "quero que essas funções e dados evoluam separadamente?" ou "esse cálculo é caro e posso reutilizar sua saída?" Para obter mais informações, consulte thisn'tebook [como criar o módulo, ModuleVersion e usá-los em um pipeline com ModuleStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb).

Conforme discutido anteriormente, separar a preparação de dados do treinamento geralmente é uma dessas oportunidades. Às vezes, a preparação de dados é complexa e demorada o suficiente para que você possa dividir o processo em etapas de pipeline separadas. Outras oportunidades incluem teste e análise após o treinamento. 

## <a name="how-do-you-speed-pipeline-iteration"></a>Como você agiliza a iteração do pipeline? 

As técnicas comuns para a rápida iteração de pipelines incluem: 

- Clonagem do pipeline (fazendo uma cópia) e executando novamente o pipeline rapidamente
- Mantendo a instância de computação em execução, para evitar o tempo de inicialização
- Configurar dados e etapas para permitir a reutilização permitirá que o pipeline ignore o recálculo de dados inalterados

Quando quiser iterar rapidamente, você pode clonar seu pipeline, criar um pipeline e executar novamente o pipeline. Outra técnica útil é se você mantiver a computação em funcionamento, não incorrerá no custo da rotação da nova computação. Se você configurar a etapa para permitir a reutilização do resultado de uma execução, a execução repetida reutilizará os resultados sempre que possível (quando não houver nenhuma alteração nas etapas).

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>Como colaborar usando pipelines ML? 

Pipelines separados são linhas naturais ao longo do qual dividir o esforço. Vários desenvolvedores ou até mesmo várias equipes podem trabalhar em etapas diferentes, desde que os dados e os argumentos que fluem entre as etapas sejam acordados. 

Durante o desenvolvimento ativo, você pode recuperar `PipelineRun` e `StepRun` executar os resultados do espaço de trabalho, usar esses objetos para baixar a saída final e intermediária e usar esses artefatos para seu próprio trabalho modularizado.

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>Usar pipelines para testar técnicas isoladamente

Soluções de ML do mundo real geralmente envolvem uma personalização considerável de cada etapa. Os dados brutos geralmente precisam ser preparados de alguma forma: filtrados, transformados e aumentados. Os processos de treinamento podem ter várias arquiteturas potenciais e, para aprendizado profundo, muitas variações possíveis para tamanhos de camada e funções de ativação. Mesmo com uma arquitetura consistente, a pesquisa de hiperparâmetro pode produzir um WINS significativo.

Além de ferramentas como [AutoML](concept-automated-ml.md) e [pesquisa de hiperparâmetro automatizado](how-to-tune-hyperparameters.md), os pipelines podem ser uma ferramenta importante para soluções de teste A/B. Se você tiver várias variantes de suas etapas de pipeline, será fácil gerar execuções separadas tentando suas variações: 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

