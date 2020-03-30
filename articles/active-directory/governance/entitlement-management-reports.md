---
title: Exibir relatórios & logs na gestão de direitos - Azure AD
description: Saiba como visualizar o relatório de atribuições do usuário e os logs de auditoria no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 514f8e86d6bd28cc5212e0f0058f00e270f43e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128413"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Exibir relatórios e logs no gerenciamento de direitos do Azure AD

Os relatórios de gerenciamento de direitos do Azure AD e o registro de auditoria do Azure AD fornecem detalhes adicionais sobre quais recursos os usuários têm acesso. Como administrador, você pode visualizar os pacotes de acesso e as atribuições de recursos para um usuário e visualizar logs de solicitação para fins de auditoria ou para determinar o status da solicitação de um usuário. Este artigo descreve como usar os relatórios de gerenciamento de direitos e os registros de auditoria do Azure AD.

Assista ao vídeo a seguir para saber como ver quais recursos os usuários têm acesso na gestão de direitos:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Exibir pacotes de acesso para um usuário

Este relatório permite que você liste todos os pacotes de acesso que um usuário pode solicitar e os pacotes de acesso que estão atualmente atribuídos ao usuário.

**Papel pré-requisito:** Administrador global ou administrador de usuário

1. Clique em **Azure Active Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu à esquerda, clique em **Relatórios**.

1. Clique **em acessar pacotes para um usuário**.

1. Clique **em Selecionar usuários** para abrir o painel Selecionar usuários.

1. Encontre o usuário na lista e clique em **Selecionar**.

    A guia **'Solicitar** pode' exibir uma lista dos pacotes de acesso que o usuário pode solicitar. Esta lista é determinada pelas [políticas de solicitação](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) definidas para os pacotes de acesso. 

    ![Pacotes de acesso para um usuário](./media/entitlement-management-reports/access-packages-report.png)

1. Se houver mais de uma função de recurso ou políticas para um pacote de acesso, clique nas funções de recurso ou na entrada de políticas para ver detalhes da seleção.

1. Clique na guia **Atribuído** para ver uma lista dos pacotes de acesso atualmente atribuídos ao usuário. Quando um pacote de acesso é atribuído a um usuário, significa que o usuário tem acesso a todas as funções de recurso no pacote de acesso.

## <a name="view-resource-assignments-for-a-user"></a>Exibir atribuições de recursos para um usuário

Este relatório permite que você liste os recursos atualmente atribuídos a um usuário na gestão de direitos. Observe que este relatório é para recursos gerenciados com gestão de direitos. O usuário pode ter acesso a outros recursos em seu diretório fora da gestão de direitos.

**Papel pré-requisito:** Administrador global ou administrador de usuário

1. Clique em **Azure Active Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu à esquerda, clique em **Relatórios**.

1. Clique **em Atribuições de recursos para um usuário**.

1. Clique **em Selecionar usuários** para abrir o painel Selecionar usuários.

1. Encontre o usuário na lista e clique em **Selecionar**.

    Uma lista dos recursos atualmente atribuídos ao usuário é exibida. A lista também mostra o pacote de acesso e a política de que eles receberam o papel de recurso, juntamente com a data de início e término para acesso.
    
    Se um usuário tiver acesso ao mesmo recurso em dois ou mais pacotes, você pode clicar em uma seta para ver cada pacote e política.

    ![Atribuições de recursos para um usuário](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Determine o status da solicitação de um usuário

Para obter detalhes adicionais sobre como um usuário solicitou e recebeu acesso a um pacote de acesso, você pode usar o registro de auditoria do Azure AD. Em particular, você pode usar `EntitlementManagement` os `UserManagement` registros de registro nas categorias e categorias para obter detalhes adicionais sobre as etapas de processamento de cada solicitação.  

1. Clique **em Azure Active Directory** e clique em **Audit logs**.

1. No topo, mude **Category** a `EntitlementManagement` categoria `UserManagement`para um ou , dependendo do registro de auditoria que você está procurando.  

1. Clique em **Aplicar**.

1. Para baixar os logs, clique em **Baixar**.

Quando o Azure AD recebe uma nova solicitação, ele `EntitlementManagement` grava um registro `User requests access package assignment`de auditoria, no qual a **Categoria** está e a **Atividade** é tipicamente .  No caso de uma atribuição direta criada no **Activity** portal Azure, `Administrator directly assigns user to access package`o campo Atividade do registro de auditoria é , e o usuário que executa a atribuição é identificado pelo **ActorUserPrincipalName**.

O Azure AD gravará registros de auditoria adicionais enquanto a solicitação estiver em andamento, incluindo:

| Categoria | Atividade | Status da solicitação |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Solicitação não requer aprovação |
| `UserManagement` | `Create request approval` | Solicitação requer aprovação |
| `UserManagement` | `Add approver to request approval` | Solicitação requer aprovação |
| `EntitlementManagement` | `Approve access package assignment request` | Pedido aprovado |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Solicitação aprovada ou não requer aprovação |

Quando um usuário é atribuído acesso, o Azure `EntitlementManagement` AD grava um registro de auditoria para a categoria com **Atividade** `Fulfill access package assignment`.  O usuário que recebeu o acesso é identificado pelo campo **ActorUserPrincipalName.**

Se o acesso não foi atribuído, então o Azure AD `Deny access package assignment request`grava um registro de auditoria para `Access package assignment request timed out (no approver action taken)`a `EntitlementManagement` categoria com **Atividade,** se a solicitação foi negada por um aprovador, ou , se a solicitação foi prorrogada antes que um aprovador pudesse aprovar.

Quando a atribuição do pacote de acesso do usuário expira, é cancelada pelo usuário ou removida por `EntitlementManagement` um administrador, `Remove access package assignment`então o Azure AD grava um registro de auditoria para a categoria com **Atividade** de .

## <a name="next-steps"></a>Próximas etapas

- [Relatórios de arquivamento e logs](entitlement-management-logs-and-reporting.md)
- [Solucionando problemas gerenciamento de direitos ad do Azure](entitlement-management-troubleshoot.md)
- [Cenários comuns](entitlement-management-scenarios.md)
