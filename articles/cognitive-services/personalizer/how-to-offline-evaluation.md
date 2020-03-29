---
title: Como realizar avaliação offline - Personalizador
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como usar a avaliação offline para medir a eficácia do seu aplicativo e analisar seu ciclo de aprendizado.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622770"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analise seu ciclo de aprendizado com uma avaliação offline

Saiba como concluir uma avaliação offline e entender os resultados.

Avaliações offline permitem medir o quão eficaz o Personalr é comparado com o comportamento padrão do seu aplicativo, aprender quais recursos estão contribuindo mais para a personalização e descobrir novos valores de aprendizado de máquina automaticamente.

Leia sobre [avaliações offline](concepts-offline-evaluation.md) para saber mais.

## <a name="prerequisites"></a>Pré-requisitos

* Um loop personalizado configurado
* O loop Personalr deve ter uma quantidade representativa de dados - como um estádio, recomendamos pelo menos 50.000 eventos em seus registros para resultados significativos de avaliação. Opcionalmente, talvez você também tenha exportado anteriormente arquivos da _política de aprendizado_, que podem ser comparados e testados na mesma avaliação.

## <a name="run-an-offline-evaluation"></a>Executar uma avaliação offline

1. No [portal Azure,](https://azure.microsoft.com/free/)localize seu recurso Personalizador.
1. No portal Azure, vá até a seção **Avaliações** e selecione **Criar Avaliação**.
    ![No portal Azure, vá para a seção **Avaliações** e selecione **Criar avaliação**.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Configure os seguintes valores:

    * Um nome de avaliação.
    * Data de início e término - são datas que especificam a gama de dados a serem usados na avaliação. Esses dados devem estar presentes nos registros, conforme especificado no valor [de Retenção de Dados.](how-to-settings.md)
    * Otimização Detecção definida como **sim**.

    > [!div class="mx-imgBorder"]
    > ![Escolha as configurações de avaliação offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Inicie a Avaliação selecionando **Ok**.

## <a name="review-the-evaluation-results"></a>Revisar os resultados da avaliação

As avaliações podem levar muito tempo para serem executadas, dependendo da quantidade de dados a serem processados, do número de políticas de aprendizado para comparação e se uma otimização foi solicitada.

Uma vez concluído, você pode selecionar a avaliação na lista de avaliações e, em seguida, selecionar **Comparar a pontuação do seu aplicativo com outras configurações potenciais de aprendizado**. Selecione esse recurso quando quiser ver como sua política de aprendizado atual funciona em comparação com uma nova política.

1. Reveja o desempenho das políticas de [aprendizagem.](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)

    > [!div class="mx-imgBorder"]
    > [![Revisar resultados da avaliação](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Selecione **Aplicar** para aplicar a política que melhora o modelo melhor para seus dados.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [como funcionam as avaliações offline.](concepts-offline-evaluation.md)
