---
title: Criar uma função no Azure disparada pelo Armazenamento de Blobs
description: Use Azure Functions para criar uma função sem servidor que é invocada por itens adicionados a um contêiner de armazenamento de BLOBs.
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: bf6865d2756579f457dded90b247326d2eec137c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122923"
---
# <a name="create-a-function-in-azure-thats-triggered-by-blob-storage"></a>Criar uma função no Azure que é disparada pelo armazenamento de BLOBs

Saiba como criar uma função disparada quando os arquivos são carregados ou atualizados em um contêiner de armazenamento de BLOBs.

## <a name="prerequisites"></a>Pré-requisitos

+ Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar um Aplicativo de funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Você criou com êxito seu novo aplicativo de funções.

:::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-create-success.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

Em seguida, crie uma nova função no novo aplicativo de funções.

<a name="create-function"></a>

## <a name="create-an-azure-blob-storage-triggered-function"></a>Criar uma função disparada pelo armazenamento de BLOBs do Azure

1. Selecione **funções**e, em seguida, selecione **+ Adicionar** para adicionar uma nova função.

   :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-template.png" alt-text="Escolha um modelo de função no portal do Azure." border="true":::

1. Escolha o modelo de **gatilho do armazenamento de BLOBs do Azure** .

1. Use as configurações conforme especificado na tabela abaixo da imagem.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png" alt-text="Nomeie e configure a função disparada pelo armazenamento de BLOBs." border="true":::

    | Configuração | Valor sugerido | Descrição |
    |---|---|---|
    | **Nova função** | Exclusivo no aplicativo de funções | O nome dessa função disparada pelo blob. |
    | **Caminho**   | samples-workitems/{name}    | Local no Armazenamento de Blobs que está sendo monitorada. O nome do arquivo do blob é passado na associação como o parâmetro _name_.  |
    | **Conexão da conta de armazenamento** | AzureWebJobsStorage | Você pode usar a conexão da conta de armazenamento que já está sendo usada por seu aplicativo de funções ou criar uma nova.  |

1. Selecione **criar função** para criar sua função.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-3.png" alt-text="Crie a função disparada pelo Armazenamento de Blobs." border="true":::

Em seguida, crie o contêiner **Samples-WorkItems** .

## <a name="create-the-container"></a>Criar o contêiner

1. Em sua função, na página **visão geral** , selecione o grupo de recursos.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-resource-group.png" alt-text="Selecione seu grupo de recursos portal do Azure." border="true":::

1. Localize e selecione a conta de armazenamento do grupo de recursos.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-account-access.png" alt-text="Acesse a conta de armazenamento." border="true":::

1. Escolha **contêineres**e, em seguida, escolha **+ contêiner**. 

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-add-container.png" alt-text="Adicione o contêiner à sua conta de armazenamento no portal do Azure." border="true":::

1. No campo **nome** , digite `samples-workitems` e, em seguida, selecione **criar**.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-name-blob-container.png" alt-text="Nomeie o contêiner de armazenamento." border="true":::

Agora que você tem um contêiner de blob, você pode testar a função carregando um arquivo para o contêiner.

## <a name="test-the-function"></a>Testar a função

1. De volta ao portal do Azure, navegue até sua função expanda os **logs** na parte inferior da página e verifique se o streaming de log não está pausado.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-log-expander.png" alt-text="Expanda o log na portal do Azure." border="true":::

1. Em uma janela separada do navegador, vá para o grupo de recursos na portal do Azure e selecione a conta de armazenamento.

1. Selecione **contêineres**e, em seguida, selecione o contêiner **Samples-WorkItems** .

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-container.png" alt-text="Vá para o contêiner Samples-WorkItems no portal do Azure." border="true":::

1. Selecione **carregar**e, em seguida, selecione o ícone de pasta para escolher um arquivo a ser carregado.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png" alt-text="Carregue um arquivo para o contêiner de blob." border="true":::

1. Navegue até um arquivo no computador local, como um arquivo de imagem, escolha o arquivo. Selecione **abrir** e **carregar**.

1. Volte para os logs de função e verifique se o blob foi lido.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png" alt-text="Exiba a mensagem nos logs." border="true":::

    >[!NOTE]
    > Quando seu aplicativo de funções é executado no plano de consumo padrão, pode haver um atraso de até vários minutos entre o blob que está sendo adicionado ou atualizado e a função sendo disparada. Se você precisar de baixa latência em suas funções disparadas por blob, considere executar seu aplicativo de funções em um Plano do Serviço de Aplicativo.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou uma função que é executada quando um blob é adicionado a um Armazenamento de Blobs ou atualizado nele. Para obter mais informações sobre gatilhos de armazenamento de blobs, consulte [Associações de Armazenamento de Blobs do Azure Functions](functions-bindings-storage-blob.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
