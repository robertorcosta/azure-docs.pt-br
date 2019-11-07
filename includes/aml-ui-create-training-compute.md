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
ms.openlocfilehash: f7cdfb8b9edbce5ef2b094cf6a603904d39a7404
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493615"
---
Um pipeline é executado em um destino de computação, que é um recurso de computação anexado ao workspace. Depois de criar um destino de computação, você poderá reutilizá-lo para execuções futuras.

1. Selecione **Executar** na parte superior da tela para executar o pipeline.

1. Quando o painel **Configurações** for exibido, selecione **Selecionar de destino de computação**.

    Se já tiver um destino de computação disponível, você poderá selecioná-lo para executar esse pipeline.

    > [!NOTE]
    > O designer somente pode executar experimentos em destinos de Computação do Machine Learning. Outros destinos de computação não serão mostrados.

1. Forneça um nome para o recurso de computação.

1. Clique em **Salvar**.

    ![Configurar o destino de computação](./media/aml-ui-create-training-compute/set-compute.png)

1. Selecione **Executar**.

1. Na caixa de diálogo **Configurar execução de pipeline**, selecione **+Novo experimento** para o **Experimento**

    > [!NOTE]
    > Pipelines semelhantes no grupo de experimentos são executados juntos. Se executar um pipeline várias vezes, você poderá selecionar o mesmo experimento para execuções sucessivas.

    * Insira um **Nome do Experimento** descritivo

    * Selecionar **Executar**
    
    Você pode exibir o status e os detalhes da execução no canto superior direito da tela.

    > [!NOTE]
    > São necessários aproximadamente 5 minutos para criar um recurso de computação. Depois que o recurso for criado, você poderá reutilizá-lo e ignorar esse tempo de espera para execuções futuras.
    >
    > O recurso de computação será dimensionado automaticamente para 0 nó quando estiver ocioso para economia de custos.  Quando você o usar novamente após um atraso, talvez precise aguardar aproximadamente 5 minutos enquanto ele é escalado verticalmente mais uma vez.
