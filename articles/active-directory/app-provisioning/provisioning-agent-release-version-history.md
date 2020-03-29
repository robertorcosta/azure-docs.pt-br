---
title: 'Azure AD Connect Provisioning Agent: Histórico de lançamento de versão | Microsoft Docs'
description: Este artigo lista todos os lançamentos do Azure AD Connect Provisioning Agent e descreve novos recursos e problemas fixos
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183237"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect Provisioning Agent: Histórico de lançamento de versão
Este artigo lista as versões e recursos do Azure Active Directory Connect Provisioning Agent que foram lançados. A equipe do Azure AD atualiza regularmente o Provisionand Agent com novos recursos e funcionalidades. O Provisionamento é atualizado automaticamente quando uma nova versão é lançada. 

A Microsoft oferece suporte direto para a versão mais recente do agente e uma versão anterior.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Status de liberação

4 de dezembro de 2019: Liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Inclui suporte para [provisionamento em nuvem do Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) para sincronizar dados de usuário, contato e grupo do Active Directory on-premises para o Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Status de liberação

9 de setembro de 2019: Lançado para atualização automática

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Capacidade de configurar rastreamento e registro adicionais para problemas de agente de provisionamento de depuração
* Capacidade de buscar apenas os atributos Azure AD que são configurados no mapeamento para melhorar o desempenho da sincronização

### <a name="fixed-issues"></a>Problemas corrigidos

* Corrigimos um bug no qual o agente entrava em um estado sem resposta se houvesse problemas com falhas de conexão AD do Azure
* Corrigimos um bug que causou problemas quando os dados binários foram lidos do Azure Active Directory
* Corrigimos um bug no qual o agente não conseguiu renovar a confiança com o serviço de identidade híbrida em nuvem

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Status de liberação

23 de janeiro de 2019: Liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Renovou o agente de provisionamento e a arquitetura do conector para melhor desempenho, estabilidade e confiabilidade 
* Simplificou a configuração do provisionamento usando o assistente de instalação orientado por interface do usuário 
* Suporte adicionado para atualizações automáticas de agentes

