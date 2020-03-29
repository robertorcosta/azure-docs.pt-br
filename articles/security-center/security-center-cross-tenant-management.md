---
title: Gestão entre inquilinos no Azure Security Center | Microsoft Docs
description: Aprenda a configurar o gerenciamento de inquilinos cruzados para gerenciar a postura de segurança de vários inquilinos no Security Center usando o gerenciamento de recursos delegado do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 2aeb2ab4cfb4ed5e8652638aaced320cc7119d3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919474"
---
# <a name="cross-tenant-management-in-security-center"></a>Gestão entre inquilinos no Security Center

O gerenciamento entre inquilinos permite que você visualize e gerencie a postura de segurança de vários inquilinos no Security Center, aproveitando [o gerenciamento de recursos delegados do Azure.](../lighthouse/concepts/azure-delegated-resource-management.md) Gerencie vários inquilinos de forma eficiente, a partir de uma única visão, sem ter que fazer login no diretório de cada inquilino.

- Os provedores de serviços podem gerenciar a postura de segurança dos recursos, para vários clientes, de dentro de seu próprio inquilino.

- Equipes de segurança de organizações com vários inquilinos podem visualizar e gerenciar sua postura de segurança a partir de um único local.

## <a name="set-up-cross-tenant-management"></a>Configurar a gestão entre inquilinos

Configure a gestão de inquilinos cruzados, delegando o acesso aos recursos dos inquilinos gerenciados ao seu próprio inquilino usando [o gerenciamento de recursos delegados do Azure.](../lighthouse/concepts/azure-delegated-resource-management.md)

> [!NOTE]
> O gerenciamento de recursos delegados do Azure é um dos principais componentes do Azure Lighthouse.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Como funciona o gerenciamento entre locatários no Security Center

Você é capaz de revisar e gerenciar assinaturas em vários inquilinos da mesma forma que você gerencia várias assinaturas em um único inquilino.

Na barra de menu superior, clique no ícone do filtro e selecione as assinaturas, do diretório de cada inquilino, que você gostaria de ver.

  ![Inquilinos de filtro](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

As visões e ações são basicamente as mesmas. Estes são alguns exemplos:

- **Gerenciar políticas de segurança**: De uma só vez, gerencie a postura de segurança de muitos recursos com [políticas,](tutorial-security-policy.md)tome ações com recomendações de segurança e colete e gerencie dados relacionados à segurança.
- **Melhore o Secure Score e a postura**de conformidade : A visibilidade do inquilino cruzado permite que você visualize a postura geral de segurança de todos os seus inquilinos e onde e como melhorar melhor a pontuação [segura](security-center-secure-score.md) e a postura de [conformidade](security-center-compliance-dashboard.md) para cada um deles.
- **Remediar recomendações**: Monitorar e remediar uma [recomendação](security-center-recommendations.md) para muitos recursos de vários inquilinos ao mesmo tempo. Em seguida, você pode enfrentar imediatamente as vulnerabilidades que apresentam o maior risco entre todos os inquilinos.
- **Gerenciar alertas**: Detecte [alertas](security-center-alerts-overview.md) em todos os diferentes inquilinos. Tome medidas sobre recursos que estão fora de conformidade com as etapas de [remediação](security-center-managing-and-responding-alerts.md)acionáveis.

- **Gerenciar recursos avançados de defesa na nuvem e muito mais**: Gerenciar os vários serviços de proteção contra ameaças, como acesso ao [VM just-in-time (JIT),](security-center-just-in-time.md) [endurecimento de rede adaptável,](security-center-adaptive-network-hardening.md) [controles de aplicativos adaptativos](security-center-adaptive-application.md)e muito mais.
 
## <a name="next-steps"></a>Próximas etapas
Este artigo explica como funciona a gestão entre inquilinos no Security Center. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Fortaleça sua postura de segurança com o Azure Security Center](security-center-monitoring.md) - Saiba como monitorar a saúde de seus recursos do Azure.
* [Perguntas frequentes sobre a Central de Segurança do Azure](faq-general.md): encontre as perguntas frequentes sobre como usar o serviço.
* [Saiba mais sobre o Azure Lighthouse em cenários corporativos](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) - Descubra como o Azure Lighthouse pode simplificar o gerenciamento de inquilinos cruzados dentro de uma empresa que usa vários inquilinos Azure AD.