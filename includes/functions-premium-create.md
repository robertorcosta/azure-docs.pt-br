---
title: incluir arquivo
description: incluir arquivo
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 10/25/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: fca8c3b0b1bd4d22720a6d15313e297d05b7fac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76020966"
---
1. Abra o portal Azure de[https://portal.azure.com](https://portal.azure.com)

1. Selecione o botão **Criar um recurso**

    ![Criar recurso](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. Selecione o aplicativo de função de **computação** > **Function App**.

    ![Criar um aplicativo de funções no portal do Azure](./media/functions-premium-create/function-app-create-start.png)

1. Use as configurações do aplicativo de funções especificadas na tabela abaixo da imagem.

    ![Noções básicas](./media/functions-premium-create/function-app-create-basics.png)

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual este novo aplicativo de funções será criado. |
    | **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do novo grupo de recursos no qual criar o seu aplicativo de funções. |
    | **Nome do aplicativo de funções** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. Os caracteres válidos são `a-z` (não diferencia maiúsculas de minúsculas), `0-9` e `-`.  |
    |**Publicar**| Código | Opção para publicar arquivos de código ou um contêiner do Docker. |
    | **Pilha de runtime** | Linguagem preferencial | Escolha um runtime compatível com sua linguagem de programação de funções favorita. Escolha **.NET** para funções C# e F#. |
    |**Região**| Região preferencial | Escolha uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. |

    Selecione o botão **Seguinte : Hospedagem >.**

1. Digite as seguintes configurações de hospedagem.

    ![Hosting](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../articles/storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento usada pelo seu aplicativo de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Use também uma conta existente, que precisará atender aos [requisitos da conta de armazenamento](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Sistema Operacional**| Sistema operacional preferencial | Um sistema operacional é pré-selecionado para você com base na seleção da pilha de runtime, mas você pode alterar a configuração, se necessário. |
    | **[Plano](../articles/azure-functions/functions-scale.md)** | Premium | Para Tipo de plano, selecione **Premium (Visualização)** e selecione padrões para as seleções *De Plano do Windows* e *Sku e tamanho.* |

    Selecione o **botão Seguinte : Monitoramento >.**

1. Digite as seguintes configurações de monitoramento.

    ![Monitoramento](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Insights de aplicativos](../articles/azure-functions/functions-monitoring.md)** | Padrão | Cria um recurso do Application Insights do mesmo *nome do aplicativo* na região com suporte mais próxima. Ao expandir essa configuração, você pode alterar o **Novo nome do recurso** ou escolher um **Local** diferente em uma [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde deseja armazenar seus dados. |

    Selecione **Examinar + Criar** para examinar as seleções de configuração de aplicativo.

1. Selecione **Criar** para provisionar e implantar o aplicativo de funções.