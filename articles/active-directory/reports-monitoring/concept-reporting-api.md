---
title: Introdução à API de relatórios do Microsoft Azure AD | Microsoft Docs
description: Como começar a usar a API de relatório do Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56f240a5191dd483f89889f3ffe13b1819ca1e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399313"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Introdução à API de relatórios do Microsoft Azure Active Directory

O Active Directory Domain Service do Azure oferece uma variedade de [relatórios](overview-reports.md), que contém informações úteis para aplicativos como sistemas SIEM, auditoria e ferramentas de business intelligence. 

Usando a API do Graph para relatórios do Azure AD, você pode obter acesso programático aos dados por meio de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

Este artigo fornece uma visão geral da API de relatórios, incluindo formas de acessá-lo.

Se houver problemas, consulte [Como obter suporte para o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Pré-requisitos

Para acessar a API de relatórios, com ou sem a intervenção do usuário, você precisa:

1. Atribuir funções (Leitor de Segurança, Admin. de Segurança, Admin. Global)
2. Registrar um aplicativo
3. Conceder permissões
4. Reunir definições de configuração

Para obter instruções detalhadas, consulte, [pré-requisitos para acessar a API de relatório do Microsoft Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>Pontos de extremidade de API 

O ponto de extremidade da API do Graph para logs de auditoria é `https://graph.microsoft.com/beta/auditLogs/directoryAudits` e o ponto de extremidade da API do Microsoft Graph para logins é `https://graph.microsoft.com/beta/auditLogs/signIns`. Para obter mais informações, consulte a [referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) e a [referência da API de login](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

Além disso, você pode usar a [API de detecção de risco de proteção de identidade](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) para obter acesso programático a detecções de segurança usando o Microsoft Graph. Para obter mais informações, consulte [Introdução ao Azure Active Directory Identity Protection e Microsoft Graph](../identity-protection/graph-get-started.md). 
  
## <a name="apis-with-microsoft-graph-explorer"></a>APIs com Microsoft Graph Explorer

Você pode usar o [explorador do Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) para verificar seus dados de API de login e auditoria. Certifique-se de entrar na conta usando ambos os botões entrar na interface do usuário do Explorador do Graph e configure as permissões **AuditLog.Read.All** e **Directory.Read.All** do locatário, conforme mostrado.   

![Explorador do Graph](./media/concept-reporting-api/graph-explorer.png)

![Modificar a interface do usuário de permissões](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Use certificados para acessar a API de relatórios do AD do Azure 

Use a API de relatórios do Azure AD com certificados se você planeja recuperar dados de relatórios sem a intervenção do usuário.

Para obter instruções [detalhadas, consulte Obter dados usando a API de relatórios ad do Azure com certificados](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Próximas etapas

 * [Pré-requisitos para acessar a API de relatório](howto-configure-prerequisites-for-reporting-api.md) 
 * [Obter dados usando a API de Relatório do Azure AD com certificados](tutorial-access-api-with-certificates.md)
 * [Solucionar problemas de erros no relatório de API do Azure AD](troubleshoot-graph-api.md)


