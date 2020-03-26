---
title: Criar uma função executada segundo uma agenda no Azure
description: Saiba como criar uma função no Azure que é executada com base em uma agendamento definido por você.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: quickstart
ms.date: 03/28/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 808f0f81f937da688a8873e5f6ee959976e9d6aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75769278"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Criar uma função no Azure que é disparada por um temporizador

Saiba como usar o Azure Functions para criar uma função [sem servidor](https://azure.microsoft.com/solutions/serverless/) que é executada com base em um agendamento definido por você.

![Criar um aplicativo de funções no portal do Azure](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial:

+ Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar um Aplicativo de funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplicativo de funções criado com êxito.](./media/functions-create-first-azure-function/function-app-create-success.png)

Em seguida, crie uma nova função no novo aplicativo de funções.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Criar uma função disparada por temporizador

1. Expanda seu aplicativo de funções e clique no botão **+** ao lado de **Functions**. Se essa for a primeira função em seu aplicativo de funções, selecione **No portal** e depois **Continuar**. Caso contrário, vá para a etapa 3.

   ![Página de início rápido de funções no portal do Azure](./media/functions-create-scheduled-function/function-app-quickstart-choose-portal.png)

2. Escolha **Mais modelos** e, em seguida, **Concluir e exibir modelos**.

    ![Início Rápido do Functions, escolher mais modelos](./media/functions-create-scheduled-function/add-first-function.png)

3. No campo de pesquisa, digite `timer` e defina o novo gatilho com as configurações especificadas na tabela abaixo da imagem.

    ![Criar uma função disparada pelo temporizador no portal do Azure.](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Configuração | Valor sugerido | DESCRIÇÃO |
    |---|---|---|
    | **Nome** | Padrão | Define o nome da sua função disparada por temporizador. |
    | **Agenda** | 0 \*/1 \* \* \* \* | Uma [expressão CRON](functions-bindings-timer.md#ncrontab-expressions) de seis campos que agenda sua função para ser executada a cada minuto. |

4. Clique em **Criar**. Uma função é criada na linguagem de programação escolhida que é executada a cada minuto, no minuto.

5. Verifique a execução, exibindo informações de rastreamento gravadas nos logs.

    ![Visualizador de log de função no Portal do Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

Agora você altera o agendamento da função para que ela seja executada uma vez por hora em vez de uma vez por minuto.

## <a name="update-the-timer-schedule"></a>Atualizar o agendamento do temporizador

1. Expanda sua função e clique em **Integrar**. É aqui que você define as associações de entrada e saída de sua função e também define o agendamento. 

2. Insira um novo valor de **Agendamento** por hora de `0 0 */1 * * *` e depois clique em **Salvar**.  

![As funções atualizam o agendamento do temporizador no Portal do Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Agora você tem uma função que é executada uma vez a cada hora, na hora.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou uma função que é executada segundo um agendamento. Para obter mais informações sobre gatilhos de temporizador, confira [Agendar a execução de código com o Azure Functions](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
