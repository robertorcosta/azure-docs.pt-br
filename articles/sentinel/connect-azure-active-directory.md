---
title: Conecte os dados do Azure AD ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar os dados do Azure Active Directory ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588613"
---
# <a name="connect-data-from-azure-active-directory"></a>Conecte dados do Azure Active Directory



O Azure Sentinel permite coletar dados do [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmiti-los para o Azure Sentinel. Você pode optar por transmitir [logs de login](../active-directory/reports-monitoring/concept-sign-ins.md) e registros de [auditoria](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Pré-requisitos

- Se você quiser exportar dados de login do Active Directory, você deve ter uma licença Azure AD P1 ou P2.

- Usuário com permissões globais de admin ou de segurança no inquilino de onde deseja transmitir os logs.

- Para poder ver o status da conexão, você deve ter permissão para acessar os registros de diagnóstico do Azure AD. 


## <a name="connect-to-azure-ad"></a>Conecte-se ao AD do Azure

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco **do Azure Active Directory.**

1. Ao lado dos logs que deseja transmitir no Azure Sentinel, clique **em Conectar**.

1. Você pode selecionar se deseja que os alertas do Azure AD gerem automaticamente incidentes no Azure Sentinel. Em **Criar incidentes**, selecione **Habilitar** para habilitar a regra analítica padrão que cria incidentes automaticamente com base em alertas gerados no serviço de segurança conectado. É possível editar essa regra em **Análise** e depois em **Regras ativas**.

1. Para usar o esquema relevante no Log Analytics para os alertas azure AD, procure **por LoginLogs** e **AuditLogs**.




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Azure AD ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
