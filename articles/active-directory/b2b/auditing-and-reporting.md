---
title: Auditoria e relatório de um usuário de colaboração B2B - Azure AD
description: As propriedades do usuário convidado são configuráveis na colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74d2473d37dcf0d435ab61763e72a64111845956
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273282"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Auditoria e relatórios de um usuário de colaboração B2B
Com usuários convidados, você conta com funcionalidades de auditoria semelhantes àquelas dos usuários membros. 

## <a name="access-reviews"></a>Análises de acesso
É possível usar as revisões de acesso para verificar periodicamente se usuários convidados ainda precisam acessar os recursos. O recurso **Revisões de acesso** está disponível no **Azure Active Directory** em **Gerenciar** > **Relações Organizacionais**. (Você também pode procurar por "avaliações de acesso" de **todos os serviços** no portal Azure.) Para saber como usar avaliações de acesso, consulte [Gerenciar o acesso de hóspedes com avaliações de acesso ao Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Logs de auditoria

Os logs de auditoria do Azure AD fornecem registros das atividades do usuário e do sistema, incluindo atividades iniciadas por usuários convidados. Para acessar os logs de auditoria, no **Azure Active Directory**, em **monitoramento**, selecione **Logs de auditoria**. Este é um exemplo do histórico de convites e resgates do convidado Davi Barros:

![Captura de tela e exemplo da saída do registro de auditoria](./media/auditing-and-reporting/audit-log.png)

Você pode se aprofundar em cada um desses eventos para obter detalhes. Por exemplo, vamos examinar os detalhes de aceitação.

![Captura de tela mostrando e exemplo de saída de detalhes de atividade](./media/auditing-and-reporting/activity-details.png)

Você também pode exportar esses logs do Azure AD e usar a ferramenta de relatório de sua preferência para obter relatórios personalizados.

### <a name="next-steps"></a>Próximas etapas

- [Propriedades de usuário de colaboração B2B](user-properties.md)

