---
title: Crie um recurso do Personalizador
description: A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço faz explorações, com que frequência o modelo é treinado novamente e quantos dados são armazenados.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336053"
---
# <a name="create-a-personalizer-resource"></a>Crie um recurso Personalizador

Um recurso Personalizador é a mesma coisa que um loop de aprendizagem do Personalizador. Um único recurso, ou loop de aprendizado, é criado para cada domínio de assunto ou área de conteúdo que você tem. Não use várias áreas de conteúdo no mesmo loop porque isso confundirá o ciclo de aprendizado e fornecerá previsões ruins.

Se você quiser que o Personalr selecione o melhor conteúdo para mais de uma área de conteúdo de uma página da Web, use um loop de aprendizado diferente para cada um.


## <a name="create-a-resource-in-the-azure-portal"></a>Crie um recurso no portal do Azure

Crie um recurso do Personalizador para cada loop de comentários.

1. Faça login no [portal Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). O link anterior leva você à página **Criar** do serviço do Personalizador.
1. Insira o nome do seu serviço, selecione uma assinatura, um local, um tipo de preço e um grupo de recursos.

    > [!div class="mx-imgBorder"]
    > ![Use o portal Azure para criar o recurso Personalr, também chamado de loop de aprendizado.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Selecione **Criar** para criar o recurso.

1. Depois que seu recurso for implantado, selecione o botão **Ir para Recurso** para ir ao recurso Personalizador.

1. Selecione a **página inicial rápida** para o seu recurso e copie os valores para o ponto final e a chave. Você precisa do ponto final de recurso e da chave para usar as APIs Rank e Reward.

1. Selecione a **página Configuração** para o novo recurso para [configurar o loop de aprendizagem](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Crie um recurso com o Azure CLI

1. Faça login na CLI do Azure com o seguinte comando:

    ```azurecli-interactive
    az login
    ```

1. Crie um grupo de recursos, um agrupamento lógico para gerenciar todos os recursos do Azure que você pretende usar com o recurso Personalr.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Crie um novo recurso Personalr, _loop de aprendizagem,_ com o seguinte comando para um grupo de recursos existente.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Isso retorna um objeto JSON, que inclui o ponto **final do recurso**.

1. Use o seguinte comando Azure CLI para obter sua **chave de recursos**.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Você precisa do ponto final de recurso e da chave para usar as APIs Rank e Reward.

## <a name="next-steps"></a>Próximas etapas

* [Configurar](how-to-settings.md) Loop de aprendizado de personalizador