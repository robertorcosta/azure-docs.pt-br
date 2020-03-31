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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76021024"
---
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Publicar**.

1. Na caixa de diálogo **Publicar,** selecione **O serviço de aplicativos do Microsoft Azure,** escolha **Criar novo**e, em seguida, **selecione Publicar**.

   ![Escolha o alvo de publicação](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Na caixa de diálogo **Criar serviço de aplicativo,** use as configurações de hospedagem conforme especificado na tabela abaixo da imagem:

    ![Criar caixa de diálogo do Serviço de Aplicativo](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Configuração      | Valor sugerido  | Descrição                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome do aplicativo** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções de forma exclusiva. |
    | **Assinatura** | Escolha sua assinatura | A assinatura do Azure a utilizar. |
    | **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual criar o seu aplicativo de funções. Escolha **Novo** para criar um novo grupo de recursos.|
    | **[Plano de Hospedagem](../articles/app-service/overview-hosting-plans.md)** | Plano do Serviço de Aplicativo | Um [plano de Serviço de Aplicativo](../articles/app-service/overview-hosting-plans.md) especifica a localização, o tamanho e os recursos da fazenda do servidor web que hospeda seu aplicativo. Você pode economizar dinheiro ao hospedar vários aplicativos configurando os aplicativos Web para compartilhar um único plano do Serviço de Aplicativo. Os planos de serviço de aplicativo definem a região, o tamanho da ocorrência, a contagem de escalas e o SKU (Livre, Compartilhado, Básico, Padrão ou Premium). Escolha **o Novo** para criar um novo plano de serviço de aplicativo. |

1. Clique **em Criar** para criar um WebJob e recursos relacionados no Azure com essas configurações e implantar seu código de projeto.