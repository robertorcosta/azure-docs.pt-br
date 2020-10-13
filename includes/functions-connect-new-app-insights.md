---
title: arquivo de inclusão
description: arquivo de inclusão
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84730961"
---
O Azure Functions facilita adicionar a integração do Application Insights a um aplicativo de funções do [portal do Azure].

1. No [portal do Azure][portal do Azure], pesquise **aplicativo de funções** e selecione-o. Depois escolha seu aplicativo de funções. 

1. Selecione a faixa **O Application Insights não está configurado** na parte superior da janela. Caso não veja essa faixa, pode ser que o Application Insights já esteja habilitado no aplicativo.

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="Habilitar o Application Insights no portal":::

1. Expanda a opção **Alterar seu recurso** e crie um recurso do Application Insights usando as configurações especificadas na tabela a seguir.  

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome de recurso novo** | Nome de aplicativo exclusivo | É mais fácil usar o mesmo nome que seu aplicativo de funções, que deve ser exclusivo em sua assinatura. | 
    | **Localidade** | Europa Ocidental | Se possível, use a mesma [região](https://azure.microsoft.com/regions/) que seu aplicativo de funções ou uma região próxima dela. |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Habilitar o Application Insights no portal":::

1. Escolha **Aplicar**. 

   O recurso do Application Insights é criado no mesmo grupo de recursos e assinatura que seu aplicativo de funções. Depois que o recurso for criado, feche a janela do Application Insights.

1. Em seu aplicativo de funções, clique em **Configuração** na opção **Configurações** e selecione **Configurações do aplicativo**. Se você vir uma configuração chamada `APPINSIGHTS_INSTRUMENTATIONKEY`, isso significa que a integração do Application Insights está habilitada para seu aplicativo de funções em execução no Azure.

[Portal do Azure]: https://portal.azure.com
