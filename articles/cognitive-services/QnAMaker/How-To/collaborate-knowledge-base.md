---
title: Colaborando na base de dados de conhecimento-QnA Maker
description: O QnA Maker permite que várias pessoas colaborem em uma base de dados de conhecimento. Esse recurso é fornecido com o Controle de Acesso Baseado em Função do Azure.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 1a2908b4b65017f427682627ce5d83b186956a58
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650791"
---
# <a name="collaboration-with-authors-and-editors"></a>Colaboração com autores e editores

A colaboração é fornecida no nível de recurso QnA Maker para permitir que você restrinja o acesso do colaborador com base na função do colaborador. Saiba mais sobre os [conceitos](../Concepts/role-based-access-control.md)de autenticação do colaborador do QnA Maker.

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>Adicionar o RBAC (acesso baseado em função) ao recurso QnA Maker

QnA Maker permite que várias pessoas colaborem em todas as bases de dados de conhecimento no mesmo recurso de QnA Maker. Esse recurso é fornecido com o [Controle de Acesso Baseado em Função](../../../active-directory/role-based-access-control-configure.md) do Azure.

## <a name="access-at-the-qna-maker-resource-level"></a>Acesso no nível de recurso QnA Maker

Não é possível compartilhar uma base de dados de conhecimento específica em um serviço QnA Maker. Se você quiser um controle de acesso mais granular, considere distribuir suas bases de dados de conhecimento em diferentes QnA Maker recursos e, em seguida, adicionar funções a cada recurso.

## <a name="add-role-to-resource"></a>Adicionar função ao recurso

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Adicionar uma conta de usuário ao recurso de QnA Maker

As etapas a seguir usam a função de colaborador, mas qualquer uma das [funções](../reference-role-based-access-control.md) pode ser adicionada usando estas etapas

1. Entre no portal [do Azure](https://portal.azure.com/) e vá para o recurso QnA Maker.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Abra a guia **Controle de Acesso (IAM)**.

    ![IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Selecione **Adicionar**.

    ![Adicionar IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Selecione uma função na lista a seguir:

    |Função|
    |--|
    |Proprietário|
    |Colaborador|
    |Leitor de QnA Maker|
    |Editor de QnA Maker|
    |Usuário dos Serviços Cognitivos|

    ![Adicionar função no IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Insira o endereço de email do usuário e pressione **salvar**.

    ![Adicionar no IAM do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Quando a pessoa que você compartilhou seu serviço de QnA Maker com logs no [portal de QnA Maker](https://qnamaker.ai), ele pode ver todas as bases de dados de conhecimento nesse serviço com base em sua função.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Testar uma base de dados de conhecimento](./test-knowledge-base.md)

Saiba mais sobre a colaboração:
* Controle de acesso baseado em função [do Azure](../../../active-directory/role-based-access-control-configure.md)
* QnA Maker [conceitos](../Concepts/role-based-access-control.md) de controle de acesso baseado em função
