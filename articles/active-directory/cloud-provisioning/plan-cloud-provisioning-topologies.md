---
title: Azure AD Connect cloud provisioning topologias e cenários suportados
description: Este tópico descreve os pré-requisitos e os requisitos de hardware de provisionamento em nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 386af46bbee623d37bc914d2ee9130c914c6c885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620868"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect cloud provisioning topologias e cenários suportados
Este artigo descreve várias topologias de nuvem do Azure Active Directory (Azure AD) que usam o provisionamento em nuvem do Azure AD Connect. Este artigo inclui apenas configurações e cenários suportados.

> [!IMPORTANT]
> A Microsoft não suporta a modificação ou operação do provisionamento em nuvem do Azure AD Connect fora das configurações ou ações formalmente documentadas. Qualquer uma dessas configurações ou ações pode resultar em um estado inconsistente ou não suportado do provisionamento em nuvem do Azure AD Connect. Consequentemente, a Microsoft não pode fornecer suporte técnico para essas implantações.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Coisas para lembrar sobre todos os cenários e topologias
A seguir está uma lista de informações a serem guardadas ao selecionar uma solução.

- Usuários e grupos devem ser identificados exclusivamente em todas as florestas
- A correspondência entre florestas não ocorre com o provisionamento de nuvens
- Um usuário ou grupo deve ser representado apenas uma vez em todas as florestas
- A âncora de origem para objetos é escolhida automaticamente.  Ele usa ms-DS-ConsistencyGuid se presente, caso contrário, ObjectGUID é usado.
- Não é possível alterar o atributo usado para a âncora de origem.

## <a name="single-forest-single-azure-ad-tenant"></a>Floresta única, locatário único do Azure AD
![Topologia de uma única floresta e um único locatário](media/plan-cloud-provisioning-topologies/single-forest.png)

A topologia mais simples é uma floresta única no local, com um ou vários domínios, e um único inquilino Azure AD.  Para um exemplo deste cenário, consulte [Tutorial: Uma única floresta com um único inquilino Azure AD](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Multi-floresta, único inquilino Azure AD
![Topologia para uma multifloresta e um único inquilino](media/plan-cloud-provisioning-topologies/multi-forest.png)

Uma topologia comum é uma floresta de vários AD, com um ou vários domínios, e um único inquilino Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Floresta existente com Azure AD Connect, nova floresta com provisionamento de nuvens
![Topologia de uma única floresta e um único locatário](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Este cenário é semelhante ao cenário multiflorestal, no entanto, este envolve um ambiente Azure AD Connect existente e, em seguida, trazer uma nova floresta usando o provisionamento de nuvem Azure AD Connect.  Para um exemplo deste cenário, consulte [Tutorial: Uma floresta existente com um único inquilino Azure AD](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Pilotando o provisionamento de nuvem Azure AD Connect em uma floresta ad híbrida existente
![Topologia para uma única floresta](media/plan-cloud-provisioning-topologies/migrate.png) e um único inquilino O cenário de pilotagem envolve a existência tanto do Azure AD Connect quanto do Azure AD Connect na mesma floresta e de escopo dos usuários e grupos de acordo. NOTA: Um objeto deve estar no escopo de apenas uma das ferramentas. 

Para um exemplo deste cenário, consulte [Tutorial: O provisionamento de nuvem Pilot Azure AD Connect em uma floresta AD sincronizada existente](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)

