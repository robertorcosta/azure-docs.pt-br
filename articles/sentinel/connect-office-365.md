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
ms.openlocfilehash: c3e63063b3ea4e7fba3997ddd645aa59fe857488
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758564"
---
# <a name="connect-data-from-office-365-logs"></a>Conectar dados de logs do Office 365



Você pode transmitir logs de auditoria do [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) para o Azure Sentinel com um único clique. Você pode transmitir logs de auditoria do Office 365 para o workspace do Azure Sentinel no mesmo locatário. O conector do log de atividades do Office 365 fornece informações sobre as atividades contínuas do usuário. Você receberá informações sobre várias ações de usuário, administrador, sistema e política e eventos do Office 365. Ao conectar os logs do Office 365 ao Azure Sentinel, você poderá usar esses dados para exibir painéis, criar alertas personalizados e melhorar o processo de investigação.

> [!IMPORTANT]
> Se tiver uma licença E3, você deverá habilitar o log de auditoria unificada para sua organização do Office 365 para conseguir acessar dados por meio da API de Atividade de Gerenciamento do Office 365. Para isso, ative o log de auditoria do Office 365. Para obter instruções, consulte [Ativar ou desativar a pesquisa de logs de auditoria do Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Consulte a [referência da API de atividade de gerenciamento do Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

- Você precisa ser um administrador global ou administrador da segurança no seu locatário.
- Seu locatário precisa ter a auditoria unificada habilitada. Locatários com licenças E3 ou E5 do Office 365 têm a auditoria unificada habilitada por padrão. <br>Se seu locatário não tiver uma dessas licenças, você precisará habilitar a auditoria unificada no seu locatário por um destes métodos:
    - [Usar o cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) e habilitar o parâmetro “UnifiedAuditLogIngestionEnabled”).
    - [Usar a interface do usuário do Centro de Segurança e Conformidade](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).
   
   > [!NOTE]
   > Atualmente, o conector de dados do O365 captura automaticamente apenas atividade do Exchange e do SharePoint como mencionamos na página do conector na seção de tipos de dados. Recomendamos conferir [este artigo caso precise de dados de auditoria do Teams e proteja o Teams com o Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761). 

## <a name="connect-to-office-365"></a>Conectar ao Office 365

1. No Azure Sentinel, selecione **Conectores de dados** e, em seguida, clique no bloco **Office 365**.

2. Se ainda tiver habilitado, acesse a folha **Conectores de dados** e selecione o conector **Office 365**. Aqui, você pode clicar na **página Abrir conector** e, na seção de configuração rotulada **Configuração**, selecione todos os logs de atividade do Office 365 que você deseja conectar ao Azure Sentinel. 
   > [!NOTE]
   > Se você já conectou vários locatários em uma versão anterior com suporte do conector do Office 365 no Azure Sentinel, poderá exibir e modificar quais logs você coleta de cada locatário. Você não poderá adicionar locatários, mas poderá remover locatários adicionados anteriormente.
3. Para usar o esquema relevante no Log Analytics para os logs do Office 365, pesquise **OfficeActivity**.


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o Office 365 ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

