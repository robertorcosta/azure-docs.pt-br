---
title: Criar uma função no Azure disparada por mensagens na fila
description: Use o Azure Functions para criar uma função sem servidor que é invocada por uma mensagem enviada para uma fila do Armazenamento do Azure.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 98f0290aad9971bdb0c0b265d96e96d8ac34b99d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756509"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Criar uma função disparada pelo Armazenamento de Filas do Azure

Saiba como criar uma função que é disparada quando as mensagens são enviadas para uma fila do Armazenamento do Azure.

![Exiba a mensagem nos logs.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Prerequisites

- Baixe e instale o [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/).

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar um Aplicativo de funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplicativo de funções criado com êxito.](./media/functions-create-first-azure-function/function-app-create-success.png)

Em seguida, crie uma nova função no novo aplicativo de funções.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Criar uma função disparada por Filas

1. Expanda seu aplicativo de funções e clique no botão **+** ao lado de **Functions**. Se essa for a primeira função em seu aplicativo de funções, selecione **No portal** e depois **Continuar**. Caso contrário, vá para a etapa três.

   ![Página de início rápido de funções no portal do Azure](./media/functions-create-storage-queue-triggered-function/function-app-quickstart-choose-portal.png)

1. Escolha **Mais modelos** e, em seguida, **Concluir e exibir modelos**.

    ![Início Rápido do Functions, escolher mais modelos](./media/functions-create-storage-queue-triggered-function/add-first-function.png)

1. No campo de pesquisa, digite `queue` e escolha o modelo **Gatilho de fila**.

1. Se solicitado, selecione **Instalar** para instalar a extensão do Armazenamento do Azure e as dependências no aplicativo de funções. Após a instalação ser bem-sucedida, selecione **Continuar**.

    ![Instalar extensões de associação](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)

1. Use as configurações conforme especificado na tabela abaixo da imagem.

    ![Configure a função disparada da fila de armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-2.png)

    | Configuração | Valor sugerido | DESCRIÇÃO |
    |---|---|---|
    | **Nome** | Exclusivo no aplicativo de funções | O nome dessa função disparada por filas. |
    | **Nome da fila**   | myqueue-items    | Nome da fila à qual se conectar em sua conta de armazenamento. |
    | **Conexão da conta de armazenamento** | AzureWebJobsStorage | Você pode usar a conexão da conta de armazenamento que já está sendo usada por seu aplicativo de funções ou criar uma nova.  |    

1. Clique em **Criar** para criar a função.

Em seguida, você pode se conectar à sua conta de armazenamento do Azure e criar a fila de armazenamento **myqueue-items**.

## <a name="create-the-queue"></a>Criar a fila

1. Em sua função, clique em **Integrar**, expanda **Documentação**e copie **Nome da conta** e **Chave de conta**. Você usa essas credenciais para conectar-se à conta de armazenamento no Gerenciador de Armazenamento do Microsoft Azure. Se você já tiver se conectado à conta de armazenamento, vá para a etapa 4.

    ![Obtenha as credenciais de conexão da conta de armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)

1. Execute a ferramenta [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/), clique no ícone conectar-se à esquerda, escolha **Usar um nome e chave de conta de armazenamento** e clique em **Avançar**.

    ![Execute a ferramenta Gerenciador de Conta de Armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)

1. Insira o **Nome da conta** e **Chave de conta** da etapa 1, clique em **Avançar** e em **Conectar**.

    ![Insira as credenciais de armazenamento e conecte-se.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

1. Expanda a conta de armazenamento anexada, clique com o botão direito do mouse em **Filas**, clique em **Criar Fila**, digite `myqueue-items` e pressione enter.

    ![Crie uma fila de armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Agora que você tem uma fila de armazenamento, você pode testar a função adicionando uma mensagem à fila.

## <a name="test-the-function"></a>Testar a função

1. De volta ao Portal do Azure, navegue até sua função, expanda os **Logs** na parte inferior da página e verifique se o streaming de log não está em pausa.

1. No Gerenciador de Armazenamento, expanda sua conta de armazenamento, **Filas** e **myqueue-items** e, em seguida, clique em **Adicionar Mensagem**.

    ![Adicione uma mensagem à fila.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

1. Digite sua mensagem "Olá, Mundo!" em **Texto da mensagem** e clique em **OK**.

1. Aguarde alguns segundos, depois volte para seus logs de função e verifique se a nova mensagem foi lida da fila.

    ![Exiba a mensagem nos logs.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

1. No Gerenciador de Armazenamento, clique em **Atualizar** e verifique se a mensagem foi processada e se não está mais na fila.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou uma função que é executada quando uma mensagem é adicionada a uma fila de armazenamento. Para obter mais informações sobre gatilhos de Armazenamento de Filas, consulte [Associações de fila do Armazenamento do Azure Functions](functions-bindings-storage-queue.md).

Agora que você criou sua primeira função, vamos adicionar uma associação de saída à função que grava uma mensagem de volta em outra fila.

> [!div class="nextstepaction"]
> [Adicionar mensagens a uma fila do Armazenamento do Azure usando o Functions](functions-integrate-storage-queue-output-binding.md)
