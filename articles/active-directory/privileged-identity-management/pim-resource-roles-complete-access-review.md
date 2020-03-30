---
title: Concluir a revisão de acesso das funções de recursos do Azure no PIM - Azure AD | Microsoft Docs
description: Saiba como concluir uma revisão de acesso das funções de recurso do Azure Gerenciamento de identidade privilegiado no Diretório Ativo do Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021993"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Complete uma revisão de acesso das funções de recurso do Azure no Gerenciamento de Identidade Privilegiada

Os administradores de função privilegiada podem rever o acesso privilegiado depois de [iniciar uma revisão de acesso](pim-resource-roles-start-access-review.md). O Privilegiao de Gerenciamento de Identidade (PIM) no Azure Active Directory (Azure AD) envia automaticamente um e-mail que solicita aos usuários que revisem seu acesso. Se um usuário não receber um email, você poderá enviar para ele as instruções de [como executar uma revisão de acesso](pim-resource-roles-perform-access-review.md).

Depois do fim do período da revisão de acesso, ou após todos os usuários terminarem a autorrevisão, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerenciar revisões de acesso

1. Vá para o [portal Azure.](https://portal.azure.com/) No painel de controle, selecione o serviço de recursos do **Azure.**

2. Selecione seu recurso.

3. Clique na seção **Revisões de acesso** do painel.

    ![Recursos do Azure - Lista de avaliações de acesso mostrando papel, proprietário, data de início, data de término e status](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Selecione a análise de acesso que você deseja gerenciar.

Na página de detalhes para a revisão de acesso, há uma série de opções para gerenciar essa revisão. As opções são as descritas a seguir:

![Opções para gerenciar uma revisão - Parar, Redefinir, Aplicar, Excluir](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stop

Todas as avaliações de acesso têm uma data final. Selecione **Pare** para terminá-lo mais cedo. Qualquer usuário que ainda não tenha terminado sua revisão até agora não será capaz de terminá-lo depois que você parar a revisão. Não é possível reiniciar uma revisão após ela ter sido interrompida.

### <a name="reset"></a>Redefinir

Você pode redefinir uma revisão de acesso para remover todas as decisões feitas nela. Depois de redefinir uma revisão de acesso, todos os usuários são marcados como não revisados novamente.

### <a name="apply"></a>Aplicar

Depois que uma revisão de acesso for concluída, **selecione Aplicar** para implementar o resultado da revisão. Se o acesso de um usuário foi negado na análise, esta etapa remove sua atribuição de função.  

### <a name="delete"></a>Excluir

Se você não estiver mais interessado na revisão, exclua-a. Selecione **Excluir** yo remover a revisão do serviço de Gerenciamento de Identidade Privilegiada.

## <a name="results"></a>Resultados

Na página **Resultados,** veja e baixe uma lista dos resultados da sua avaliação.

![Página de resultados listando usuários, resultado, razão, revisado por, aplicado por, e aplicar resultado](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisores

Exiba e adicione revisores à sua revisão de acesso existente. Lembre os revisores de concluir suas revisões.

![Revisores página listando nome e nome principal do usuário](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Próximas etapas

- [Inicie uma revisão de acesso para funções de recursos do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-start-access-review.md)
- [Realize uma revisão de acesso das minhas funções de recurso do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-perform-access-review.md)
