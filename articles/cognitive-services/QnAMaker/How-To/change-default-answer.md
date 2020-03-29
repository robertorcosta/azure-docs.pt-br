---
title: Obter resposta padrão - QnA Maker
description: A resposta padrão é devolvida quando não há correspondência com a pergunta. Você pode querer alterar a resposta padrão da resposta padrão padrão.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843270"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Defina a resposta padrão para uma base de conhecimento

A resposta padrão é devolvida quando não há correspondência com a pergunta. Você pode querer alterar a resposta padrão da resposta padrão padrão.

## <a name="change-default-answer"></a>Alterar a resposta padrão

1. Vá para o [portal do Azure](https://portal.azure.com) e navegue até o grupo de recursos que representa o serviço QnA Maker que você criou.

2. Clique para abrir o **Serviço de Aplicativo**.

    ![No portal do Azure, acesse o serviço de aplicativo para o QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Clique em **Configurações do Aplicativo** e edite o campo **DefaultAnswer** para a resposta padrão desejada. Clique em **Salvar**.

    ![Selecione Configurações de aplicativo e, em seguida, edite DefaultAnswer para o QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Reinicie o serviço Aplicativo

    ![Depois de alterar o DefaultAnswer, reinicie o serviço de aplicativo do QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Próximas etapas

* [Crie um bot com QnA Maker e LUIS](../tutorials/integrate-qnamaker-luis.md)