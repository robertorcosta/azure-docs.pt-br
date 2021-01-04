---
title: 'Tutorial: Usar a interface do usuário do tipo "arrastar e soltar" para criar o modelo preditivo (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Saiba como criar e implantar um modelo preditivo de machine learning com o designer, de modo que você possa usá-lo para prever resultados no Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f0e1ffe60069a2379f8eddab1aae74db2b4eac50
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370534"
---
# <a name="tutorial--power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Tutorial:  Integração do Power BI – Usar a interface do usuário do tipo "arrastar e soltar" para criar o modelo preditivo (parte 1 de 2)

Na primeira parte deste tutorial, você treinará e implantará um modelo preditivo de machine learning usando o designer do Azure Machine Learning, uma interface do usuário com pouco código do tipo "arrastar e soltar". Na segunda parte, você usará o modelo para prever resultados no Microsoft Power BI.

Neste tutorial, você:

> [!div class="checklist"]
> * Criar uma instância de computação do Azure Machine Learning
> * Criar um cluster de inferência do Azure Machine Learning
> * Criar um conjunto de dados
> * Treinar o modelo de regressão
> * Implantar o modelo em um ponto de extremidade de pontuação em tempo real


Há três maneiras diferentes de criar e implantar o modelo que você usará no Power BI.  Este artigo aborda a Opção B: Treinar e implantar modelos usando o designer.  Essa opção mostra uma experiência de criação com pouco código usando o designer (uma interface do usuário do tipo "arrastar e soltar").  

Em vez disso, você poderá usar:

* [Opção A: Treinar e implantar modelos usando notebooks](tutorial-power-bi-custom-model.md): uma experiência de criação code-first usando Jupyter Notebooks hospedados no estúdio do Azure Machine Learning.
* [Opção C: Treinar e implantar modelos usando o ML automatizado](tutorial-power-bi-automated-model.md): uma experiência de criação sem código que automatiza totalmente a preparação de dados e o treinamento do modelo.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure ([uma avaliação gratuita está disponível](https://aka.ms/AMLFree)). 
- Um Workspace do Azure Machine Learning. Caso ainda não tenha um workspace, siga [Como criar um Workspace do Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Conhecimento introdutório de fluxos de trabalho de machine learning.


## <a name="create-compute-for-training-and-scoring"></a>Criar uma instância de computação para treinamento e pontuação

Nesta seção, você criará uma *instância de computação*, que será usada para treinar modelos de machine learning. Além disso, você criará um *cluster de inferência*, que será usado para hospedar o modelo implantado para pontuação em tempo real.

Faça logon no [estúdio do Azure Machine Learning](https://ml.azure.com) e selecione **Computação** no menu à esquerda e escolha **Nova**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Captura de tela mostrando como criar uma instância de computação":::

Na tela resultante **Criar instância de computação**, selecione um tamanho de VM (para este tutorial, escolha uma `Standard_D11_v2`) e **Avançar**. Na página Configuração, forneça um nome válido para a instância de computação e selecione **Criar**. 

>[!TIP]
> A instância de computação também pode ser usada para criar e executar notebooks.

Agora você poderá ver que o **Status** da sua instância de computação é **Criando**. Serão necessários cerca de 4 minutos para que o computador seja provisionado. Enquanto estiver aguardando, selecione a guia **Cluster de Inferência** na página de computação seguido de **Novo**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Captura de tela mostrando como criar um cluster de inferência":::

Na página resultante **Criar cluster de inferência**, selecione uma região seguida de um tamanho de VM (para este tutorial, escolha uma `Standard_D11_v2`) e **Avançar**. Na página **Definir Configurações**:

1. Forneça um nome de computação válido
1. Selecione **Desenvolvimento/teste** como a finalidade do cluster (cria um só nó para hospedar o modelo implantado)
1. Escolha **Criar**

Agora você poderá ver que o **Status** do seu cluster de inferência é **Criando**. Serão necessários cerca de 4 minutos para que o cluster de nó único seja implantado.

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Neste tutorial, você usará o [conjunto de dados Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), que é disponibilizado em [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).

Para criar o conjunto de dados, no menu à esquerda, selecione **Conjuntos de Dados** e **Criar Conjunto de Dados** e você verá as seguintes opções:

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

## <a name="create-a-machine-learning-model-using-designer"></a>Criar um modelo de machine learning usando o designer

Depois de criar a instância de computação e os conjuntos de dados, passe para a criação do modelo de machine learning usando o designer. No estúdio do Azure Machine Learning, selecione **Designer** seguido de **Novo Pipeline**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Captura de tela mostrando como criar um pipeline":::

Você verá uma *tela* em branco, em que também poderá ver um **menu Configurações**:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Captura de tela mostrando como selecionar um destino de computação":::

No **menu Configurações**, **Selecione destino de computação** e a instância de computação criada anteriormente seguida de **Salvar**. Renomeie o **Nome de rascunho** para algo mais fácil de memorizar (por exemplo *diabetes-model*) e insira uma descrição.

Em seguida, nos ativos listados, expanda **Conjuntos de Dados**, localize o conjunto de dados **diabetes** e arraste e solte este módulo na tela:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Captura de tela mostrando como arrastar um componente":::

Depois, arraste e solte os seguintes componentes na tela:

1. Regressão linear (localizada em **Algoritmos do Machine Learning**)
1. Treinar modelo (localizado em **Treinamento do Modelo**)

A tela será parecida com esta (observe que a parte superior e a parte inferior dos componentes têm pequenos círculos chamados portas, realçados em vermelho abaixo):

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Captura de tela mostrando como os componentes não conectados":::
 
Em seguida, você precisará *conectar* esses componentes juntos. Escolha a porta na parte inferior do conjunto de dados **diabetes** e arraste-o para a porta à direita na parte superior do componente **Treinar modelo**. Selecione a porta na parte inferior do componente **Regressão linear** e arraste-o para a porta à esquerda na parte superior da porta **Treinar modelo**.

Escolha a coluna no conjunto de dados a ser usada como a variável de rótulo (destino) para previsão. Selecione o componente **Treinar modelo** seguido de **Editar coluna**. Na caixa de diálogo, selecione **Inserir o nome da coluna** seguido de **Y** na lista suspensa:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Captura de tela Selecionar coluna de rótulo":::

Selecione **Salvar**. O *fluxo de trabalho* de machine learning terá a seguinte aparência:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Captura de tela mostrando como os componentes conectados":::

Selecione **Enviar** e **Criar** em experimento. Forneça um nome para o experimento seguido de **Enviar**.

>[!NOTE]
> Deverá levar cerca de cinco minutos para que o experimento seja concluído na primeira execução. As execuções seguintes serão muito mais rápidas, pois os caches do designer já executaram os componentes para reduzir a latência.

Quando o experimento for concluído, você verá:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Captura de tela mostrando a execução concluída":::

Inspecione os logs do experimento selecionando **Treinar modelo** seguido de **Saídas + logs**.

## <a name="deploy-the-model"></a>Implantar o modelo

Para implantar o modelo, selecione **Criar Pipeline de Inferência** (localizado na parte superior da tela) seguido de **Pipeline de inferência em tempo real**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Captura de tela mostrando o pipeline de inferência em tempo real":::
 
O pipeline é condensado com apenas os componentes necessários para fazer a pontuação do modelo. Ao pontuar os dados, você não saberá os valores da variável de destino e, portanto, podemos remover **Y** do conjunto de dados. Para remover isso, adicione à tela um componente **Selecionar colunas no Conjunto de Dados**. Conecte o componente para que o conjunto de dados Diabetes seja a entrada e os resultados sejam a saída para o componente **Pontuar Modelo**:

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Captura de tela mostrando a remoção de uma coluna":::

Escolha o componente **Selecionar Colunas no Conjunto de Dados** na tela seguida de **Editar Colunas**. Na caixa de diálogo Selecionar colunas, escolha **Por nome** e verifique se todas as variáveis de entrada estão selecionadas, mas **não** o destino:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Captura de tela mostrando a remoção de uma configuração de coluna":::

Selecione **Salvar**. Por fim, selecione o componente **Pontuar Modelo** e verifique se a caixa de seleção **Acrescentar colunas de pontuação à saída** está desmarcada (somente as previsões são enviadas novamente, em vez das entradas *e* das previsões, reduzindo a latência):

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Captura de tela mostrando as configurações do componente Pontuar Modelo":::

Escolha **Enviar** na parte superior da tela.

Depois de executar o pipeline de inferência com êxito, implante o modelo no seu cluster de inferência. Selecione **Implantar**, que mostrará a caixa de diálogo **Configurar ponto de extremidade em tempo real**. Escolha **Implantar novo ponto de extremidade em tempo real**, nomeie o ponto de extremidade **my-diabetes-model**, selecione a inferência criada anteriormente e **Implantar**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Captura de tela mostrando as configurações do ponto de extremidade em tempo real":::
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu como treinar e implantar um modelo de designer. Na próxima parte, você aprenderá a consumir (pontuar) esse modelo no Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumir um modelo no Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
