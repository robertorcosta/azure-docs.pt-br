---
title: 'Agente de provisionamento do Azure AD Connect: histórico de lançamento de versão | Microsoft Docs'
description: Este artigo lista todas as versões do agente de provisionamento do Azure AD Connect e descreve os novos recursos e problemas corrigidos
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
ms.date: 02/04/2020
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: cac9eac2d17fa0a4f1db487d4c7f8beb67de24a9
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030645"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agente de provisionamento do Azure AD Connect: histórico de lançamento de versão
Este artigo lista as versões e os recursos do agente de provisionamento do Azure Active Directory Connect que foram lançados. A equipe do Azure AD atualiza regularmente o agente de provisionamento com novos recursos e funcionalidades. O agente de provisionamento é atualizado automaticamente quando uma nova versão é liberada. 

Recomendamos habilitar a atualização automática para seus agentes para garantir que você tenha os recursos e correções de bugs mais recentes. A Microsoft fornece suporte direto para a versão mais recente do agente e uma versão anterior.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Previsão de lançamento

4 de dezembro de 2019: liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Inclui suporte para [provisionamento Azure ad Connect nuvem](../cloud-provisioning/what-is-cloud-provisioning.md) para sincronizar usuários, contatar e agrupar dados do Active Directory local para o Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Previsão de lançamento

9 de setembro de 2019: liberado para atualização automática

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Capacidade de configurar rastreamento e log adicionais para depuração de problemas do agente de provisionamento
* Capacidade de buscar somente os atributos do AD do Azure que estão configurados no mapeamento para melhorar o desempenho da sincronização

### <a name="fixed-issues"></a>Problemas corrigidos

* Corrigido um bug em que o agente entrou em um estado sem resposta se houvesse problemas com falhas de conexão do Azure AD
* Correção de um bug que causou problemas quando dados binários foram lidos de Azure Active Directory
* Correção de um bug em que o agente falhou ao renovar a confiança com o serviço de identidade híbrida na nuvem

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Previsão de lançamento

23 de janeiro de 2019: liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Remodelado o agente de provisionamento e a arquitetura de conector para melhorar o desempenho, a estabilidade e a confiabilidade 
* Simplifica a configuração do agente de provisionamento usando o assistente de instalação orientado por interface do usuário 
* Suporte adicionado para atualizações automáticas de agente

