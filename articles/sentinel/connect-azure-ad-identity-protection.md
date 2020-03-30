---
title: Conecte os dados de proteção de identidade do Azure AD ao Azure Sentinel
description: Saiba como conectar os dados de proteção de identidade do Azure AD ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: 7d42ff28ddd2d883feb25139096d781efe64d50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588562"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Conecte dados da Proteção de Identidade AD do Azure



Você pode transmitir logs do [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) para o Azure Sentinel para transmitir alertas no Azure Sentinel para visualizar dashboards, criar alertas personalizados e melhorar a investigação. O Azure Active Directory Identity Protection fornece uma visão consolidada de usuários de risco, detecções de risco e vulnerabilidades, com a capacidade de remediar riscos imediatamente e definir políticas para remediar automaticamente eventos futuros. O serviço é baseado na experiência da Microsoft protegendo as identidades dos consumidores e ganha uma tremenda precisão do sinal de mais de 13 bilhões de logins por dia. 


## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma [licença Premium P1 ou P2 do Diretório Ativo do Azure](https://azure.microsoft.com/pricing/details/active-directory/)
- Usuário com permissões globais de administrador ou administrador de segurança


## <a name="connect-to-azure-ad-identity-protection"></a>Conecte-se à proteção de identidade AD do Azure

Se você já tiver proteção de identidade Azure AD, certifique-se de que ele está [ativado em sua rede](../active-directory/identity-protection/overview-identity-protection.md).
Se o Azure AD Identity Protection for implantado e receber dados, os dados de alerta podem ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados** e clique no azulejo **azure AD Identity Protection.**

2. Clique **em Conectar** para iniciar a transmissão de eventos de Proteção de Identidade Azure AD no Azure Sentinel.


6. Para usar o esquema relevante no Log Analytics para os alertas de Proteção de Identidade AD do Azure, procure **o SecurityAlert**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Azure AD Identity Protection ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
