---
title: Conectar dados do Azure AD ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar Azure Active Directory dados ao Azure Sentinel.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588613"
---
# <a name="connect-data-from-azure-active-directory"></a>Conectar dados de Azure Active Directory



O Azure Sentinel permite coletar dados de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmiti-los para o Azure Sentinel. Você pode optar por transmitir [logs de entrada](../active-directory/reports-monitoring/concept-sign-ins.md) e [logs de auditoria](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Prerequisites

- Se você quiser exportar dados de entrada do Active Directory, deverá ter uma licença do Azure AD P1 ou P2.

- Usuário com permissões de administrador global ou de administrador de segurança no locatário do qual você deseja transmitir os logs.

- Para poder ver o status da conexão, você deve ter permissão para acessar os logs de diagnóstico do Azure AD. 


## <a name="connect-to-azure-ad"></a>Conecte-se ao AD do Azure

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no bloco **Azure Active Directory** .

1. Ao lado dos logs que você deseja transmitir para o Azure Sentinel, clique em **conectar**.

1. Você pode selecionar se deseja que os alertas do Azure AD gerem incidentes automaticamente no Azure Sentinel. Em **Criar incidentes**, selecione **Habilitar** para habilitar a regra analítica padrão que cria incidentes automaticamente com base em alertas gerados no serviço de segurança conectado. É possível editar essa regra em **Análise** e depois em **Regras ativas**.

1. Para usar o esquema relevante no Log Analytics para os alertas do Azure AD, procure **SigninLogs** e **AuditLogs**.




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar o Azure AD ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
