---
title: Conectar Azure AD Identity Protection dados ao Azure Sentinel
description: Saiba como conectar Azure AD Identity Protection dados ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: 7d42ff28ddd2d883feb25139096d781efe64d50f
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588562"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Conectar dados de Azure AD Identity Protection



Você pode transmitir logs de [Azure ad Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) para o Azure Sentinel para transmitir alertas para o Azure Sentinel para exibir painéis, criar alertas personalizados e melhorar a investigação. O Azure Active Directory Identity Protection fornece uma exibição consolidada em risco de usuários, detecções de risco e vulnerabilidades, com a capacidade de corrigir o risco imediatamente e definir políticas para corrigir automaticamente eventos futuros. O serviço se baseia na experiência da Microsoft em proteger as identidades dos consumidores e ganha uma considerável precisão do sinal de mais de 13.000.000.000 logons por dia. 


## <a name="prerequisites"></a>Prerequisites

- Você deve ter uma [licença Azure Active Directory Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Usuário com permissões de administrador global ou administrador de segurança


## <a name="connect-to-azure-ad-identity-protection"></a>Conectar-se ao Azure AD Identity Protection

Se você já tiver Azure AD Identity Protection, verifique se ele está [habilitado em sua rede](../active-directory/identity-protection/overview-identity-protection.md).
Se Azure AD Identity Protection for implantada e estiver obtendo dados, os dados de alerta poderão ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no bloco **Azure ad Identity Protection** .

2. Clique em **conectar** para iniciar o streaming de Azure ad Identity Protection eventos no Azure Sentinel.


6. Para usar o esquema relevante em Log Analytics para os alertas de Azure AD Identity Protection, procure **SecurityAlert**.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar Azure AD Identity Protection ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
