---
title: Obter QnA Maker de resposta padrão
description: A resposta padrão é retornada quando não há nenhuma correspondência com a pergunta. Talvez você queira alterar a resposta padrão da resposta padrão standard.
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097091"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Alterar a resposta padrão para um recurso de QnA Maker

A resposta padrão é retornada quando não há nenhuma correspondência com a pergunta. Talvez você queira alterar a resposta padrão da resposta padrão standard.

## <a name="change-default-answer-in-the-azure-portal"></a>Alterar a resposta padrão no portal do Azure

1. Vá para o [portal do Azure](https://portal.azure.com) e navegue até o grupo de recursos que representa o serviço QnA Maker que você criou.

2. Clique para abrir o **Serviço de Aplicativo**.

    ![No portal do Azure, acesse o serviço de aplicativo para o QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Clique em **Configurações do Aplicativo** e edite o campo **DefaultAnswer** para a resposta padrão desejada. Clique em **Salvar**.

    ![Selecione Configurações de aplicativo e, em seguida, edite DefaultAnswer para o QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Reinicie o serviço Aplicativo

    ![Depois de alterar o DefaultAnswer, reinicie o serviço de aplicativo do QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Próximas etapas

* [Criar uma base de dados de conhecimento](../How-to/manage-knowledge-bases.md)