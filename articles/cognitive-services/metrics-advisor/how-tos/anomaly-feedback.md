---
title: Fornecer comentários de anomalias para o serviço do Orientador de métricas
titleSuffix: Azure Cognitive Services
description: Saiba como enviar comentários sobre anomalias encontradas por sua instância do supervisor de métricas e ajustar os resultados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 50d422edf1a4b45132d0b86eac9d4947cef5e5bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933001"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>Ajustar a detecção de anomalias usando os comentários

Se você não estiver satisfeito com alguns dos resultados da detecção de anomalias fornecidos pelo monitor de métricas, poderá adicionar comentários manualmente que afetarão o modelo aplicado aos seus dados. 

Use os botões no canto superior direito da página para ativar o modo de anotação de comentários.

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="Modo de anotação de comentários.":::

Depois que o modo de anotação de comentários for ativado, você poderá fornecer comentários para um ponto ou vários pontos contínuos.

## <a name="give-feedback-for-one-point"></a>Fornecer comentários para um ponto 

Com o modo de anotação de comentários ativado, clique em um ponto para abrir um painel **adicionar comentários** . Você pode definir o tipo de comentário que deseja aplicar. Esses comentários serão incorporados à detecção para pontos futuros.  

* Selecione **anomalia** se você considerar que o ponto foi rotulado incorretamente pelo monitor de métricas. Você pode especificar se um ponto deve ou não ser uma anomalia. 
* Selecione **ChangePoint** se você considerar que o ponto indica o início de uma alteração de tendência.
* Selecione **período** para indicar sazonalidade. O monitor de métrica pode detectar intervalos de sazonalidade automaticamente ou você pode especificar isso manualmente. 

Considere deixar um comentário na caixa de texto de **Comentário** ao mesmo tempo e clique em **salvar** para salvar seus comentários.

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="Modo de anotação de comentários.":::

## <a name="give-feedback-for-multiple-continuous-points"></a>Fornecer comentários para vários pontos contínuos

Você pode fornecer comentários para vários pontos contínuos ao mesmo tempo clicando em baixo e arrastando o mouse sobre os pontos que deseja anotar. Você verá o mesmo menu de comentários acima. Os mesmos comentários serão aplicados a todos os pontos escolhidos depois que você clicar em **salvar**.

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="Modo de anotação de comentários.":::

## <a name="how-to-view-my-feedback"></a>Como exibir meus comentários

Para ver se a detecção de anomalias de um ponto foi alterada, passe o mouse sobre o ponto. A dica de ferramenta mostrará os **comentários afetados: verdadeiro** se a detecção tiver sido alterada. Se ele exibir **false**, o cálculo de comentários foi feito no ponto, mas o resultado da detecção de anomalias não foi alterado.

:::image type="content" source="../media/feedback/affected-point.png" alt-text="Modo de anotação de comentários.":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>Quando devo anotar uma anomalia como "normal"

Há muitas razões pelas quais você pode considerar uma anomalia é um alarme falso. Considere o uso dos seguintes recursos do supervisor de métricas se um dos seguintes cenários se aplicar:


|Cenário  |Recomendação |
|---------|---------|
|A anomalia é causada por alteração de fonte de dados conhecida, por exemplo, uma alteração no sistema.     | Não anote essa anomalia se não for esperado que esse cenário ocorra regularmente.        |
|A anomalia é causada por feriado.     | Use [eventos predefinidos](configure-metrics.md#preset-events) para sinalizar a detecção de anomalias em horários especificados.       |
|Há um padrão regular para detectar anomalias (por exemplo, nos fins de semana) e elas não devem ser anomalias.      |Use o recurso de comentários ou os eventos predefinidos.        |

## <a name="next-steps"></a>Próximas etapas
- [Diagnosticar um incidente](diagnose-incident.md).
- [Configurar métricas e ajustar a configuração de detecção](configure-metrics.md)
- [Configurar alertas e obter notificações usando um web hook](../how-tos/alerts.md)