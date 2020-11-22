---
ms.openlocfilehash: 46c27fa2073ea1178fdf01ea3cbf96fe86954a6a
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255085"
---
Este artigo lista as versões e os recursos do agente de provisionamento do Azure Active Directory Connect que foram lançados. A equipe do Azure AD atualiza regularmente o agente de provisionamento com novos recursos e funcionalidades. O agente de provisionamento é atualizado automaticamente quando uma nova versão é liberada. 

A Microsoft fornece suporte direto para a versão mais recente do agente e uma versão anterior.

## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Status de liberação

XX de novembro de 2020: liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Suporte para [gMSA](../articles/active-directory/cloud-provisioning/how-to-prerequisites.md#group-managed-service-accounts)
* Suporte para grupos com tamanho de 1500 Membros (durante a sincronização Delta)
* Suporte para grupos grandes com tamanho de membro de até 15 k
* Melhorias de sincronização inicial
* Log detalhado avançado
* Introdução do [módulo AADCloudSyncTools PowerShell](../articles/active-directory/cloud-provisioning/reference-powershell.md)
* Limitações corrigidas para permitir que o agente seja instalado em um servidor que não seja o inglês
* Suporte para filtragem de PHS somente para objetos no escopo (originalmente, estamos sincronizando hashes de senha para todos os objetos)
* Correção do problema de vazamento de memória no agente
* Logs de provisionamento aprimorados


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Status de liberação

4 de dezembro de 2019: liberado para download

### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos

* Inclui suporte para [provisionamento Azure ad Connect nuvem](../articles/active-directory/cloud-provisioning/what-is-cloud-provisioning.md) para sincronizar usuários, contatar e agrupar dados do Active Directory local para o Azure AD


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


