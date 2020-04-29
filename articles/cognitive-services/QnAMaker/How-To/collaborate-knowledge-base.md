---
title: Colaborando na base de dados de conhecimento-QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker permite que várias pessoas colaborem em uma base de dados de conhecimento. Esse recurso é fornecido com o Controle de Acesso Baseado em Função do Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "75660742"
---
# <a name="collaborate-on-your-knowledge-base"></a>Colaborar com sua base de dados de conhecimento

QnA Maker permite que várias pessoas colaborem em todas as bases de dados de conhecimento no mesmo recurso de QnA Maker. Esse recurso é fornecido com o [Controle de Acesso Baseado em Função](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) do Azure.

Execute as seguintes etapas para compartilhar seu serviço QnA Maker com alguém:

1. Entre no portal do Azure e vá para o recurso de QnA Maker.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Abra a guia **Controle de Acesso (IAM)**.

    ![IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Selecione **Adicionar**.

    ![Adicionar IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Selecione a função de **Proprietário** ou **Colaborador**. Você não pode conceder acesso somente leitura por meio do Controle de Acesso Baseado em Função. As funções de proprietário e colaborador têm permissões de acesso de leitura/gravação para o serviço de QnA Maker.

    ![Adicionar função no IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Insira o endereço de email do usuário e pressione **salvar**.

    ![Adicionar no IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Quando a pessoa, você compartilhou seu serviço de QnA Maker com, faz logon no [portal de QnA Maker](https://qnamaker.ai) eles podem ver todas as bases de dados de conhecimento nesse serviço.

Lembre-se de que não é possível compartilhar uma base de Conhecimento específica em um serviço do QnA Maker. Se desejar um controle de acesso mais granular, considere distribuir as bases de dados de conhecimento em diferentes serviços do QnA Maker.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Testar uma base de dados de conhecimento](./test-knowledge-base.md)
