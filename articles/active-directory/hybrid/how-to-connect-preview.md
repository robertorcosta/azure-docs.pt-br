---
title: 'Azure AD Connect: Recursos em versão prévia | Microsoft Docs'
description: Este tópico descreve em mais detalhes os recursos que estão na visualização no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bb7305fe8ee96697c5c8b2172e32a2a97e5bd6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85358422"
---
# <a name="more-details-about-features-in-preview"></a>Mais detalhes sobre os recursos no modo de visualização
Este tópico descreve como usar recursos presentes atualmente na visualização.

## <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>API de ponto de extremidade da sincronização do Azure AD Connect V2 (versão prévia pública) 

Implantamos um novo ponto de extremidade (API) para o Azure AD Connect que aprimora o desempenho das operações de serviço de sincronização para o Azure Active Directory. Ao utilizar o novo ponto de extremidade V2, você experimentará ganhos de desempenho perceptíveis na exportação e na importação para o Azure AD. Esse novo ponto de extremidade também dá suporte à sincronização de grupos com até 250 mil membros. O uso desse ponto de extremidade também permite que você faça write-back de grupos unificados do O365, sem limite máximo de associação, para seu Active Directory local, quando o write-back de grupo está habilitado.   Para obter mais informações, confira [API do ponto de extremidade de sincronização do Azure AD Connect V2 (versão prévia pública)](how-to-connect-sync-endpoint-api-v2.md).

## <a name="user-writeback"></a>Write-back de usuário
> [!IMPORTANT]
> O recurso de visualização de write-back do usuário foi removido na atualização de agosto de 2015 do Azure AD Connect. Se você tiver habilitado, você deve desabilitar esse recurso.
>
>

## <a name="next-steps"></a>Próximas etapas
Continuar a [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
