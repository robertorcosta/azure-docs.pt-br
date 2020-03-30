---
title: Conecte os dados do Office 365 ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados do Office 365 ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252509"
---
# <a name="connect-data-from-office-365-logs"></a>Conecte dados do Office 365 Logs



Você pode transmitir logs de auditoria do [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) para o Azure Sentinel com um único clique. Você pode transmitir logs de auditoria do seu Office 365 para o seu espaço de trabalho do Azure Sentinel no mesmo inquilino. O conector de registro de atividades do Office 365 fornece informações sobre as atividades do usuário em andamento. Você obterá informações sobre vários usuários, administradores, ações de sistema e políticas e eventos do Office 365. Ao conectar os logs do Office 365 no Azure Sentinel, você pode usar esses dados para visualizar dashboards, criar alertas personalizados e melhorar seu processo de investigação.

> [!IMPORTANT]
> Se você tiver uma licença E3, antes de acessar dados através da API de atividade de gerenciamento do Office 365, você deve habilitar o registro de auditoria unificado para sua organização Office 365. Você faz isso ligando o registro de auditoria do Office 365. Para obter instruções, consulte [Turn Office 365 audit log search on ou off](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Consulte [office 365 management Activity API reference](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference), para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um administrador global ou administrador de segurança em seu inquilino.
- Seu inquilino deve ter auditoria unificada habilitada. Os inquilinos com licenças Office 365 E3 ou E5 têm auditoria unificada habilitada por padrão. <br>Se o seu inquilino não tiver uma dessas licenças, você deve habilitar a auditoria unificada em seu inquilino usando um desses métodos:
    - [Usando o cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) e habilitar o parâmetro "UnifiedAuditLogIngestionEnabled").
    - [Usando a iU do Security & Compliance Center](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Conectar ao Office 365

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco **Office 365.**

2. Se você ainda não o habilitou, você pode fazê-lo indo para a lâmina **De conectores de dados** e selecionando o conector Office **365.** Aqui você pode clicar na **Página do Conector Aberto** e na seção de configuração rotulada **Configuração** selecione todos os registros de atividades do Office 365 que deseja conectar ao Azure Sentinel. 
   > [!NOTE]
   > Se você já conectou vários inquilinos em uma versão anteriormente suportada do conector Office 365 no Azure Sentinel, você poderá visualizar e modificar quais logs você coleta de cada inquilino. Você não será capaz de adicionar inquilinos adicionais, mas você pode remover inquilinos adicionados anteriormente.
3. Para usar o esquema relevante no Log Analytics para os logs do Office 365, procure o **OfficeActivity**.


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Office 365 ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

