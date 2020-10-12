---
title: Colaborando na base de dados de conhecimento-QnA Maker
description: O QnA Maker permite que várias pessoas colaborem em uma base de dados de conhecimento. Esse recurso é fornecido com o controle de acesso baseado em função do Azure (RBAC do Azure).
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 8adc02d4b397c2b0bdaf5d3998cfbe229c896d41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776843"
---
# <a name="collaboration-with-authors-and-editors"></a>Colaboração com autores e editores

A colaboração é fornecida no nível de recurso QnA Maker para permitir que você restrinja o acesso do colaborador com base na função do colaborador. Saiba mais sobre os [conceitos](../Concepts/role-based-access-control.md)de autenticação do colaborador do QnA Maker.

## <a name="add-azure-role-based-access-control-azure-rbac-to-your-qna-maker-resource"></a>Adicionar o Azure RBAC (controle de acesso baseado em função) ao recurso QnA Maker

QnA Maker permite que várias pessoas colaborem em todas as bases de dados de conhecimento no mesmo recurso de QnA Maker. Esse recurso é fornecido com o [controle de acesso baseado em função do Azure (RBAC do Azure)](../../../active-directory/role-based-access-control-configure.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Acesso no nível de recurso QnA Maker

Não é possível compartilhar uma base de dados de conhecimento específica em um serviço QnA Maker. Se você quiser um controle de acesso mais granular, considere distribuir suas bases de dados de conhecimento em diferentes QnA Maker recursos e, em seguida, adicionar funções a cada recurso.

## <a name="add-role-to-resource"></a>Adicionar função ao recurso

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Adicionar uma conta de usuário ao recurso de QnA Maker

As etapas a seguir usam a função de colaborador, mas qualquer uma das [funções](../reference-role-based-access-control.md) pode ser adicionada usando estas etapas

1. Entre no portal [do Azure](https://portal.azure.com/) e vá para o recurso QnA Maker.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Abra a guia **Controle de Acesso (IAM)**.

    ![IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Selecione **Adicionar**.

    ![Adicionar IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Selecione uma função na lista a seguir:

    |Função|
    |--|
    |Proprietário|
    |Colaborador|
    |QnA Maker leitor de serviços cognitivas|
    |Editor de QnA Maker de serviços cognitivas|
    |Usuário dos Serviços Cognitivos|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker Adicionar função IAM.":::

1. Insira o endereço de email do usuário e pressione **salvar**.

    ![Adicionar no IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Exibir QnA Maker base de dados de conhecimento

Quando a pessoa que você compartilhou seu serviço de QnA Maker com logs no [portal de QnA Maker](https://qnamaker.ai), ele pode ver todas as bases de dados de conhecimento nesse serviço com base em sua função.

Quando eles selecionam uma base de dados de conhecimento, sua função atual nesse QnA Maker recurso fica visível ao lado do nome da base de dados de conhecimento.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="QnA Maker Adicionar função IAM.":::

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Testar uma base de dados de conhecimento](./test-knowledge-base.md)

Saiba mais sobre a colaboração:
* Controle de acesso baseado em função [do Azure](../../../active-directory/role-based-access-control-configure.md)
* QnA Maker [conceitos](../Concepts/role-based-access-control.md) de controle de acesso baseado em função
