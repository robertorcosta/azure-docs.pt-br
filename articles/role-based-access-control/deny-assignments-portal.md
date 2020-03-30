---
title: Lista negar atribuições para recursos do Azure com o portal Azure
description: Saiba como listar os usuários, grupos, diretores de serviços e identidades gerenciadas que tiveram acesso negado a ações específicas de recursos do Azure em escopos específicos usando o portal Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4db76e5c6191457346ca1f95678cf73843334d3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137435"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Lista negar atribuições para recursos do Azure usando o portal Azure

[Negar atribuições](deny-assignments.md) bloqueie os usuários de executar ações específicas de recursos do Azure, mesmo que uma atribuição de função lhes conceda acesso. Este artigo descreve como listar atribuições de negação usando o portal Azure.

> [!NOTE]
> Você não pode criar diretamente suas próprias atribuições de negação. Para obter informações sobre como as atribuições de negação são criadas, consulte [As atribuições de Negar](deny-assignments.md).

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de negação, você deve ter:

- `Microsoft.Authorization/denyAssignments/read`permissão, que está incluída na maioria [das funções incorporadas para os recursos do Azure](built-in-roles.md).

## <a name="list-deny-assignments"></a>Listar atribuições de negação

Siga essas etapas para listar atribuições de negação no escopo de assinatura ou grupo de gerenciamento.

1. No portal do Azure, clique em **Todos os serviços** e, em seguida, em **Grupos de gerenciamento** ou **Assinaturas**.

1. Clique no grupo de gerenciamento ou assinatura que deseja listar.

1. Clique **em Acessar controle (IAM)**.

1. Clique na guia **Negar atribuições** (ou clique no botão **Visualizar** na peça Visualizar negar atribuições).

    Se houver alguma atribuição de negação nesse escopo ou herdada para esse escopo, ela será listada.

    ![Controle de acesso - guia Negar atribuições](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Para exibir colunas adicionais, clique em **Editar colunas**.

    ![Negar atribuições - colunas](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Nome** | Nome da atribuição de negação. |
    | **Tipo principal** | Usuário, grupo, grupo definido pelo sistema ou principal do serviço. |
    | **Negado**  | Nome do principal de segurança incluído na atribuição de negação. |
    | **Id** | Identificador exclusivo para a atribuição de negação. |
    | **Principais Excluídos** | Se existem entidades de segurança que são excluídas da atribuição de negação. |
    | **Não se aplica a crianças** | Se a atribuição de negação é herdada para subescópios. |
    | **Protegidos do sistema** | Se a atribuição de negação é gerenciada pelo Azure. Atualmente, sempre sim. |
    | **Escopo** | Grupo de gerenciamento, assinatura, grupo de recursos ou recurso. |

1. Adicione uma marca de seleção a qualquer um dos itens ativados e clique em **OK** para exibir as colunas selecionadas.

## <a name="list-details-about-a-deny-assignment"></a>Detalhes da lista sobre uma atribuição de negação

Siga estas etapas para listar detalhes adicionais sobre uma atribuição de negação.

1. Abra o painel **Negar atribuições** conforme descrito na seção anterior.

1. Clique no nome da atribuição de negação para abrir a folha **Usuários**.

    ![Negar designação - usuários](./media/deny-assignments-portal/deny-assignment-users.png)

    A folha ** Usuários ** inclui as duas seções a seguir.

    |  |  |
    | --- | --- |
    | **Negar atribuição se aplica a**  | Entidades de segurança às quais a atribuição de negação se aplica. |
    | **Negar a exclui da atribuição** | Entidades de segurança que são excluídas da atribuição de negação. |

    **A diretiva definida pelo sistema** representa todos os usuários, grupos, entidades de serviço e identidades gerenciadas em um Azure Active Directory.

1. Para ver uma lista das permissões que são negados, clique em **permissões negadas**.

    ![Negar - atribuição de permissões negado](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Tipo de ação | Descrição |
    | --- | --- |
    | **Ações**  | Operações de gerenciamento negado. |
    | **NotActions** | Operações de gerenciamento excluídas da operação de gerenciamento negado. |
    | **DataActions**  | Operações de dados negado. |
    | **NotDataActions** | Operações de dados excluídas da operação de dados negados. |

    Para o exemplo mostrado na captura de tela anterior, as seguintes são as permissões efetivas:

    - Todas as operações de armazenamento no plano de dados são negadas, exceto para operações de computação.

1. Para ver as propriedades de uma atribuição de negação, clique em **Propriedades**.

    ![Negar atribuição - propriedades](./media/deny-assignments-portal/deny-assignment-properties.png)

    Na folha **Propriedades**, você pode ver o nome, a ID, a descrição e o escopo da atribuição de negação. A opção **Não se aplica a filhos** indica se a atribuição de negação é herdada para os subescópios. A chave **System protected** indica se essa atribuição de negação é gerenciada pelo Azure. Atualmente, isso é **Sim** em todos os casos.

## <a name="next-steps"></a>Próximas etapas

* [Compreender atribuições de negação dos recursos do Azure](deny-assignments.md)
* [Lista negar atribuições para recursos do Azure usando o Azure PowerShell](deny-assignments-powershell.md)
