---
title: 'Tutorial: Implantar um modelo de machine learning com o designer'
titleSuffix: Azure Machine Learning
description: Saiba como criar uma solução de análise preditiva no designer do Azure Machine Learning (versão prévia). Treine, pontue e implante um modelo de machine learning usando módulos do tipo "arrastar e soltar".
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 724a38cb516e5689f817e9ddeaa867b17274971b
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932041"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Tutorial: Implantar um modelo de machine learning com o designer (versão prévia)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Você poderá implantar o modelo preditivo desenvolvido na [parte um do tutorial](tutorial-designer-automobile-price-train-score.md) para dar aos outros uma chance de usá-lo. Na primeira parte, você treinou seu modelo. Agora é hora de gerar novas previsões com base na entrada do usuário. Nesta parte do tutorial, você:

> [!div class="checklist"]
> * Criará um pipeline de inferência em tempo real.
> * Criará um cluster de inferência.
> * Implantará o ponto de extremidade em tempo real.
> * Testará o ponto de extremidade em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

Conclua a [parte um do tutorial](tutorial-designer-automobile-price-train-score.md) para aprender a treinar e pontuar um modelo de machine learning na interface visual.

## <a name="create-a-real-time-inference-pipeline"></a>Criar um pipeline de inferência em tempo real

Para implantar o pipeline, primeiro, converta o pipeline de treinamento em um pipeline de inferência em tempo real. Esse processo remove os módulos de treinamento e adiciona entradas e saídas para as solicitações de inferência.

### <a name="create-a-real-time-inference-pipeline"></a>Criar um pipeline de inferência em tempo real

1. Acima da tela do pipeline, selecione **Criar pipeline de inferência** > **Pipeline de inferência em tempo real**.

    Agora, seu pipeline deve ter esta aparência: 

   ![Captura de tela mostrando a configuração esperada do pipeline após sua preparação para implantação](./media/ui-tutorial-automobile-price-deploy/real-time-inference-pipeline.png)

    Quando você seleciona **Criar pipeline de inferência**, várias coisas acontecem:
    
    * O modelo treinado é armazenado como um módulo de **Conjunto de dados** na paleta de módulos. Você pode encontrá-lo em **Meus conjuntos de dados**.
    * Os módulos de treinamento como **Treinar Modelo** e **Dividir Dados** são removidos.
    * O modelo treinado salvo é adicionado de volta ao pipeline.
    * Os módulos **Entrada de Serviço Web** e **Saída de Serviço Web** são adicionados. Esses módulos mostram o local em que os dados do usuário são inseridos no modelo e o local em que eles são retornados.

    > [!NOTE]
    > O *pipeline de treinamento* é salvo na nova guia na parte superior da tela do pipeline. Ele também pode ser encontrado como um pipeline publicado no designer.
    >

1. Selecione **Executar** e use o mesmo destino de computação e experimento usados na primeira parte.

1. Saída do módulo **Modelo de Pontuação**.

1. No painel de propriedades, selecione **Saídas** > **Visualizar** para verificar se o modelo ainda está funcionando. Você pode ver que os dados originais são exibidos, juntamente com o preço previsto ("Rótulos Pontuados").

1. Selecione **Implantar**.

## <a name="create-an-inferencing-cluster"></a>Cria um cluster inferência

Na caixa de diálogo exibida, selecione uma opção entre os clusters existentes do AKS (Serviço de Kubernetes do Azure) no qual o modelo será implantado. Se não tiver um cluster do AKS, use as etapas a seguir para criar um.

1. Selecione **Computação** na caixa de diálogo exibida para acessar a página **Computação**.

1. Na faixa de opções de navegação, selecione **Clusters de Inferência** >  **+ Novo**.

    ![Captura de tela mostrando como acessar o novo painel do cluster de inferência](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. No painel do cluster de inferência, configure um novo Serviço de Kubernetes.

1. Insira *aks-compute* para o **Nome de computação**.
    
1. Selecione uma região próxima que esteja disponível para a **Região**.

1. Selecione **Criar**.

    > [!NOTE]
    > Leva aproximadamente 15 minutos para criar um novo serviço do AKS. Verifique o estado de provisionamento na página **Clusters de Inferência**.
    >

## <a name="deploy-the-real-time-endpoint"></a>Implantar o ponto de extremidade em tempo real

Após o provisionamento do serviço do AKS, volte para o pipeline de inferência em tempo real para concluir a implantação.

1. Selecione **Implantar** acima da tela.

1. Selecione **Implantar novo ponto de extremidade em tempo real**. 

1. Selecione o cluster do AKS que você criou.

1. Selecione **Implantar**.

    ![Captura de tela mostrando como configurar um novo ponto de extremidade em tempo real](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    Uma notificação de êxito acima da tela é exibida após a conclusão da implantação. Isso pode levar alguns minutos.

## <a name="test-the-real-time-endpoint"></a>Testar o ponto de extremidade em tempo real

Após a conclusão da implantação, teste o ponto de extremidade em tempo real acessando a página **Pontos de extremidade**.

1. Na página **Pontos de extremidade**, selecione o ponto de extremidade implantado.

    ![Captura de tela mostrando a guia pontos de extremidade em tempo real com o ponto de extremidades recém-criado realçado](./media/ui-tutorial-automobile-price-deploy/endpoints.png)

1. Selecione **Testar**.

1. Insira os dados de teste manualmente ou use os dados de exemplo preenchidos automaticamente e selecione **Testar**.

    O portal envia uma solicitação de teste para o ponto de extremidade e mostra os resultados. Embora um valor seja gerado para os dados de entrada, ele não é usado para gerar o valor da previsão.

    ![Captura de tela mostrando como testar o ponto de extremidade em tempo real com o rótulo pontuado para o preço realçado](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu as etapas principais na criação, na implantação e no consumo de um modelo de machine learning no designer. Para saber mais sobre como você pode usar o designer para resolver outros tipos de problemas, confira nossos outros pipelines de exemplo.

> [!div class="nextstepaction"]
> [Amostra de classificação de risco de crédito](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
