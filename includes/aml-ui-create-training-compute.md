---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: 1ee90e0c99234497b072bbee0b92d76129baea48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929623"
---
Um pipeline é executado em um destino de computação, que é um recurso de computação anexado ao workspace. Depois de criar um destino de computação, você poderá reutilizá-lo para execuções futuras.

1. Selecione **Executar** na parte superior da tela para executar o pipeline.

1. Quando o painel **Configurações** for exibido, selecione **Selecionar de destino de computação**.

    Se já tiver um destino de computação disponível, você poderá selecioná-lo para executar esse pipeline.

    > [!NOTE]
    > O designer pode executar experimentos apenas em destinos de Computação do Azure Machine Learning. Outros destinos de computação não serão mostrados.

1. Insira um nome para o recurso de computação.

1. Clique em **Salvar**.

    ![Configurar o destino de computação](./media/aml-ui-create-training-compute/set-compute.png)

1. Selecione **Executar**.

1. Na caixa de diálogo **Configurar execução de pipeline**, selecione **+Novo experimento** para o **Experimento**.

    > [!NOTE]
    > Pipelines semelhantes no grupo de experimentos são executados juntos. Se executar um pipeline várias vezes, você poderá selecionar o mesmo experimento para execuções sucessivas.

    1. Insira um nome descritivo para o **Nome do Experimento**.

    1. Selecione **Executar**.
    
    Você pode exibir o status e os detalhes da execução no canto superior direito da tela.

    > [!NOTE]
    > A criação de um recurso de computação demora aproximadamente cinco minutos. Depois que o recurso for criado, você poderá reutilizá-lo e ignorar esse tempo de espera para execuções futuras.
    >
    > O recurso de computação será dimensionado automaticamente para zero nós quando estiver ocioso, a fim de economizar custos. Ao usá-lo novamente após um atraso, talvez precise aguardar aproximadamente cinco minutos enquanto ele é escalado verticalmente mais uma vez.
