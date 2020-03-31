---
title: 'Autenticação de passagem do AD do Azure: histórico de lançamento de versão | Microsoft Docs'
description: Este artigo lista todos os lançamentos do agente de autenticação de passagem ad do Azure
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 11/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c5d0efe8e662544dc69356c6b17dd7eca6f3a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786445"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD Pass-through Authentication agent: Histórico de lançamento de versão 
 
Os agentes instalados no local que permitem a autenticação de passagem são atualizados regularmente para fornecer novos recursos. Este artigo lista as versões e recursos que são adicionados quando novas funcionalidades são introduzidas. Os agentes de autenticação de passagem são atualizados automaticamente quando uma nova versão é lançada. 

Aqui estão os tópicos relacionados: 

- [Login do usuário com autenticação de passagem do AD do Azure](how-to-connect-pta.md) 
- [Instalação do agente de autenticação ad pass-through do Azure](how-to-connect-pta-quick-start.md) 



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Status de liberação 
22/1/2019: Liberado para download  
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 
- Adicionado suporte para canais confiáveis do Service Bus para adicionar outra camada de resiliência de conexão para conexões de saída 
- Impor TLS 1.2 durante o registro do agente 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Status de liberação 
4/10/2018: Liberado para download  
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 
- Suporte à conexão do soquete web 
- Defina o TLS 1.2 como o protocolo padrão para o agente 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Status de liberação 
31/1/2018: Liberado para download  
### <a name="fixed-issues"></a>Problemas corrigidos 

- Corrigimos um bug que causou alguns vazamentos de memória no agente. 
- Atualizei a versão do Azure Service Bus, que inclui uma correção de bugs para problemas de tempo de tempo. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Status de liberação 
26/11/2017: Liberado para download  
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 
- Adicionado suporte para conexões baseadas em websocket entre o agente e os serviços AD do Azure para melhorar a resiliência da conexão 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Status de liberação 
25/11/2017: Liberado para download  
### <a name="fixed-issues"></a>Problemas corrigidos 
- Corrigimos bugs relacionados ao cache DNS para cenários proxy padrão 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Status de liberação 
17/10/2017: Liberado para download  
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 
- Adicionado a funcionalidade de cache DNS para conexões de saída para adicionar resiliência a partir de falhas de DNS 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Status de liberação 
31/08/2017: Liberado para download  
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 
- Versão GA do agente de autenticação AD Pass-through do Azure 

## <a name="next-steps"></a>Próximas etapas

- [Entrada do usuário com autenticação de passagem do Azure Active Directory](how-to-connect-pta.md)