---
title: 'Azure AD Connect agente de provisionamento em nuvem: Upgrade automático | Microsoft Docs'
description: Este artigo descreve o recurso de upgrade automático incorporado no agente de provisionamento em nuvem Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190306"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect agente de provisionamento de nuvem: Atualização automática

Certificar-se de que a instalação do agente de provisionamento em nuvem do Azure Active Directory (Azure AD) Conecte a instalação do agente de provisionamento em nuvem sempre atualizado é fácil com o recurso de upgrade automático.

O agente está instalado aqui: "Arquivos do programa\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"

Para verificar sua versão, clique com o botão direito do mouse no executável e selecione propriedades e, em seguida, detalhes.

![Versão do arquivo do agente](media/how-to-automatic-upgrade/agent1.png)

O updater do agente está instalado aqui: "Arquivos do programa\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

Para verificar sua versão, clique com o botão direito do mouse no executável e selecione propriedades e, em seguida, detalhes.

![Versão do agente updater](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Desinstalar o agente
Para remover o agente, vá para **Desinstalar ou alterar um programa** e desinstalar o seguinte:

- **Atualizador do Agente do Microsoft Azure AD Connect**
- **Agente de Provisionamento do Microsoft Azure AD Connect**
- **Pacote do Agente de Provisionamento do Microsoft Azure AD Connect**

![Remoção do agente](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)

