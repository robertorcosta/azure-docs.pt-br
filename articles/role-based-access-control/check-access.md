---
title: Início rápido – Exibir o acesso que um usuário tem aos recursos do Azure
description: Neste Início rápido, saiba como exibir o acesso que um usuário ou outra entidade de segurança tem aos recursos do Azure usando o RBAC (controle de acesso baseado em função) e o portal do Azure.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b23c10fc2a551b8044b208911dbc048968b06564
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "74419610"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Início Rápido: Exibir o acesso que um usuário tem aos recursos do Azure

Use a folha **Controle de acesso (IAM)** no [RBAC (controle de acesso baseado em função)](overview.md) para exibir o acesso que um usuário ou outra entidade de segurança tem aos recursos do Azure. No entanto, às vezes, você apenas precisa exibir rapidamente o acesso de um único usuário ou outra entidade de segurança. A maneira mais fácil de fazer isso é usar o recurso **Verificar acesso** no portal do Azure.

## <a name="view-role-assignments"></a>Exibir atribuições de função

 A forma como o acesso de um usuário é exibido é uma lista de suas atribuições de funções. Siga essas etapas para exibir as atribuições de função para um único usuário, grupo, entidade de serviço ou identidade gerenciada no escopo de assinaturas.

1. No portal do Microsoft Azure, clique em **Todos os serviços** e, em seguida, em **Assinaturas**.

1. Clique em sua assinatura.

1. Clique em **Controle de acesso (IAM)** .

1. Clique na guia **Verificar acesso**.

    ![Controle de acesso - verificar a guia de acesso](./media/check-access/access-control-check-access.png)

1. Na lista **Localizar**, selecione o tipo de entidade de segurança para a qual você deseja verificar o acesso.

1. Na caixa de pesquisa, insira uma cadeia de caracteres para pesquisar no diretório nomes de exibição, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de seleção de acesso](./media/check-access/check-access-select.png)

1. Clique na entidade de segurança para abrir o painel **atribuições**.

    ![painel atribuições](./media/check-access/check-access-assignments.png)

    Nesse painel, você pode ver as funções atribuídas à entidade de segurança selecionada e o escopo. Se houver alguma atribuição de negação nesse escopo ou herdada para esse escopo, ela será listada.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Conceder acesso aos recursos do Azure para um usuário usando o RBAC e o portal do Azure](quickstart-assign-role-user-portal.md)
