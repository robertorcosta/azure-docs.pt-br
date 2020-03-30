---
title: Alertas de segurança para ambientes em Azure DevTest Labs
description: Este artigo mostra como visualizar alertas de segurança para um ambiente no DevTest Labs e tomar uma ação apropriada.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588698"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alertas de segurança para ambientes em Azure DevTest Labs
Como usuário de laboratório, agora você pode visualizar os alertas do Azure Security Center para seus ambientes de laboratório. A Central de segurança coleta, analisa e integra automaticamente os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros conectados, como firewall e soluções de proteção de ponto de extremidade, a fim de detectar ameaças reais e reduzir os falsos positivos. Uma lista priorizada de alertas de segurança é exibida na Central de Segurança, junto com as informações necessárias para investigar rapidamente o problema, e recomendações sobre como corrigir um ataque. [Saiba mais sobre alertas de segurança no Azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Pré-requisitos
Atualmente, você pode visualizar alertas de segurança apenas para ambientes de plataforma como um serviço (PaaS) implantados em seu laboratório. Para testar ou usar esse recurso, [implante um ambiente em seu laboratório.](devtest-lab-create-environment-from-arm.md) 

## <a name="view-security-alerts-for-an-environment"></a>Exibir alertas de segurança para um ambiente

1. Na página inicial do seu laboratório, selecione **alertas de segurança** no menu esquerdo. Você deve ver o número de alertas de segurança (alto, médio e baixo). Saiba mais sobre [como os alertas são classificados.](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)

    ![Alertas de segurança - visão geral](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Clique com o botão direito do mouse em três pontos (...) na última coluna e selecione **Exibir alertas de segurança**. 

    ![Exibir alertas de segurança](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Você vê mais detalhes sobre os alertas e recomendações do conselheiro. Saiba mais sobre [como gerenciar e responder a alertas de segurança no Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Exibir alertas de segurança](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre ambientes, consulte os seguintes artigos:

- [Crie ambientes multivm e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e usar ambientes públicos](devtest-lab-configure-use-public-environments.md)
