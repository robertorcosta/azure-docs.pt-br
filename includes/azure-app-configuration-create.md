---
title: incluir arquivo
description: incluir arquivo
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 12/03/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: ccd2a0ded1cd915dc52dea1449d75136bbef18fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75413427"
---
1. Para criar um novo repositório de Configurações de Aplicativo, entre no [portal do Azure](https://portal.azure.com). No canto superior esquerdo da Página Inicial, selecione **Criar um recurso**. Na caixa **Pesquisar no Marketplace**, insira **Configuração de Aplicativos** e selecione Enter.

    ![Pesquisar a Configuração de Aplicativo](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

1. Selecione **Configuração de Aplicativo** nos resultados da pesquisa e, em seguida, selecione **Criar**.

1. No painel **Configuração de Aplicativos** > **Criar**, insira as configurações a seguir:

    | Configuração | Valor sugerido | DESCRIÇÃO |
    |---|---|---|
    | **Nome do recurso** | Nome globalmente exclusivo | Insira um nome exclusivo do recurso a ser usado para o recurso do repositório de Configurações de Aplicativo. O nome deve ser uma cadeia de caracteres com 1 a 63 caracteres e deve conter somente números, letras e o caractere `-`. O nome não pode iniciar ou terminar com o caractere `-` e caracteres `-` consecutivos não são válidos.  |
    | **Assinatura** | Sua assinatura | Selecione a assinatura do Azure que deseja usar para testar a Configuração de Aplicativo. Se a conta tiver apenas uma assinatura, ela será selecionada automaticamente e a lista **Assinatura** não será exibida. |
    | **Grupo de recursos** | *AppConfigTestResources* | Selecione ou crie um grupo de recursos para o recurso do repositório de Configurações de Aplicativo. Esse grupo é útil para organizar vários recursos que você pode querer excluir ao mesmo tempo, excluindo o grupo de recursos. Para saber mais, veja [Usar os grupos de recursos para gerenciar seus recursos do Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Localidade** | *Centro dos EUA* | Use **Localização** para especificar a localização geográfica na qual o repositório de configurações de aplicativo está hospedado. Para obter o melhor desempenho, crie o recurso na mesma região que outros componentes do aplicativo. |

    ![Criar um recurso do repositório de Configurações de Aplicativo](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

1. Selecione **Criar**. A implantação pode levar alguns minutos.

1. Após a conclusão da implantação, selecione **Configurações** > **Chaves de acesso**. Anote a cadeia de conexão de chave somente leitura primária. Você usará essa cadeia de conexão posteriormente para configurar o aplicativo a comunicar-se com o repositório de Configurações de Aplicativo que você criou.
