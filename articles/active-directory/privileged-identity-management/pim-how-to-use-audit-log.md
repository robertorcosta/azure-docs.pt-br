---
title: Veja o relatório de registro de auditoria para funções azure AD no Azure AD PIM | Microsoft Docs
description: Saiba como visualizar o histórico de registros de auditoria para funções azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8aef68e0f61e6ca995fc2bb362d59aba73ead2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329487"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Veja o histórico de auditoria das funções do Azure AD no Gerenciamento de Identidade Privilegiada

Você pode usar o histórico de auditoria do Audit Privileged Identity Management (PIM) para ver todas as atribuições e ativações de funções nos últimos 30 dias para todas as funções privilegiadas. Se você quiser ver o histórico completo de atividades da auditoria na organização Azure Active Directory (Azure AD), incluindo atividade de administrador, usuário final e sincronização, você pode usar os relatórios de [segurança e atividade do Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Determine sua versão do PIM

A partir de novembro de 2019, a parte de funções Azure AD do Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências para funções de recursos do Azure. Isso cria recursos adicionais, bem como [alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo lançada, quais procedimentos você segue neste artigo dependem da versão do Gerenciamento de Identidade Privilegiada que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Gerenciamento de Identidade Privilegiada você tem. Depois de conhecer sua versão do Gerenciamento de Identidade Privilegiada, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Faça login no [portal Azure](https://portal.azure.com/) com um usuário que esteja na função [de administrador de funções privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Abra **o Azure AD Privileged Identity Management**. Se você tiver um banner na parte superior da página de visão geral, siga as instruções na guia **Nova versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior.**

    [![Azure AD funde nova versão](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Selecione a guia para sua versão")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Nova versão](#tab/new)

Siga essas etapas para visualizar o histórico de auditoria das funções do Azure AD.

## <a name="view-resource-audit-history"></a>Exibir o histórico de auditoria de recursos

A auditoria de recursos oferece uma visão de todas as atividades associadas às suas funções azure AD.

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **funções Azure AD**.

1. Selecione **auditoria de recursos**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria de recursos com filtros](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Exibir minha auditoria

A opção Minha auditoria permite que você exiba sua atividade de função pessoal.

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **funções Azure AD**.

1. Selecione o recurso para o seu objetivo de visualizar o histórico de auditoria.

1. Selecione **Minha auditoria**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria para o usuário atual](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="view-audit-history"></a>Exibir histórico de auditoria

Siga essas etapas para visualizar o histórico de auditoria das funções do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função [Administrador de Funções com Privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **funções Azure AD**.

1. Selecione **o histórico de auditoria das funções do diretório**.

    Dependendo do histórico de auditoria, um gráfico de colunas é exibido junto com as ativações totais, ativações máximas por dia e ativações médias por dia.

    [![Azure AD funde nova versão](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Ver histórico de auditoria de funções de diretório")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Na parte inferior da página, uma tabela é exibida com informações sobre cada ação no histórico de auditoria disponível. As colunas têm os seguintes significados:

    | Coluna | Descrição |
    | --- | --- |
    | Hora | Quando a ação ocorreu. |
    | Solicitante | Usuário que solicitou a ativação ou alteração da função. Se o valor for **O Sistema Azure,** verifique o histórico de auditoria do Azure para obter mais informações. |
    | Ação | Ações tomadas pelo solicitante. As ações podem incluir Atribuir, Desatribuir, Ativar, Desativar ou Adicionar OutsidePIM. |
    | Membro | Usuário que está ativando ou atribuindo a uma função. |
    | Função | Função atribuída ou ativada pelo usuário. |
    | Raciocínio | Texto que foi inserido no campo de razão durante a ativação. |
    | Expiração | Quando uma função ativada expirar. Aplica-se apenas a atribuições de função elegíveis. |

1. Para classificar o histórico de auditoria, clique nos botões **Tempo,** **Ação**e **Função.**

## <a name="filter-audit-history"></a>Filtrar histórico de auditoria

1. Na parte superior da página do histórico de auditoria, clique no botão **Filtro.**

    O painel **de parâmetros do gráfico de atualização** é exibido.

1. No **intervalo de tempo,** selecione um intervalo de tempo.

1. Em **Funções,** selecione as caixas de seleção para indicar as funções que deseja exibir.

    ![Painel de parâmetros do gráfico de atualização](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Selecione **Feito** para exibir o histórico de auditoria filtrado.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Obter razão, aprovador e número de bilhete para eventos de aprovação

1. Faça login no [portal Azure](https://aad.portal.azure.com) com permissões de função de administrador de funções privilegiadas e abra o Azure AD.
1. Selecione **registros de auditoria**.
1. Use o filtro **Serviço** para exibir apenas eventos de auditoria para o serviço de gerenciamento de identidade privilegiado. Na página de registros de **auditoria,** você pode:

    - Veja o motivo de um evento de auditoria na coluna **Razão status.**
    - Consulte o aprovador na coluna **Iniciado por (ator)** para o evento "adicionar membro à solicitação de papel aprovado".

    [![Azure AD funde nova versão](media/pim-how-to-use-audit-log/filter-audit-logs.png "Filtrar o registro de auditoria para o serviço PIM")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Selecione um evento de registro de auditoria para ver o número do bilhete na guia **Atividade** do painel **Detalhes.**
  
    [![Azure AD funde nova versão](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Confira o número do ingresso para o evento de auditoria")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. Você pode visualizar o solicitante (pessoa ativando a função) na guia **'''Objetivos'** do painel **Detalhes** para um evento de auditoria. Existem dois tipos de destino para funções Azure AD:

    - O papel (**Tipo** = Papel)
    - O solicitante (**Tipo** = Usuário)

Normalmente, o evento de registro de auditoria imediatamente acima do evento de aprovação é um evento para "Adicionar membro ao papel concluído" onde o **Iniciado por (ator)** é o solicitante. Na maioria dos casos, você não precisará encontrar o solicitante no pedido de aprovação de uma perspectiva de auditoria.

---

## <a name="next-steps"></a>Próximas etapas

- [Exibir histórico de atividades e auditoria susta is](azure-pim-resource-rbac.md)
