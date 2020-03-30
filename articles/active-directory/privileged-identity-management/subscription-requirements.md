---
title: Requisitos de licença para usar o Privileged Identity Management - Azure Active Directory | Microsoft Docs
description: Descreve os requisitos de licenciamento para uso do Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70696cdb95fffc1e5faa46ca1b5f2180633ed63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932331"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Requisitos de licença para usar o Privileged Identity Management

Para usar o Azure AD (Azure Active Directory) PIM (Privileged Identity Management), um diretório precisa ter uma licença válida. Além disso, as licenças precisam ser atribuídas aos administradores e aos usuários relevantes. Este artigo descreve os requisitos de licença para usar o Gerenciamento de Identidade Privilegiada.

## <a name="valid-licenses"></a>Licenças válidas

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>Quantas licenças você deve ter?

Certifique-se de que seu diretório tenha pelo menos tantas licenças Azure AD Premium P2 quanto você tem funcionários que estarão executando as seguintes tarefas:

- Usuários atribuídos como elegíveis para funções Ad do Azure gerenciadas usando PIM
- Usuários capazes de aprovar ou rejeitar solicitações de ativação no PIM
- Usuários atribuídos a uma função de recurso do Azure com atribuições Just-In-Time ou diretas (por tempo limitado)  
- Usuários atribuídos a uma revisão de acesso
- Usuários que executam revisões de acesso

As licenças Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Não são necessárias licenças para usuários com as funções de Administrador Global ou Administrador de Funções Privilegiadas que configuram o PIM, configuram políticas, recebem alertas e configuram avaliações de acesso.

Para obter mais informações sobre licenças, consulte [Atribuir ou remover licenças usando o portal Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Exemplos de cenários de licença

Aqui estão alguns exemplos de cenários de licenças para ajudá-lo a determinar o número de licenças que você deve ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| O Woodgrove Bank possui 10 administradores para diferentes departamentos e 2 administradores globais que configuram e gerenciam o PIM. Eles tornam cinco administradores elegíveis. | Cinco licenças para os administradores elegíveis | 5 |
| O Graphic Design Institute tem 25 administradores, dos quais 14 são gerenciados através do PIM. A ativação da função requer aprovação e há três usuários diferentes na organização que podem aprovar ativações. | 14 licenças para as funções elegíveis + três aprovadores | 17 |
| Contoso tem 50 administradores dos quais 42 são gerenciados através do PIM. A ativação da função requer aprovação e há cinco usuários diferentes na organização que podem aprovar ativações. Contoso também faz avaliações mensais de usuários atribuídos a funções de administrador e os revisores são os gerentes dos usuários dos quais seis não estão em funções de administrador gerenciados pelo PIM. | 42 licenças para as funções elegíveis + cinco aprovadores + seis revisores | 53 |

## <a name="what-happens-when-a-license-expires"></a>O que acontece quando uma licença expira?

Se um Azure AD Premium P2, EMS E5 ou licença de teste expirar, os recursos de Gerenciamento de Identidade Privilegiado não estarão mais disponíveis em seu diretório:

- Atribuições de função permanentes para funções do Azure AD não serão afetadas.
- O serviço de Gerenciamento de Identidade Privilegiada no portal Azure, bem como os cmdlets de API do Gráfico e as interfaces PowerShell de Gerenciamento de Identidade Privilegiada, não estarão mais disponíveis para os usuários ativarem funções privilegiadas, gerenciarem acesso privilegiado ou executarem revisões de acesso de funções privilegiadas.
- Atribuições de funções qualificadas de funções do Azure AD serão removidas, pois os usuários não poderão mais ativar funções privilegiadas.
- Quaisquer revisões de acesso contínuas das funções Ad do Azure serão encerradas e as configurações de configuração do Gerenciamento de Identidade Privilegiada serão removidas.
- O Gerenciamento de Identidade Privilegiado não enviará mais e-mails sobre alterações de atribuição de papéis.

## <a name="next-steps"></a>Próximas etapas

- [Implantar o Privileged Identity Management](pim-deployment-plan.md)
- [Começar usando o Privileged Identity Management](pim-getting-started.md)
- [Funções que você não pode gerenciar no Gerenciamento de Identidade Privilegiada](pim-roles.md)
