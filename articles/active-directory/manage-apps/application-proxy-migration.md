---
title: Atualizar para o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: Escolha qual solução de proxy será a melhor se você estiver atualizando do Microsoft Forefront ou do Unified Access Gateway.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4790dc7ebeeee3407e89bcf38d7e3f25699ed328
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "67108418"
---
# <a name="compare-remote-access-solutions"></a>Comparar as soluções de acesso remoto

O Azure Active Directory Application Proxy é uma das duas soluções de acesso remoto que a Microsoft oferece. O outro é o Proxy de Aplicativo Web, a versão local. Essas duas soluções substituem os produtos anteriores oferecidos pela Microsoft: Microsoft Forefront Threat Management Gateway (TMG) e Unified Access Gateway (UAG). Use este artigo para ver uma comparação entre estas quatro soluções. Para aqueles que ainda utilizam as soluções TMG ou UAG preteridas, use este artigo para ajudar a planejar a migração para um Proxy de Aplicativo. 


## <a name="feature-comparison"></a>Comparação de recursos

Use essa tabela para entender a comparação entre o Threat Management Gateway (TMG), o Unified Access Gateway (UAG), o Proxy de Aplicativo da Web (WAP) e o Proxy de Aplicativo do Azure AD (AP).

| Recurso | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| Autenticação de certificado | Sim | Sim | - | - |
| Publicar seletivamente os aplicativos de navegador | Sim | Sim | Sim | Sim |
| Pré-autenticação e logon único | Sim | Sim | Sim | Sim | 
| Firewall de Camada 2/3 | Sim | Sim | - | - |
| Recursos de proxy de encaminhamento | Sim | - | - | - |
| Funcionalidades de VPN | Sim | Sim | - | - |
| Suporte avançado a protocolo | - | Sim | Sim, se estiver em execução sobre HTTP | Sim, se a execução for feita via HTTP ou por meio do Gateway de Área de Trabalho Remota |
| Atua como um servidor proxy do ADFS | - | Sim | Sim | - |
| Um portal para acesso de aplicativo | - | Sim | - | Sim |
| Conversão de link de corpo de resposta | Sim | Sim | - | Sim | 
| Autenticação com cabeçalhos | - | Sim | - | Sim, com PingAccess | 
| Segurança em escala de nuvem | - | - | - | Sim | 
| Acesso condicional | - | Sim | - | Sim |
| Nenhum componente na zona desmilitarizada (DMZ) | - | - | - | Sim |
| Nenhuma conexão de entrada | - | - | - | Sim |

Para a maioria dos cenários, recomendamos o Azure Proxy de Aplicativo do AD como a solução moderna. P Proxy de Aplicativo Web é preferencial apenas em cenários que exigem um servidor proxy para o AD FS e não pode usar domínios personalizados no Azure Active Directory. 

O Proxy de Aplicativo do Azure AD oferece benefícios exclusivos quando comparados a produtos semelhantes, incluindo:

- Extensão do AD do Azure para recursos locais
   - Proteção e segurança em escala de nuvem
   - Recursos como acesso condicional e autenticação multifator são fáceis de habilitar
- Nenhum componente na zona desmilitarizada
- Nenhuma conexão de entrada necessária
- Um painel de acesso em que os usuários poderão procurar todos os aplicativos, incluindo o O365, os aplicativos SaaS integrados do Azure AD e seus aplicativos Web locais. 


## <a name="next-steps"></a>Próximas etapas

- [Usar o Aplicativo do Azure AD para fornecer acesso remoto seguro a aplicativos locais](application-proxy.md)
