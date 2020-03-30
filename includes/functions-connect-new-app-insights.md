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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132626"
---
As funções facilitam a adição da integração do Application Insights a um aplicativo de funções do [portal do Azure].

1. No [portal][Portal Azure], digite `Function Apps` a barra de pesquisa na parte superior da página, escolha seu aplicativo de função e, em seguida, selecione o banner do aplicativo **Insights não está configurado** na parte superior da janela. Se você não ver esse banner, então seu aplicativo já tem o Application Insights ativado.

    ![Habilitar o Application Insights no portal](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Crie um recurso do Application Insights usando as configurações especificadas na tabela abaixo da imagem.

   ![Criar um recurso do Application Insights](media/functions-connect-new-app-insights/ai-general.png)

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | Nome de aplicativo exclusivo | É mais fácil usar o mesmo nome que seu aplicativo de funções, que deve ser exclusivo em sua assinatura. | 
    | **Local** | Europa Ocidental | Se possível, use a mesma [região](https://azure.microsoft.com/regions/) que seu aplicativo de funções ou uma região próxima dela. |

1. Selecione **OK**. O recurso do Application Insights é criado no mesmo grupo de recursos e assinatura que seu aplicativo de funções. Depois que o recurso for criado, feche a janela do Application Insights.

1. No aplicativo de funções, selecione **Configurações do aplicativo** e role para baixo até **Configurações do aplicativo**. Se você vir uma configuração chamada `APPINSIGHTS_INSTRUMENTATIONKEY`, isso significa que a integração do Application Insights está habilitada para seu aplicativo de funções em execução no Azure.

[Azure Portal]: https://portal.azure.com
