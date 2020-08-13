---
title: Conectar dados de Azure Active Directory ao Azure Sentinel | Microsoft Docs
description: Saiba como coletar dados de Azure Active Directory e transmitir logs de entrada e logs de auditoria do Azure AD para o Azure Sentinel.
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
ms.openlocfilehash: a19ced1cb9496bddd1f9aa7ea9e3eb58ba7eea3d
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185682"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Conectar dados de Azure Active Directory (Azure AD)



O Azure Sentinel permite coletar dados de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmiti-los para o Azure Sentinel. Você pode optar por transmitir [logs de entrada](../active-directory/reports-monitoring/concept-sign-ins.md) e [logs de auditoria](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer licença do Azure AD (gratuito/O365/P1/P2) pode ser integrada com os logs de entrada Azure Monitor e de ingestão no Azure sentinela. Encargos adicionais por gigabyte serão aplicados por Azure Monitor ou pelo Azure Sentinel.

- Usuário com permissões de administrador global ou de administrador de segurança no locatário do qual você deseja transmitir os logs.

- Para poder ver o status da conexão, você deve ter permissão para acessar os logs de diagnóstico do Azure AD. 


## <a name="connect-to-azure-active-directory"></a>Conectar ao Active Directory do Azure

1. No Azure Sentinel, selecione **conectores de dados** no menu de navegação.

1. Na Galeria de conectores de dados, selecione **Azure Active Directory** e, em seguida, clique no botão **abrir página do conector** .

1. Marque as caixas de seleção ao lado dos logs que você deseja transmitir para o Azure Sentinel e clique em **conectar**.

1. Você pode selecionar se deseja que os alertas do Azure AD gerem incidentes automaticamente no Azure Sentinel. Em **Criar incidentes**, selecione **Habilitar** para habilitar a regra analítica padrão que cria incidentes automaticamente com base em alertas gerados no serviço de segurança conectado. É possível editar essa regra em **Análise** e depois em **Regras ativas**.

1. Para usar o esquema relevante no Log Analytics para consultar alertas do Azure AD, digite `SigninLogs` ou `AuditLogs` na janela de consulta.




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar Azure Active Directory ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
