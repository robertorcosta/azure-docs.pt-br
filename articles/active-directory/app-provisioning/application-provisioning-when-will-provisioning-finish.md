---
title: O provisionamento do usuário para o aplicativo da galeria do Azure AD está demorando horas ou mais
description: Como descobrir por que o provisionamento para seu aplicativo está demorando mais do que o esperado
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22d19733fbeb162fba55dd732c10e552879c78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77522638"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>O provisionamento do usuário para um aplicativo da Galeria do Azure AD está levando horas ou mais

Ao habilitar o provisionamento automático para um aplicativo pela primeira vez, o ciclo inicial pode levar de 20 minutos a várias horas, dependendo do tamanho do diretório do Azure AD e do número de usuários no escopo para provisionamento. 

As sincronizações subsequentes após o ciclo inicial serão mais rápidas, pois o serviço de provisionamento armazena as marcas d' água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes.

## <a name="how-to-improve-provisioning-performance"></a>Como melhorar o desempenho do provisionamento

Se o ciclo inicial estiver demorando mais do que algumas horas, há uma coisa que você pode fazer para melhorar o desempenho:

-   **Filtros de escopo de usuário.** Filtros de escopo permitem ajustar os dados que o serviço de provisionamento extrai do Azure AD filtrando usuários com base em valores de atributo específicos. Para obter mais informações sobre filtros de escopo, consulte [provisionamento de aplicativos com base em atributo com filtros de escopo](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Próximas etapas
[Automatizar o provisionamento e o desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](user-provisioning.md)

