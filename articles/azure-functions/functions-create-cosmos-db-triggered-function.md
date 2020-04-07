---
title: Criar uma função disparada pelo Azure Cosmos DB
description: Use o Azure Functions para criar uma função sem servidor que seja invocada quando são adicionados dados a um banco de dados no Azure Cosmos DB.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: how-to
ms.date: 10/02/2018
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 6045c61dc9837667bfaf01c685f687fcf5816e4c
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754203"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Criar uma função disparada pelo Azure Cosmos DB

Saiba como criar uma função disparada quando dados são adicionados ou alterados no Azure Cosmos DB. Para obter mais informações sobre o Azure Cosmos DB, consulte [Azure Cosmos DB: computação de banco de dados sem servidor usando o Azure Functions](../cosmos-db/serverless-computing-database.md).

![Exiba a mensagem nos logs.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial:

+ Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Antes de criar o gatilho, você precisa ter uma conta do Azure Cosmos DB que use a API de SQL.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Criar um Aplicativo de funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, crie uma nova função no novo aplicativo de funções.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Criar gatilho do Azure Cosmos DB

1. Expanda seu aplicativo de funções e clique no botão **+** ao lado de **Functions**. Se essa for a primeira função em seu aplicativo de funções, selecione **No portal** e depois **Continuar**. Caso contrário, vá para a etapa três.

   ![Página de início rápido de funções no portal do Azure](./media/functions-create-cosmos-db-triggered-function/function-app-quickstart-choose-portal.png)

1. Escolha **Mais modelos** e, em seguida, **Concluir e exibir modelos**.

    ![Início Rápido do Functions, escolher mais modelos](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

1. No campo de pesquisa, digite `cosmos` e escolha o modelo **Gatilho do Azure Cosmos DB**.

1. Se solicitado, selecione **Instalar** para instalar a extensão do Azure Cosmos DB no aplicativo de funções. Após a instalação ser bem-sucedida, selecione **Continuar**.

    ![Instalar extensões de associação](./media/functions-create-cosmos-db-triggered-function/functions-create-cosmos-db-trigger-portal.png)

1. Configure o novo gatilho com as configurações conforme especificado na tabela abaixo da imagem.

    ![Criar a função acionada do Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)

    | Configuração      | Valor sugerido  | DESCRIÇÃO                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Nome** | Padrão | Use o nome da função padrão sugerido pelo modelo.|
    | **Conexão de conta do Azure Cosmos DB** | Nova configuração | Selecione **Novo** e depois escolha sua **Assinatura**, a **Conta de banco de dados** criada anteriormente e **Selecionar**. Isso cria uma configuração de aplicativo para sua conexão de conta. Essa configuração é usada pela associação para conexão com o banco de dados. |
    | **Nome do contêiner** | Itens | Nome do contêiner a ser monitorado. |
    | **Crie o contêiner de concessão se ele não existir** | Verificado | O contêiner ainda não existe. Portanto, crie-o. |
    | **Nome do banco de dados** | Tarefas | Nome do banco de dados com o contêiner a ser monitorado. |

1. Clique em **Criar** para criar o banco de dados da função disparada do Azure Cosmos DB. Depois que a função for criada, o código de função baseado em modelo será exibido.  

    ![Modelo de função do Cosmos DB em C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Esse modelo de função grava o número de documentos e a primeira ID de documento para os logs.

Em seguida, você se conecta à sua conta do Azure Cosmos DB e cria o contêiner `Items` no banco de dados `Tasks`.

## <a name="create-the-items-container"></a>Criar o contêiner Itens

1. Abra uma segunda instância do [portal do Azure](https://portal.azure.com) em uma nova guia no navegador.

1. No lado esquerdo do portal, expanda a barra de ícones, digite `cosmos` no campo de pesquisa e selecione **Azure Cosmos DB**.

    ![Pesquise o serviço do Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Escolha sua conta do Azure Cosmos DB e selecione o **Data Explorer**. 

1. Em **API SQL**, escolha o banco de dados **Tarefas** e selecione **Novo Contêiner**.

    ![Criar um contêiner](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. Em **Adicionar Contêiner**, use as configurações mostradas na tabela embaixo da imagem. 

    ![Definir o contêiner Tarefas](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | Configuração|Valor sugerido|DESCRIÇÃO |
    | ---|---|--- |
    | **ID do banco de dados** | Tarefas |O nome do novo banco de dados. Isso deve corresponder ao nome definido na sua associação de função. |
    | **ID do contêiner** | Itens | O nome do novo contêiner. Isso deve corresponder ao nome definido na sua associação de função.  |
    | **[Chave de partição](../cosmos-db/partition-data.md)** | /category|Uma chave de partição que distribui dados uniformemente para cada partição. É importante selecionar a chave de partição correta ao criar um contêiner de alto desempenho. | 
    | **Taxa de transferência** |400 RU| Use o valor padrão. Se quiser reduzir a latência, você poderá escalar verticalmente a taxa de transferência mais tarde. |    

1. Clique em **OK** para criar o contêiner Itens. Pode levar alguns instantes para o contêiner ser criado.

Depois que o contêiner especificado na associação de função existir, você poderá testar a função adicionando itens a esse novo contêiner.

## <a name="test-the-function"></a>Testar a função

1. Expanda o novo contêiner **Itens** no Data Explorer, escolha **Itens** e selecione **Novo Item**.

    ![Criar um item no contêiner Itens](./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png)

1. Substitua o conteúdo do novo item pelo conteúdo a seguir e então escolha **Salvar**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Mude para a primeira guia do navegador que contém a função no portal. Expanda os logs de função e verifique se o novo documento disparou a função. Veja se o valor de ID do documento `task1` é gravado nos logs. 

    ![Exiba a mensagem nos logs.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (Opcional) Volte para o seu documento, faça uma alteração e, em seguida, clique em **Atualizar**. Em seguida, volte para os logs de função e verifique se a atualização também disparou a função.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou uma função que é executada quando um documento é adicionado ou modificado no Azure Cosmos DB. Para obter mais informações sobre gatilhos do Azure Cosmos DB, consulte [Associações do Azure Cosmos DB para Azure Functions](functions-bindings-cosmosdb.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
