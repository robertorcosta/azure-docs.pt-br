---
title: Conectar dados do Office 365 ao Azure Sentinel | Microsoft Docs
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
ms.openlocfilehash: b00f9c9e7de9568a29d2b7a7f4ef84c022ef5679
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588154"
---
# <a name="connect-data-from-office-365-logs"></a>Conectar dados de logs do Office 365



Você pode transmitir logs de auditoria do [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) para o Azure Sentinel com um único clique. Você pode transmitir logs de auditoria do seu Office 365 para seu espaço de trabalho do Azure Sentinel no mesmo locatário. O conector do log de atividades do Office 365 fornece informações sobre atividades de usuário contínuas. Você obterá informações sobre várias ações de usuário, de administrador, de sistema e de política e eventos do Office 365. Conectando os logs do Office 365 ao Azure Sentinel, você pode usar esses dados para exibir painéis, criar alertas personalizados e melhorar o processo de investigação.

> [!IMPORTANT]
> Se você tiver uma licença E3, antes de poder acessar dados por meio da API de atividade de gerenciamento do Office 365, você deverá habilitar o log de auditoria unificada para sua organização do Office 365. Faça isso ativando o log de auditoria do Office 365. Para obter instruções, consulte [Ativar ou desativar a pesquisa de log de auditoria do Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Consulte [referência de API de atividade de gerenciamento do Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)para obter mais informações.

## <a name="prerequisites"></a>Prerequisites

- Você deve ser um administrador global ou administrador de segurança em seu locatário.
- Se seu locatário não tiver uma licença do Office 365 E3 ou do Office 365 e5, você deverá habilitar a auditoria unificada em seu locatário usando um destes processos:
    - [Usando o cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) e habilitar o parâmetro "UnifiedAuditLogIngestionEnabled").
    - [Ou usando a interface do usuário do centro de conformidade e segurança](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Conectar ao Office 365

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco do **Office 365** .

2. Se você ainda não o tiver habilitado, poderá fazer isso acessando a folha **conectores de dados** e selecionando conector **do Office 365** . Aqui, você pode clicar na **página abrir conector** e, na seção configuração rotulada **configuração** , selecione todos os logs de atividade do Office 365 que você deseja conectar ao Azure Sentinel. 
   > [!NOTE]
   > Se você já conectou vários locatários em uma versão anterior com suporte do conector do Office 365 no Azure Sentinel, você poderá exibir e modificar quais logs você coleta de cada locatário. Você não poderá adicionar locatários adicionais, mas poderá remover locatários adicionados anteriormente.
3. Para usar o esquema relevante no Log Analytics para os logs do Office 365, pesquise **OfficeActivity**.


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o Office 365 ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

