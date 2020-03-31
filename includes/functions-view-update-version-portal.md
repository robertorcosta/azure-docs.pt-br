---
title: incluir arquivo
description: incluir arquivo
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67172571"
---
Use o procedimento a seguir para exibir e atualizar a versão de runtime usada no momento por um aplicativo de funções.

1. No [portal do Azure](https://portal.azure.com), navegue até o aplicativo de funções.

1. Em **Recursos Configurados**, escolha **Configurações do aplicativo de funções**.

    ![Selecionar as configurações do aplicativo de funções](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. Na guia **Configurações do aplicativo de funções**, localize a **Versão de runtime**. Observe a versão de runtime específica e a versão principal solicitada. No exemplo a seguir, a versão é definida como `~2`.

   ![Selecionar as configurações do aplicativo de funções](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Para fixar o aplicativo de funções no runtime de versão 1.x, escolha **~1** em **Versão de runtime**. Essa opção estará desabilitada quando houver funções no aplicativo.

1. Ao alterar a versão de runtime, retorne à guia **Visão geral** e escolha **Reiniciar** para reiniciar o aplicativo.  O aplicativo de funções reinicia a execução no runtime de versão 1.x e os modelos de versão 1.x são usados quando você cria funções.