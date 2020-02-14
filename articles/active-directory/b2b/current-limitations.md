---
title: Limitações da colaboração B2B – Azure Active Directory | Microsoft Docs
description: Limitações atuais à colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1717897261404a2ab8df723c280c9be6a2dacea4
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77196126"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitações da colaboração B2B do Azure AD
A colaboração B2B do Azure Active Directory (Azure AD) está sujeita, atualmente, às limitações descritas neste artigo.

## <a name="possible-double-multi-factor-authentication"></a>Possível autenticação multifator dupla
Com B2B do Azure AD é possível impor a autenticação multifator na organização do recurso (a organização emissora do convite). Os motivos para essa abordagem são detalhados no [acesso condicional para usuários de colaboração B2B](conditional-access.md). Se um parceiro já tiver uma autenticação multifator configurada e aplicada, é possível que os usuários do parceiro tenham que executar a autenticação uma vez na organização inicial e novamente na sua.

## <a name="instant-on"></a>Instant-on
Nos fluxos de colaboração B2B, adicionamos usuários ao diretório e os atualizamos dinamicamente durante o resgate do convite, atribuição do aplicativo e assim por diante. As atualizações e as gravações em geral ocorrem em uma instância do diretório e devem ser replicadas em todas as instâncias. A replicação estará concluída quando todas as instâncias estiverem atualizadas. Às vezes, quando o objeto é gravado ou atualizado em uma instância e a chamada para recuperar esse objeto for para outra instância, poderão ocorrer latências de replicação. Se isso acontecer, atualize ou tente novamente. Se você estiver gravando um aplicativo utilizando nossa API, então, tentativas com algumas retiradas é uma boa prática defensiva para aliviar esse problema.

## <a name="azure-ad-directories"></a>Diretórios do AD do Azure
O Azure AD B2B está sujeito aos limites de diretório de serviço do Azure AD. Para obter detalhes sobre o número de diretórios que um usuário pode criar e o número de diretórios aos quais um usuário ou usuário convidado pode pertencer, consulte [Limites e restrições do serviço do Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Nuvens nacionais
As [nuvens nacionais](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) são instâncias fisicamente isoladas do Azure. A colaboração B2B não tem suporte em limites de nuvem nacional. Por exemplo, se o seu locatário do Azure estiver na nuvem pública e global, você não poderá convidar um usuário cuja conta esteja em uma nuvem nacional. Para colaborar com o usuário, peça a ele outro endereço de email ou crie uma conta de usuário membro para eles em seu diretório.

## <a name="azure-us-government-clouds"></a>Nuvens do governo dos EUA do Azure
Na nuvem do governo dos EUA do Azure, atualmente, a colaboração B2B só tem suporte entre locatários que estão na nuvem do governo dos EUA do Azure e que ambos dão suporte à colaboração B2B. Se você convidar um usuário em um locatário que não faz parte da nuvem do governo dos EUA do Azure ou que ainda não dá suporte à colaboração B2B, o convite falhará ou o usuário não poderá resgatar o convite. Para obter detalhes sobre outras limitações, consulte [Azure Active Directory Premium as variações P1 e P2](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Como saber se a colaboração B2B está disponível no meu locatário do governo dos EUA do Azure?
Para descobrir se o seu locatário de nuvem do governo dos EUA do Azure dá suporte à colaboração B2B, faça o seguinte:

1. Em um navegador, vá para a URL a seguir, substituindo o nome do locatário por *&lt;tenantname&gt;* :

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Localizar `"tenant_region_scope"` na resposta JSON:

   - Se `"tenant_region_scope":"USGOV”` aparecer, o B2B terá suporte.
   - Se `"tenant_region_scope":"USG"` for exibido, o B2B não terá suporte.
 

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Delegação de convites de colaboração B2B](delegate-invitations.md)

