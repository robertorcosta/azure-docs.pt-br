---
title: 'Agente de provisionamento do Azure AD Connect: histórico de lançamento de versão | Microsoft Docs'
description: Este artigo lista todas as versões do agente de provisionamento do Azure AD Connect e descreve os novos recursos e problemas corrigidos
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: 9e05d1a85f17800ddf4d77e4e4acba6396a8da47
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781558"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agente de provisionamento do Azure AD Connect: histórico de lançamento de versão

Este artigo lista as versões e os recursos do agente de provisionamento do Azure Active Directory Connect que foram lançados. A equipe do Azure AD atualiza regularmente o agente de provisionamento com novos recursos e funcionalidades. O agente de provisionamento é atualizado automaticamente quando uma nova versão é liberada. 

A Microsoft fornece suporte direto para a versão mais recente do agente e uma versão anterior.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Status de liberação

4 de dezembro de 2019: liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Inclui suporte para [provisionamento Azure ad Connect nuvem](../cloud-provisioning/what-is-cloud-provisioning.md) para sincronizar usuários, contatar e agrupar dados do Active Directory local para o Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Status de liberação

9 de setembro de 2019: liberado para atualização automática

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Capacidade de configurar rastreamento e log adicionais para depuração de problemas do agente de provisionamento
* Capacidade de buscar somente os atributos do AD do Azure que estão configurados no mapeamento para melhorar o desempenho da sincronização

### <a name="fixed-issues"></a>Problemas corrigidos

* Corrigido um bug em que o agente entrou em um estado sem resposta se houvesse problemas com falhas de conexão do Azure AD
* Correção de um bug que causou problemas quando dados binários foram lidos de Azure Active Directory
* Correção de um bug em que o agente falhou ao renovar a confiança com o serviço de identidade híbrida na nuvem

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Status de liberação

23 de janeiro de 2019: liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Remodelado o agente de provisionamento e a arquitetura de conector para melhorar o desempenho, a estabilidade e a confiabilidade 
* Simplifica a configuração do agente de provisionamento usando o assistente de instalação orientado por interface do usuário 
* Suporte adicionado para atualizações automáticas de agente

