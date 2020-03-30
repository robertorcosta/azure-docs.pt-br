---
title: Limitações da colaboração B2B - Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: ffee01488ecf658ce02a20a41252aca19288667c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263355"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitações da colaboração B2B do Azure AD
A colaboração B2B do Azure Active Directory (Azure AD) está sujeita, atualmente, às limitações descritas neste artigo.

## <a name="possible-double-multi-factor-authentication"></a>Possível autenticação multifator dupla
Com B2B do Azure AD é possível impor a autenticação multifator na organização do recurso (a organização emissora do convite). As razões para essa abordagem são detalhadas no [Acesso Condicional para usuários de colaboração B2B](conditional-access.md). Se um parceiro já tiver uma autenticação multifator configurada e aplicada, é possível que os usuários do parceiro tenham que executar a autenticação uma vez na organização inicial e novamente na sua.

## <a name="instant-on"></a>Instant-on
Nos fluxos de colaboração B2B, adicionamos usuários ao diretório e os atualizamos dinamicamente durante o resgate do convite, atribuição do aplicativo e assim por diante. As atualizações e as gravações em geral ocorrem em uma instância do diretório e devem ser replicadas em todas as instâncias. A replicação estará concluída quando todas as instâncias estiverem atualizadas. Às vezes, quando o objeto é gravado ou atualizado em uma instância e a chamada para recuperar esse objeto for para outra instância, poderão ocorrer latências de replicação. Se isso acontecer, atualize ou tente novamente. Se você estiver gravando um aplicativo utilizando nossa API, então, tentativas com algumas retiradas é uma boa prática defensiva para aliviar esse problema.

## <a name="azure-ad-directories"></a>Diretórios do AD do Azure
O Azure AD B2B está sujeito aos limites de diretório de serviço do Azure AD. Para obter detalhes sobre o número de diretórios que um usuário pode criar e o número de diretórios aos quais um usuário ou usuário convidado pode pertencer, consulte [Limites e restrições do serviço do Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Nuvens nacionais
[Nuvens nacionais](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) são casos fisicamente isolados de Azure. A colaboração B2B não é suportada através das fronteiras nacionais de nuvem. Por exemplo, se o seu inquilino do Azure está na nuvem pública e global, você não pode convidar um usuário cuja conta está em uma nuvem nacional. Para colaborar com o usuário, solicite outro endereço de e-mail ou crie uma conta de usuário membro para eles em seu diretório.

## <a name="azure-us-government-clouds"></a>Nuvens do governo dos EUA
Dentro da nuvem do governo dos EUA azure, a colaboração B2B é atualmente apoiada apenas entre inquilinos que estão dentro da nuvem do governo dos EUA azure e que ambos apoiam a colaboração B2B. Se você convidar um usuário em um inquilino que não faz parte da nuvem do Governo dos EUA do Azure ou que ainda não suporta a colaboração B2B, o convite falhará ou o usuário não poderá resgatar o convite. Para obter detalhes sobre outras limitações, consulte [Variações Premium Premium e P2 do Diretório Ativo do Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Como posso dizer se a colaboração B2B está disponível no meu inquilino do governo dos EUA?
Para descobrir se o seu inquilino de nuvem do Governo dos EUA apóia a colaboração B2B, faça o seguinte:

1. Em um navegador, vá para a URL a * &lt;&gt;* seguir, substituindo o nome do inquilino pelo nome do inquilino :

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Encontre `"tenant_region_scope"` na resposta JSON:

   - Se `"tenant_region_scope":"USGOV”` aparecer, o B2B é suportado.
   - Se `"tenant_region_scope":"USG"` aparecer, o B2B não será suportado.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração Azure AD B2B?](what-is-b2b.md)
- [Delegação de convites de colaboração B2B](delegate-invitations.md)

