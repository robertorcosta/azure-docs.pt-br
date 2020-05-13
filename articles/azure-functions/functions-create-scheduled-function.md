---
title: Criar uma função executada segundo uma agenda no Azure
description: Saiba como criar uma função no Azure que é executada com base em uma agendamento definido por você.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: be539efdb66b0a9bda583960484f40fae1e18235
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123361"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Criar uma função no Azure que é disparada por um temporizador

Saiba como usar o Azure Functions para criar uma função [sem servidor](https://azure.microsoft.com/solutions/serverless/) que é executada com base em um agendamento definido por você.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar um Aplicativo de funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Seu novo aplicativo de funções está pronto para uso. Em seguida, você criará uma função no novo aplicativo de funções.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Criar uma função disparada por temporizador

1. Em seu aplicativo de funções, selecione **funções**e, em seguida, selecione **+ Adicionar** 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="Adicione uma função no portal do Azure." border="true":::

1. Selecione o modelo de **gatilho de temporizador** . 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="Selecione o gatilho de temporizador no portal do Azure." border="true":::

1. Configure o novo gatilho com as configurações conforme especificado na tabela abaixo da imagem e, em seguida, selecione **criar função**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="Selecione o gatilho de temporizador no portal do Azure." border="true":::
    
    | Configuração | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome** | Padrão | Define o nome da sua função disparada por temporizador. |
    | **Agenda** | 0 \* /1 \* \* \*\* | Uma [expressão CRON](functions-bindings-timer.md#ncrontab-expressions) de seis campos que agenda sua função para ser executada a cada minuto. |

## <a name="test-the-function"></a>Testar a função

1. Em sua função, selecione **código + teste** e expanda os logs.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="Teste o gatilho de temporizador no portal do Azure." border="true":::

1. Verifique a execução exibindo as informações gravadas nos logs.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="Exiba o gatilho de temporizador no portal do Azure." border="true":::

Agora você altera o agendamento da função para que ela seja executada uma vez por hora em vez de uma vez por minuto.

## <a name="update-the-timer-schedule"></a>Atualizar o agendamento do temporizador

1. Em sua função, selecione **integração**. Aqui, você define as associações de entrada e saída para sua função e também define a agenda. 

1. Selecione **temporizador (MyTimer)**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="Atualize a agenda do temporizador no portal do Azure." border="true":::

1. Atualize o valor de **agendamento** para `0 0 */1 * * *` e, em seguida, selecione **salvar**.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="As funções atualizam o agendamento do temporizador no Portal do Azure." border="true":::

Agora você tem uma função que é executada uma vez a cada hora, na hora.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou uma função que é executada com base em uma agenda. Para obter mais informações sobre gatilhos de temporizador, confira [Agendar a execução de código com o Azure Functions](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
