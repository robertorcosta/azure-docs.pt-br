---
title: Como dimensionar seu ambiente-Azure Time Series Insights | Microsoft Docs
description: Saiba como dimensionar seu ambiente de Azure Time Series Insights usando o portal do Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: bc6c33762fa572e971fff9dee8988a100e50b07c
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561136"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Como dimensionar o ambiente do Time Series Insights

Este artigo descreve como alterar a capacidade de seu ambiente de Time Series Insights usando o [portal do Azure](https://portal.azure.com). A capacidade é o multiplicador aplicado à taxa de ingresso, à capacidade de armazenamento e ao custo associado com a SKU selecionada.

Você pode usar o Portal do Azure para aumentar ou diminuir a capacidade de um determinada SKU de preços.

No entanto, não é permitido alterar a SKU da camada de preços. Por exemplo, um ambiente com uma SKU de preço S1 não pode ser convertido em um S2 ou vice-versa.

## <a name="ga-limits"></a>Limites de GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Alterar a capacidade do seu ambiente

1. No Portal do Azure, localize e selecione seu ambiente Time Series Insights.

1. No menu de seu ambiente de Time Series Insights, selecione **configuração de armazenamento**.

   [![configurar sua capacidade de Time Series Insights](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Ajuste o controle deslizante **Capacidade** para selecionar a capacidade que atenda aos requisitos de suas taxas de entrada e capacidade de armazenamento. Observe que a **Taxa de entrada**, a **Capacidade de armazenamento** e o **Custo estimado** são atualizadas dinamicamente para mostrar o impacto da alteração.

   [![configurar seu ambiente usando o controle deslizante capacidade](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Como alternativa, você pode digitar o número do multiplicador de capacidade na caixa de texto à direita do controle deslizante.

1. Selecione **Salvar** para dimensionar o ambiente. O indicador de progresso é exibido momentaneamente até que a alteração seja confirmada.

1. Verifique se a nova capacidade é [suficiente para evitar a limitação](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Próximos passos

- Para mais informações, veja [Noções básicas sobre retenção no Time Series Insights](time-series-insights-concepts-retention.md).

- Saiba mais sobre como [Configurar a retenção de dados no Azure Time Series insights](time-series-insights-how-to-configure-retention.md).

- Saiba mais sobre como [planejar o seu ambiente](time-series-insights-environment-planning.md).