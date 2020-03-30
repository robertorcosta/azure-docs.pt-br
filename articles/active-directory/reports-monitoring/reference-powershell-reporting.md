---
title: Cmdlets Azure AD PowerShell para relatórios | Microsoft Docs
description: Referência dos cmdlets Ad PowerShell do Azure Para relatórios.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2192c472e00d123780ec6bc5574e7b9fe326258b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495316"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Cmdlets do PowerShell do Azure AD para relatórios

> [!NOTE] 
> Esses cmdlets do Powershell atualmente só funcionam com o [Módulo de Visualização Ad do Azure.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) Observe que o módulo de visualização não é sugerido para uso de produção. 

Para instalar a versão de visualização pública, use o seguinte. 

```powershell
Install-module AzureADPreview
```
Para obter mais informações sobre como se conectar ao Azure AD usando powershell, consulte o artigo [Azure AD Powershell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).  

Com os relatórios do Azure Active Directory (Azure AD), você pode obter detalhes sobre atividades em torno de todas as operações de gravação em sua direção (logs de auditoria) e dados de autenticação (logs de login). Embora as informações estejam disponíveis usando a API do MS Graph, agora você pode recuperar os mesmos dados usando os cmdlets AD PowerShell do Azure para reportar.

Este artigo fornece uma visão geral dos cmdlets do PowerShell para usar para registros de auditoria e logs de login.

## <a name="audit-logs"></a>Logs de auditoria

[Os registros de auditoria](concept-audit-logs.md) fornecem rastreabilidade através de logs para todas as alterações feitas por vários recursos dentro do Azure AD. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

Você tem acesso aos registros de auditoria usando o cmdlet 'Get-AzureADAuditDirectoryLogs.


| Cenário                      | Comando do PowerShell |
| :--                           | :--                |
| Nome do display do aplicativo      | Get-AzureADAuditDirectoryLogs -Filtro "iniciadoPor/aplicativo/displayNome eq 'Azure AD Cloud Sync'" |
| Categoria                      | Get-AzureADAuditDirectoryLogs -Filtro "categoria eq 'Gerenciamento de aplicativos'" |
| Hora da data da atividade            | Get-AzureADAuditDirectoryLogs -Filtro "activityDateTime gt 2019-04-18" |
| Todos os itens acima              | Get-AzureADAuditDirectoryLogs -Filtro "iniciadoPor/aplicativo/displayNome eq 'Azure AD Cloud Sync' e categoria eq 'Gerenciamento de aplicativos' e activityTime gt 2019-04-18"|


A imagem a seguir mostra um exemplo para este comando. 

![Botão "Resumo de dados"](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Logs de entrada

Os [logins](concept-sign-ins.md) fornecem informações sobre o uso de aplicativos gerenciados e atividades de login do usuário.

Você tem acesso aos logs de login usando o cmdlet 'Get-AzureADAuditSignInLogs.


| Cenário                      | Comando do PowerShell |
| :--                           | :--                |
| Nome de exibição do usuário             | Get-AzureADAuditSignInLogs -Filtro "userDisplayName eq 'Timothy Perkins'" |
| Criar data-hora              | Get-AzureADAuditSignInLogs -Filtro "createdDateTime gt 2019-04-18T17:30:00.0Z" (Tudo desde 17:30 em 4/18) |
| Status                        | Get-AzureADAuditSignInLogs -Filtro "status/errorCode eq 50105" |
| Nome do display do aplicativo      | Get-AzureADAuditSignInLogs -Filtre "appDisplayName eq 'StoreFrontStudio [wsfed ativado]'" |
| Todos os itens acima              | Get-AzureADAuditSignInLogs -Filtro "userDisplayName eq 'Timothy Perkins' e status/errorCode ne 0 e appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |


A imagem a seguir mostra um exemplo para este comando. 

![Botão "Resumo de dados"](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos relatórios do Azure AD](overview-reports.md).
- [Relatório de registros de auditoria](concept-audit-logs.md). 
- [Acesso programático aos relatórios do Microsoft Azure Active Directory](concept-reporting-api.md)
