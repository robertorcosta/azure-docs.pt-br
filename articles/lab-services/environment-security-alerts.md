---
title: Alertas de segurança para ambientes no Azure DevTest Labs
description: Este artigo mostra como exibir alertas de segurança para um ambiente no DevTest Labs e tomar uma ação apropriada.
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
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992229"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Alertas de segurança para ambientes no Azure DevTest Labs
Este artigo mostra como exibir alertas de segurança para ambientes no Azure DevTest Labs. 

## <a name="prerequisites"></a>Pré-requisitos
No momento, você pode exibir alertas de segurança somente para ambientes implantados em seu laboratório. Para testar ou usar esse recurso, implante um ambiente em seu laboratório. 

## <a name="view-security-alerts-for-an-environment"></a>Exibir alertas de segurança para um ambiente

1. No home page para seu laboratório, selecione **alertas de segurança** no menu à esquerda. Você deve ver o número de alertas de segurança (alta, média e baixa).

    ![Alertas de segurança-visão geral](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Clique com o botão direito do mouse em três pontos (...) na última coluna e selecione **exibir alertas de segurança**. 

    ![Exibir alertas de segurança](./media/environment-security-alerts/view-security-alerts-menu.png)
3. Você verá mais detalhes sobre as recomendações de alertas e do assistente. 

    ![Exibir alertas de segurança](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Próximos passos
Para saber mais sobre ambientes, consulte os seguintes artigos:

- [Criar ambientes de várias VMs e recursos de PaaS com modelos de Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e usar ambientes públicos](devtest-lab-configure-use-public-environments.md)
