---
title: Painéis de recursos para avaliações de acesso no PIM - Azure AD | Microsoft Docs
description: Descreve como usar um painel de recursos para executar uma análise de acesso no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6affa2ecc8919dabeb6173622b525280ce96bcfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847021"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Use um painel de recursos para executar uma revisão de acesso no Gerenciamento de Identidade Privilegiada

Você pode usar um painel de recursos para executar uma revisão de acesso no Pim (Privileged Identity Management, gerenciamento de identidade privilegiada). O painel Admin View no Azure Active Directory (Azure AD) tem três componentes principais:

- Uma representação gráfica das ativações de função de recurso
- Gráficos que exibem a distribuição de atribuições de função por tipo de atribuição
- Uma área de dados contendo informações para novas atribuições de função

![Captura de tela do painel de exibição do administrador, mostrando gráficos e tabelas](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Captura de tela do painel de exibição do administrador, mostrando listas de dados](media/pim-resource-roles-overview-dashboards/role-settings.png)

A representação gráfica das ativações de função de recurso abrange os últimos sete dias. Esses dados estão no escopo das ativações de telas e recursos selecionados para as funções mais comuns (Proprietário, Colaborador, Administrador de Acesso do Usuário) e todas as funções combinadas.

Em um lado do gráfico de ativações, dois gráficos exibem a distribuição de atribuições de função por tipo de atribuição, tanto para usuários quanto para grupos. Você pode alterar o valor para uma porcentagem (ou vice-versa), selecionando uma fatia do gráfico.

Abaixo dos gráficos estão listados o número de usuários e grupos com novas atribuições de função nos últimos 30 dias, e funções classificadas por atribuições totais em ordem decrescente.

## <a name="next-steps"></a>Próximas etapas

- [Inicie uma revisão de acesso para funções de recursos do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-start-access-review.md)
