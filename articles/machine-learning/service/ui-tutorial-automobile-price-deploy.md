---
title: 'Tutorial: Implantar um modelo de machine learning com a interface visual'
titleSuffix: Azure Machine Learning
description: Saiba como criar uma solução de análise preditiva na interface visual do Azure Machine Learning. Treine, pontue e implante um modelo de machine learning usando módulos do tipo "arrastar e soltar".
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/22/2019
ms.openlocfilehash: 6f8717f70a2cb03a7fd683cfe61f1198461f4305
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792669"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Tutorial: Implantar um modelo de machine learning com a interface visual

Para que outras pessoas possam usar o modelo preditivo desenvolvido na [parte um do tutorial](ui-tutorial-automobile-price-train-score.md), implante-o como um ponto de extremidade em tempo real. Na parte 1, você treinou seu modelo. Agora é hora de gerar novas previsões com base na entrada do usuário. Nesta parte do tutorial, você:

> [!div class="checklist"]
> * Implanta um ponto de extremidade em tempo real
> * Cria um cluster inferência
> * Testa um ponto de extremidade em tempo real

## <a name="prerequisites"></a>Pré-requisitos

Conclua a [parte um do tutorial](ui-tutorial-automobile-price-train-score.md) para aprender a treinar e pontuar um modelo de machine learning na interface visual.

## <a name="deploy-a-real-time-endpoint"></a>Implanta um ponto de extremidade em tempo real

Para implantar seu pipeline, você precisa:

1. Converter o pipeline de treinamento em um pipeline de inferência em tempo real, o que remove módulos de treinamento e adiciona entradas e saídas para solicitações inferência.
1. Implantar o pipeline de inferência.

### <a name="create-a-real-time-inference-pipeline"></a>Criar um pipeline de inferência em tempo real

1. Na parte superior da tela do pipeline, selecione **Criar pipeline de inferência** > **Pipeline de inferência em tempo real**

    Quando você seleciona **Criar pipeline de inferência**, várias coisas acontecem:
    
    * O modelo treinado é armazenado como um módulo de **Conjunto de dados** na paleta de módulos. Você pode encontrá-lo em **Meus conjuntos de dados**.
    * Os módulos, como **Treinar Modelo** e **Dividir Dados**, que foram usados para treinamento são removidos.
    * O modelo treinado salvo é adicionado de volta ao pipeline.
    * Os módulos **Entrada de Serviço Web** e **Saída de Serviço Web** são adicionados. Esses módulos identificam o local em que os dados do usuário serão inseridos no modelo e o local em que os dados são retornados.

    > [!Note]
    > O **pipeline de treinamento** é salvo na nova guia na parte superior da tela do pipeline. Ele também pode ser encontrado como um pipeline publicado na interface visual.
    >

    Agora, seu pipeline deve ter esta aparência:  

   ![Captura de tela mostrando a configuração esperada do pipeline após sua preparação para implantação](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

1. Selecione **Executar** e use o mesmo destino de computação e experimento que você usou na parte 1.

1. Saída do módulo **Modelo de Pontuação**.

1. No painel de propriedades, selecione **Saídas** > **Visualizar** para verificar se o modelo ainda está funcionando. Você pode ver que os dados originais são exibidos, juntamente com o preço previsto ("Rótulos Pontuados").

1. Selecione **Implantar**.

### <a name="create-an-inferencing-cluster"></a>Cria um cluster inferência

Na caixa de diálogo exibida, você pode selecionar entre os clusters do AKS (Serviço de Kubernetes do Azure) existentes no workspace para implantar seu modelo. Se não tiver um cluster do AKS, use as etapas a seguir para criar um.

1. Selecione **Computação** na caixa de diálogo para navegar até a página de **Computação**.

1. Na faixa de opções de navegação, selecione **Clusters de Inferência** >  **+ Novo**.

    ![Captura de tela mostrando como navegar até o novo painel do cluster de inferência](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. No painel do cluster de inferência, configure um novo Serviço de Kubernetes.

1. Insira "aks-compute" como o **Nome de computação**.
    
1. Selecione uma **Região** próxima disponível.

1. Selecione **Criar**.

    > [!Note]
    > Leva aproximadamente 15 minutos para criar um novo serviço do AKS. Você pode verificar o estado de provisionamento na página **Clusters de Inferência**
    >

### <a name="deploy-the-real-time-endpoint"></a>Implantar o ponto de extremidade em tempo real

Após o provisionamento do serviço do AKS, volte para o pipeline de inferência em tempo real para concluir a implantação.

1. Selecione **Implantar** acima da tela.

1. Selecione **Implantar novo ponto de extremidade em tempo real**. 

1. Selecione o cluster do AKS que você criou.

1. Selecione **Implantar**.

    ![Captura de tela mostrando como configurar um novo ponto de extremidade em tempo real](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Uma notificação de êxito acima da tela será exibida quando a implantação for concluída, o que pode levar alguns minutos.

## <a name="test-the-real-time-endpoint"></a>Testar o ponto de extremidade em tempo real

Você pode testar seu ponto de extremidade em tempo real navegando até a página **Pontos de extremidade** no painel de navegação do workspace à esquerda.

1. Na página **Pontos de extremidade**, selecione o ponto de extremidade implantado.

    ![Captura de tela mostrando a guia pontos de extremidade em tempo real com o ponto de extremidades recém-criado realçado](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Selecione **Testar**.

1. Insira os dados de teste ou use os dados de exemplo preenchidos automaticamente e selecione **Testar**.

    A solicitação de teste é enviada para o ponto de extremidade e os resultados são mostrados na página. Embora um valor seja gerado para os dados de entrada, ele não é usado para gerar o valor da previsão.

    ![Captura de tela mostrando como testar o ponto de extremidade em tempo real com o rótulo pontuado para o preço realçado](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu as principais etapas na criação, na implantação e no consumo de um modelo de machine learning na interface visual. Para saber mais sobre como você pode usar a interface visual para resolver outros tipos de problemas, confira os outros pipelines de exemplo.

> [!div class="nextstepaction"]
> [Amostra de classificação de risco de crédito](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
