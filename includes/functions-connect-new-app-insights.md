---
title: incluir arquivo
description: incluir arquivo
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 266cd52117f36b282fdd4bc8615a15e451cc203f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132626"
---
As funções facilitam a adição da integração do Application Insights a um aplicativo de funções do [portal do Azure].

1. No portal do [portal][do Azure], `Function Apps` digite na barra de pesquisa na parte superior da página, escolha seu aplicativo de funções e, em seguida, selecione a faixa **Application insights não está configurada** na parte superior da janela. Se você não vir essa faixa, seu aplicativo já terá Application Insights habilitado.

    ![Habilitar o Application Insights no portal](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Crie um recurso do Application Insights usando as configurações especificadas na tabela abaixo da imagem.

   ![Criar um recurso do Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome de aplicativo exclusivo | É mais fácil usar o mesmo nome que seu aplicativo de funções, que deve ser exclusivo em sua assinatura. | 
    | **Local** | Europa Ocidental | Se possível, use a mesma [região](https://azure.microsoft.com/regions/) que seu aplicativo de funções ou uma região próxima dela. |

1. Selecione **OK**. O recurso do Application Insights é criado no mesmo grupo de recursos e assinatura que seu aplicativo de funções. Depois que o recurso for criado, feche a janela do Application Insights.

1. No aplicativo de funções, selecione **Configurações do aplicativo** e role para baixo até **Configurações do aplicativo**. Se você vir uma configuração chamada `APPINSIGHTS_INSTRUMENTATIONKEY`, isso significa que a integração do Application Insights está habilitada para seu aplicativo de funções em execução no Azure.

[Portal do Azure]: https://portal.azure.com
