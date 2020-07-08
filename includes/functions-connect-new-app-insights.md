---
title: incluir arquivo
description: incluir arquivo
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84730961"
---
Azure Functions facilita a adição de Application Insights integração a um aplicativo de funções do [portal do Azure].

1. No portal do [portal do Azure][Azure], procure e selecione **aplicativo de funções**e, em seguida, escolha seu aplicativo de funções. 

1. Selecione a faixa **Application insights não está configurada** na parte superior da janela. Se você não vir essa faixa, seu aplicativo poderá já ter Application Insights habilitado.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Habilitar o Application Insights no portal":::

1. Expanda **alterar seu recurso** e crie um recurso de Application insights usando as configurações especificadas na tabela a seguir.  

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome de recurso novo** | Nome de aplicativo exclusivo | É mais fácil usar o mesmo nome que seu aplicativo de funções, que deve ser exclusivo em sua assinatura. | 
    | **Localidade** | Europa Ocidental | Se possível, use a mesma [região](https://azure.microsoft.com/regions/) que seu aplicativo de funções ou uma região próxima dela. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Criar um recurso do Application Insights":::

1. Escolha **Aplicar**. 

   O recurso do Application Insights é criado no mesmo grupo de recursos e assinatura que seu aplicativo de funções. Depois que o recurso for criado, feche a janela do Application Insights.

1. Em seu aplicativo de funções, selecione **configuração** em **configurações**e, em seguida, selecione **configurações do aplicativo**. Se você vir uma configuração chamada `APPINSIGHTS_INSTRUMENTATIONKEY`, isso significa que a integração do Application Insights está habilitada para seu aplicativo de funções em execução no Azure.

[Portal do Azure]: https://portal.azure.com
