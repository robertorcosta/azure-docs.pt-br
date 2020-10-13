---
title: O que é o provisionamento em nuvem do Azure AD Connect. | Microsoft Docs
description: Descreve o provisionamento em nuvem do Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a5a69bedb45f8037f3a97f5ebf4e2128930330a
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91728345"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>O que é o provisionamento em nuvem do Azure AD Connect?
O provisionamento em nuvem do Azure AD Connect é um novo agente da Microsoft projetado para atender e atingir suas metas de identidade híbrida para a sincronização de usuários, grupos e contatos com o Azure AD.  Ele pode ser usado junto com a sincronização do Azure AD Connect e oferece os seguintes benefícios:
    
- Suporte para sincronização com um locatário do Azure AD de um ambiente de floresta do Active Directory desconectado de várias florestas: Os cenários comuns incluem a fusão e aquisição, em que as florestas do AD adquiridas da empresa são isoladas das florestas do AD da empresa pai e das empresas que historicamente tinham várias florestas do AD.
- Instalação simplificada com agentes de provisionamento leves: Os agentes atuam como uma ponte do AD para o Azure AD, com todas as configurações de sincronização gerenciadas na nuvem. 
- Vários agentes de provisionamento podem ser usados para simplificar implantações de alta disponibilidade, especialmente críticas para as organizações que dependem da sincronização de hash de senha do AD para o Azure AD.


![O que é o Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Qual a diferença entre o provisionamento em nuvem do Azure AD Connect e a sincronização do Azure AD Connect?
Com o provisionamento em nuvem do Azure AD Connect, o provisionamento do AD para o Azure AD é orquestrado no Microsoft Online Services. Uma organização só precisa implantar um agente leve que atue como uma ponte entre o Azure AD e o AD em seu ambiente local e de IaaS. A configuração de provisionamento é armazenada no Azure AD e gerenciada como parte do serviço.

## <a name="azure-ad-connect-cloud-provisioning-video"></a>Vídeo sobre o provisionamento em nuvem do Azure AD Connect
O seguinte vídeo breve fornece uma excelente visão geral do provisionamento em nuvem do Azure AD Connect:

> [!VIDEO https://youtube/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-provisioning"></a>Comparação entre o Azure AD Connect e o provisionamento em nuvem

A tabela a seguir fornece uma comparação entre o Azure AD Connect e o provisionamento em nuvem do Azure AD Connect:

| Recurso | Sincronização do Azure Active Directory Connect| Provisionamento em nuvem do Azure Active Directory Connect |
|:--- |:---:|:---:|
|Conectar-se a única floresta do AD local|● |● |
| Conectar-se a várias florestas do AD local |● |● |
| Conectar-se a várias florestas locais do AD desconectadas | |● |
| Modelo de instalação de agente leve | |● |
| Vários agentes ativos para alta disponibilidade | |● |
| Conexão com diretórios LDAP|●| | 
| Suporte para objetos do usuário |● |● |
| Suporte para objetos de grupo |● |● |
| Suporte para objetos de contato |● |● |
| Suporte para objetos de dispositivo |● | |
| Permite a personalização básica para fluxos de atributo |● |● |
| Sincronização de atributos do Exchange Online |● |● |
| Sincronização de atributos de extensão 1-15 |● |● |
| Sincronização de atributos do AD definidos pelo cliente (extensões de diretório) |● | |
| Suporte para Sincronização de Hash de Senha |●|●|
| Suporte para Autenticação de Passagem |●||
| Suporte para federação |●|●|
| Logon Único Contínuo|● |●|
| Oferece suporte à instalação em um controlador de domínio |● |● |
| Suporte para o Windows Server 2012 e Windows Server 2012 R2 |● |● |
| Filtro em Domínios/UOs/grupos |● |● |
| Filtro em valores de atributo de objetos |● | |
| Permitir que um conjunto mínimo de atributos seja sincronizado (MinSync) |● |● |
| Permitir a remoção de atributos do fluxo do AD para o AD do Azure |● |● |
| Permitir a personalização avançada para fluxos de atributo |● | |
| Suporte para write-back (senhas, dispositivos, grupos) |● | |
| Suporte ao Azure AD Domain Services|● | |
| [Write-back híbrido do Exchange](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Suporte para mais de 50 mil objetos por domínio do AD |● | |

## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [Instalar o provisionamento de nuvem](how-to-install.md)
