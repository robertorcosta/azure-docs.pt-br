---
title: Obter QnA Maker de resposta padrão
description: A resposta padrão é retornada quando não há nenhuma correspondência com a pergunta. Talvez você queira alterar a resposta padrão da resposta padrão standard.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843270"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Definir a resposta padrão para uma base de dados de conhecimento

A resposta padrão é retornada quando não há nenhuma correspondência com a pergunta. Talvez você queira alterar a resposta padrão da resposta padrão standard.

## <a name="change-default-answer"></a>Alterar a resposta padrão

1. Vá para o [portal do Azure](https://portal.azure.com) e navegue até o grupo de recursos que representa o serviço QnA Maker que você criou.

2. Clique para abrir o **Serviço de Aplicativo**.

    ![No portal do Azure, acesse o serviço de aplicativo para o QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Clique em **Configurações do Aplicativo** e edite o campo **DefaultAnswer** para a resposta padrão desejada. Clique em **Save** (Salvar).

    ![Selecione Configurações de aplicativo e, em seguida, edite DefaultAnswer para o QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Reinicie o serviço Aplicativo

    ![Depois de alterar o DefaultAnswer, reinicie o serviço de aplicativo do QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Próximos passos

* [Criar um bot com QnA Maker e LUIS](../tutorials/integrate-qnamaker-luis.md)