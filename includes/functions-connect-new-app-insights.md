---
title: incluir arquivo
description: incluir arquivo
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/09/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3289ba03d0f613d004bc8bff4dbcf2bd434f3da3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121505"
---
As funções facilitam a adição da integração do Application Insights a um aplicativo de funções do [portal do Azure].

1. No portal do [portal do Azure][Azure], procure e selecione **aplicativo de funções**e, em seguida, escolha seu aplicativo de funções. 

1. Selecione a faixa **Application insights não está configurada** na parte superior da janela. Se você não vir essa faixa, seu aplicativo já terá Application Insights habilitado.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Habilitar o Application Insights no portal":::

1. Crie um recurso do Application Insights usando as configurações especificadas na tabela abaixo da imagem.

   :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Criar um recurso de Application Insights":::

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome de aplicativo exclusivo | É mais fácil usar o mesmo nome que seu aplicativo de funções, que deve ser exclusivo em sua assinatura. | 
    | **Localidade** | Europa Ocidental | Se possível, use a mesma [região](https://azure.microsoft.com/regions/) que seu aplicativo de funções ou uma região próxima dela. |

1. Escolha **Aplicar**. O recurso do Application Insights é criado no mesmo grupo de recursos e assinatura que seu aplicativo de funções. Depois que o recurso for criado, feche a janela do Application Insights.

1. De volta ao seu aplicativo de funções, selecione **configurações**  >  **configuração**e, em seguida, selecione **configurações do aplicativo**. Se você vir uma configuração chamada `APPINSIGHTS_INSTRUMENTATIONKEY`, isso significa que a integração do Application Insights está habilitada para seu aplicativo de funções em execução no Azure.

[Portal do Azure]: https://portal.azure.com
