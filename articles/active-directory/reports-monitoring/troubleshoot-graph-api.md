---
title: Solucionar erros na API de relatório do Azure Active Directory | Microsoft Docs
description: Fornece uma resolução de erros ao chamar APIs de relatórios do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d1fb4f49e4f9ad41f971d869873200e6180b5cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399289"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Solucionar erros na API de relatório do Azure Active Directory

Este artigo lista as mensagens de erro comuns que você pode encontrar ao acessar relatórios de atividade usando a API do Microsoft Graph e as etapas para sua resolução.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 Erro interno do servidor HTTP ao acessar o ponto de extremidade do Microsoft Graph V2

No momento, não há suporte para o ponto de extremidade do Microsoft Graph v2 – acesse os logs de atividades usando o ponto de extremidade do Microsoft Graph v1.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Erro: O locatário não é B2C ou não tem uma licença Premium

O acesso a relatórios de entrada requer uma licença do Azure Active Directory Premium 1 (P1). Se essa mensagem de erro for exibida quando você acessar as entradas, verifique se o locatário está licenciado com uma licença do Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Erro: O usuário não está nas funções permitidas 

Se essa mensagem de erro for exibida quando você tentar acessar os logs de auditoria ou as entradas usando a API, verifique se sua conta faz parte da função **Leitor de segurança** ou **Leitor de relatório** no locatário do Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Erro: O aplicativo não tem a permissão 'Ler dados do diretório' do AAD 

Siga as etapas nos [Pré-requisitos para acessar a API de relatório do Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) para garantir que seu aplicativo esteja em execução com o conjunto certo de permissões. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Erro: API do Microsoft Graph ausente 'Leia todos os dados do registro de auditoria'

Siga as etapas nos [Pré-requisitos para acessar a API de relatório do Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) para garantir que seu aplicativo esteja em execução com o conjunto certo de permissões. 

## <a name="next-steps"></a>Próximas etapas

[Use a referência](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
da API de auditoria[Use a referência de API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin) do relatório de atividade de login
