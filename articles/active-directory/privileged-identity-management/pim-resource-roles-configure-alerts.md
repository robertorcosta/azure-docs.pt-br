---
title: Configure alertas de segurança para funções de recursos do Azure no Privileged Identity Management - Azure Active Directory | Microsoft Docs
description: Saiba como configurar alertas de segurança para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
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
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023079"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Configure alertas de segurança para funções de recursos do Azure no Gerenciamento de Identidade Privilegiada

O PIM (Privileged Identity Management, gerenciamento de identidade privilegiado) gera alertas quando há atividadesuspeita ou insegura em sua organização Azure Active Directory (Azure AD). Quando um alerta é disparado, ele aparece na página Alertas.

![Recursos do Azure - Alertas de listagem de página, nível de risco e contagem](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Revisar alertas

Selecione um alerta para ver um relatório que liste os usuários ou funções que acionaram o alerta, juntamente com a orientação de correção.

![Relatório de alerta mostrando o tempo da última varredura, descrição, etapas de mitigação, tipo, gravidade, impacto de segurança e como prevenir a próxima vez](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alertas

| Alerta | Severity | Gatilho | Recomendação |
| --- | --- | --- | --- |
| **Muitos proprietários atribuídos a um recurso** |Médio |Muitos usuários têm a função de proprietário. |Examine os usuários na lista e reatribua alguns a funções menos privilegiadas. |
| **Muitos proprietários permanentes atribuídos a um recurso** |Médio |Muitos usuários são permanentemente atribuídos a uma função. |Revise os usuários na lista e reatribua alguns para exigir ativação para o uso da função. |
| **Duplicar função criada** |Médio |Várias funções têm os mesmos critérios. |Use apenas uma dessas funções. |

### <a name="severity"></a>Severity

- **Alta**: exige ação imediata devido a uma violação da política. 
- **Média**: não exige ação imediata, mas sinaliza uma possível violação da política.
- **Baixa**: não exige ação imediata, mas sugere uma alteração preferencial da política.

## <a name="configure-security-alert-settings"></a>Definir configurações de alerta de segurança

Na página Alertas, vá para **Configurações**.

![Página de alertas com Configurações destacadas](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalize configurações nos diferentes alertas para trabalhar com seu ambiente e as metas de segurança.

![Definir página para um alerta para ativar e configurar configurações](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Próximas etapas

- [Configure as configurações de função de recurso do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
