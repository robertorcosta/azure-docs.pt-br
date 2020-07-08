---
title: Obter QnA Maker de resposta padrão
description: A resposta padrão é retornada quando não há nenhuma correspondência com a pergunta. Talvez você queira alterar a resposta padrão da resposta padrão standard.
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979956"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Alterar a resposta padrão para um recurso de QnA Maker

A resposta padrão para uma base de dados de conhecimento deve ser retornada quando uma resposta não for encontrada. Se você estiver usando um aplicativo cliente, como o [serviço de bot do Azure](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), ele também poderá ter uma resposta padrão separada, indicando que nenhuma resposta atende ao limite de pontuação.

## <a name="set-default-answer-when-you-create-knowledge-base"></a>Definir a resposta padrão ao criar a base de dados de conhecimento

Quando você cria uma nova base de dados de conhecimento, o texto de resposta padrão é uma das configurações. Se você optar por não defini-lo durante o processo de criação, poderá alterá-lo posteriormente com o procedimento a seguir.

## <a name="change-default-answer-in-qna-maker-portal"></a>Alterar a resposta padrão no portal de QnA Maker

A resposta padrão da base de dados de conhecimento é retornada quando nenhuma resposta é retornada do serviço de QnA Maker.

1. Entre no portal de [QnA Maker](https://www.qnamaker.ai/) e selecione sua base de dados de conhecimento na lista.
1. Selecione **configurações** na barra de navegação.
1. Altere o valor do **texto de resposta padrão** na seção **gerenciar base de dados de conhecimento** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Captura de tela do portal QnA Maker, página configurações, com a caixa de texto resposta padrão realçada.":::

1. Selecione **salvar e treinar** para salvar a alteração.

## <a name="next-steps"></a>Próximas etapas

* [Criar uma base de dados de conhecimento](../How-to/manage-knowledge-bases.md)