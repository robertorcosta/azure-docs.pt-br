---
title: Como personalizar uma regra de sincronização no Azure AD Connect | Microsoft Docs
description: Este tópico fornece etapas para solucionar problemas com a instalação do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60351029"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Como personalizar uma regra de sincronização

## <a name="recommended-steps"></a>**Etapas Recomendadas**

Você pode usar o editor de regra de sincronização para editar ou criar uma nova regra de sincronização. Você precisa ser um usuário avançado para fazer alterações nas regras de sincronização. Qualquer alteração incorreta pode resultar na exclusão de objetos do seu diretório de destino. Leia os [Documentos Recomendados](#recommended-documents) para obter conhecimentos em regras de sincronização. Para modificar uma regra de sincronização, percorra as etapas a seguir:

* Inicie o editor de sincronização no menu de aplicativo na área de trabalho conforme mostrado abaixo:

    ![Menu do Editor de Regra de Sincronização](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Para personalizar uma regra de sincronização padrão, clone a regra existente clicando no botão "Editar" no Editor de Regras de Sincronização, que criará uma cópia da regra padrão, e desabilite-a. Salve a regra clonada com uma precedência menor que 100.  A precedência determina qual regra vence (valor numérico mais baixo) uma resolução de conflitos quando há um conflito de fluxo de atributo.

    ![Editor de Regra de Sincronização](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Ao modificar um atributo específico, o ideal é manter o atributo modificador na regra clonada.  Em seguida, habilite a regra padrão para que o atributo modificado venha da regra clonada e outros atributos sejam escolhidos da regra padrão. 

* Observe que, no caso em que o valor calculado do atributo modificado é NULL na sua regra clonada e não é NULL na regra padrão, o valor não NULL vencerá e substituirá o NULL. Se não quiser que um valor nulo seja substituído por um valor não nulo, atribua AuthoritativeNull à sua regra clonada.

* Para modificar uma regra de **Saída**, altere o filtro de editor de regra de sincronização.

## <a name="recommended-documents"></a>**Documentos recomendados**
* [Sincronização do Azure AD Connect: conceitos técnicos](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Sincronização do Azure AD Connect: noções básicas sobre a arquitetura](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Sincronização do Azure AD Connect: Noções básicas sobre a configuração padrão](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Azure Active Directory Connect Sync: noções básicas sobre usuários, grupos e contatos](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Sincronização Azure AD Connect: Atributos de sombra](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Próximas etapas
- [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md).
- [O que é a identidade híbrida?](whatis-hybrid-identity.md).
