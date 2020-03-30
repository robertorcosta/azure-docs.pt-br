---
title: Gerenciar incidentes de segurança no Azure Security Center | Microsoft Docs
description: Este documento ajuda você a usar o Azure Security Center para gerenciar incidentes de segurança.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415661"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gerenciar incidentes de segurança no Azure Security Center

Triagem e investigação de alertas de segurança podem ser demorados até mesmo para os analistas de segurança mais qualificados, e para muitos é difícil saber por onde começar. Usando [análise](security-center-detection-capabilities.md) para conectar as informações entre diferentes [alertas de segurança](security-center-managing-and-responding-alerts.md), a Central de Segurança pode fornecer uma exibição única de uma campanha de ataque e todos os alertas relacionados, com isso, você pode entender rapidamente quais ações o invasor executou e quais recursos foram afetados.

Este tópico explica sobre incidentes no Security Center e como usar seus alertas.

## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?

Na Central de Segurança, um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com os padrões da [cadeia de desativações](alerts-reference.md#intentions) . Os incidentes aparecem na lista [de alertas de segurança.](security-center-managing-and-responding-alerts.md) Clique em um incidente para visualizar os alertas relacionados, o que permite obter mais informações sobre cada ocorrência.

## <a name="managing-security-incidents"></a>Gerenciamento de incidentes de segurança

1. No painel do Security Center, clique no azulejo **de alertas de segurança.** Os incidentes e alertas estão listados. Observe que a descrição de incidentes de segurança tem um ícone diferente em comparação com outros alertas.

    ![Ver incidentes de segurança](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Para ver detalhes, clique em um incidente. O **incidente de segurança detectado** da lâmina exibe mais detalhes. A seção **Informações Gerais** pode oferecer uma visão do que desencadeou o alerta de segurança. Ele exibe informações como o recurso de destino, endereço IP de origem (quando aplicável), se o alerta ainda estiver ativo e recomendações sobre como remediar.  

    ![Responda a incidentes de segurança no Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Para obter mais informações sobre cada alerta, clique em um alerta. A correção sugerida pela Central de Segurança varia de acordo com o alerta de segurança.

   > [!NOTE]
   > O mesmo alerta pode existir como parte de um incidente, bem como ser visível como um alerta autônomo.

    ![Detalhes do Alerta](./media/security-center-incident/security-center-incident-alert.png)

1. Siga as etapas de remediação dadas para cada alerta.


## <a name="see-also"></a>Confira também
Neste documento, você aprendeu a usar os recursos de incidente de segurança na Central de Segurança do Azure. Para obter informações relacionadas, consulte os artigos a seguir:

* [Proteção contra ameaças no Centro de Segurança Do Azure](threat-protection.md)
* [Alertas na Central de Segurança do Azure](security-center-alerts-overview.md)
* [Gerenciar alertas de segurança](security-center-managing-and-responding-alerts.md)