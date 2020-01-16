---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4860532e59227618ce819772887556719ecb53fc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021024"
---
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Publicar**.

1. Na caixa de diálogo **publicar** , selecione **Microsoft Azure serviço de aplicativo**, escolha **criar novo**e, em seguida, selecione **publicar**.

   ![Escolher destino de publicação](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Na caixa de diálogo **Criar serviço de aplicativo** , use as configurações de hospedagem conforme especificado na tabela abaixo da imagem:

    ![Criar caixa de diálogo do Serviço de Aplicativo](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Configuração      | Valor sugerido  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome do aplicativo** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções de forma exclusiva. |
    | **Assinatura** | Escolha sua assinatura | A assinatura do Azure a utilizar. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual criar o seu aplicativo de funções. Escolha **Novo** para criar um novo grupo de recursos.|
    | **[Plano de hospedagem](../articles/app-service/overview-hosting-plans.md)** | Plano do Serviço de Aplicativo | Um [plano do Serviço de Aplicativo](../articles/app-service/overview-hosting-plans.md) especifica o local, tamanho e recursos do farm de servidores Web que hospeda o aplicativo. Você pode economizar dinheiro ao hospedar vários aplicativos configurando os aplicativos Web para compartilhar um único plano do Serviço de Aplicativo. Os planos do serviço de aplicativo definem a região, o tamanho da instância, a contagem de escala e a SKU (gratuito, compartilhado, básico, Standard ou Premium). Escolha **novo** para criar um novo plano do serviço de aplicativo. |

1. Clique em **criar** para criar um WebJob e recursos relacionados no Azure com essas configurações e implantar o código do projeto.