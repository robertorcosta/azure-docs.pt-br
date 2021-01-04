---
title: 'Tutorial: Criar o modelo preditivo usando ML automatizado (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Saiba como criar e implantar modelos de ML automatizado, de modo que você possa usar o melhor modelo para prever resultados no Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 897f493edf6ccdebb25c201e8e4f9babfb0754c5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370560"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-using-automated-machine-learning-part-1-of-2"></a>Tutorial: Integração do Power BI – Criar o modelo preditivo usando machine learning automatizado (parte 1 de 2)

Na primeira parte deste tutorial, você treinará e implantará um modelo preditivo de machine learning usando o machine learning automatizado no estúdio do Azure Machine Learning.  Na segunda parte, você usará o modelo com o melhor desempenho para prever resultados no Microsoft Power BI.

Neste tutorial, você:

> [!div class="checklist"]
> * Criar um cluster de computação do Azure Machine Learning
> * Criar um conjunto de dados
> * Criar uma execução do ML automatizado
> * Implantar o melhor modelo em um ponto de extremidade de pontuação em tempo real


Há três maneiras diferentes de criar e implantar o modelo que você usará no Power BI.  Este artigo aborda a Opção C: Treinar e implantar modelos usando o ML automatizado no estúdio.  Essa opção mostra uma experiência de criação sem código que automatiza totalmente a preparação de dados e o treinamento do modelo. 

Em vez disso, você poderá usar:

* [Opção A: Treinar e implantar modelos usando Notebooks](tutorial-power-bi-custom-model.md): uma experiência de criação code-first usando Jupyter Notebooks hospedados no estúdio do Azure Machine Learning.
* [Opção B: Treinar e implantar modelos usando o designer](tutorial-power-bi-designer-model.md): uma experiência de criação com pouco código usando o Designer (uma interface do usuário do tipo "arrastar e soltar").

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure ([uma avaliação gratuita está disponível](https://aka.ms/AMLFree)). 
- Um Workspace do Azure Machine Learning. Caso ainda não tenha um workspace, siga [Como criar um Workspace do Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-compute-cluster"></a>Criar um cluster de cálculo

O ML automatizado treina automaticamente muitos modelos de machine learning diferentes para encontrar o "melhor" algoritmo e os "melhores" parâmetros. O Azure Machine Learning paraleliza a execução do treinamento de modelo em um cluster de cálculo.

No [estúdio do Azure Machine Learning](https://ml.azure.com), selecione **Computação** no menu à esquerda seguido da guia **Clusters de Cálculo**. Selecione **Novo**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Captura de tela mostrando como criar um cluster de cálculo":::

Na tela **Criar cluster de cálculo**:

1. Escolha um tamanho de VM (para os fins deste tutorial, um computador `Standard_D11_v2` é suficiente).
1. Selecione **Avançar**
1. Forneça um nome de computação válido
1. Mantenha o **Número mínimo de nós** em 0
1. Altere o **Número máximo de nós** para 4
1. Escolha **Criar**

Você poderá ver que o status do cluster foi alterado para **Criando**.

>[!NOTE]
> Quando o cluster for criado, ele não terá nenhum nó, o que significa que nenhum custo de computação será cobrado. Você só gera custos quando o trabalho de ML automatizado é executado. O cluster será escalado de novo para 0 automaticamente após 120 segundos de tempo ocioso.


## <a name="create-dataset"></a>Criar conjunto de dados

Neste tutorial, você usará o [conjunto de dados Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), que é disponibilizado em [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).

Para criar o conjunto de dados, selecione o menu à esquerda **Conjuntos de dados** seguido de **Criar Conjunto de Dados** e você verá as seguintes opções:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Captura de tela mostrando como criar um conjunto de dados":::

Selecione **Do Conjunto de Dados em Aberto** e na tela **Criar conjunto de dados com base no Conjunto de Dados em Aberto**:

1. Procure *diabetes* usando a barra de pesquisa
1. Selecione **Exemplo: Diabetes**
1. Selecione **Avançar**
1. Forneça um nome para o conjunto de dados *diabetes*
1. Escolha **Criar**

Explore os dados selecionando o conjunto de dados seguido de **Explorar**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Captura de tela mostrando como explorar o conjunto de dados":::

Os dados têm 10 variáveis de entrada de linha de base (como idade, sexo, índice de massa corpórea, pressão arterial média e seis medições de soro sanguíneo) e uma variável de destino chamada **Y** (uma medida quantitativa da progressão de diabetes um ano após a linha de base).

## <a name="create-automated-ml-run"></a>Criar uma execução de ML automatizado

No [estúdio do Azure Machine Learning](https://ml.azure.com), selecione **ML Automatizado** no menu à esquerda seguido de **Nova Execução de ML Automatizado**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Captura de tela mostrando como criar uma execução de ML automatizado":::

Em seguida, selecione o conjunto de dados de **diabetes** criado anteriormente e escolha **Avançar**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Captura de tela mostrando como selecionar um conjunto de dados":::
 
Na tela **Configurar a execução**:

1. Em **Nome do experimento,** selecione **Criar**
1. Forneça um nome para o experimento
1. No campo Coluna de destino, selecione **Y**
1. No campo **Selecionar cluster de cálculo**, selecione o cluster de cálculo criado anteriormente. 

O formulário concluído será semelhante a:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Captura de tela mostrando como configurar o ML automatizado":::

Por fim, você precisará selecionar a tarefa de machine learning a ser executada, que é **Regressão**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Captura de tela mostrando como configurar a tarefa":::

Selecione **Concluir**.

> [!IMPORTANT]
> Levará cerca de 30 minutos para que o ML automatizado termine de treinar os 100 modelos diferentes.

## <a name="deploy-the-best-model"></a>Implantar o melhor modelo

Quando a execução do ML automatizado for concluída, você poderá ver a lista de todos os diferentes modelos de machine learning testados selecionando a guia **Modelos**. Os modelos são classificados em ordem de desempenho: o modelo com o melhor desempenho será mostrado primeiro. Quando você selecionar o melhor modelo, o botão **Implantar** será habilitado:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Captura de tela mostrando a lista de modelos":::

A seleção de **Implantar** apresentará uma tela **Implantar um modelo**:

1. Forneça um nome para o serviço de modelo: use **diabetes-model**
1. Selecione **Serviço de Contêiner do Azure**
1. Escolha **Implantar**

Você verá uma mensagem informando que o modelo foi implantado com êxito.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu como treinar e implantar um modelo de machine learning usando o ML automatizado. No próximo tutorial, você verá como consumir (pontuar) esse modelo no Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumir um modelo no Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
