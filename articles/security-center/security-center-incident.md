---
title: Gerenciar incidentes de segurança na central de segurança do Azure | Microsoft Docs
description: Este documento ajuda você a usar a central de segurança do Azure para gerenciar incidentes de segurança.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 98fc339e473ffb2bf54e7119634e93046cca1ef3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415661"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gerenciar incidentes de segurança na central de segurança do Azure

A triagem e a investigação de alertas de segurança podem ser demoradas para até mesmo os analistas de segurança mais qualificados e, para muitos, é difícil saber até onde começar. Usando [análise](security-center-detection-capabilities.md) para conectar as informações entre diferentes [alertas de segurança](security-center-managing-and-responding-alerts.md), a Central de Segurança pode fornecer uma exibição única de uma campanha de ataque e todos os alertas relacionados, com isso, você pode entender rapidamente quais ações o invasor executou e quais recursos foram afetados.

Este tópico explica os incidentes na central de segurança e como usar a correção de seus alertas.

## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?

Na Central de Segurança, um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com os padrões da [cadeia de desativações](alerts-reference.md#intentions) . Os incidentes aparecem na lista [alertas de segurança](security-center-managing-and-responding-alerts.md) . Clique em um incidente para exibir os alertas relacionados, o que permite obter mais informações sobre cada ocorrência.

## <a name="managing-security-incidents"></a>Gerenciamento de incidentes de segurança

1. No painel central de segurança, clique no bloco **alertas de segurança** . Os incidentes e alertas são listados. Observe que a descrição de incidentes de segurança tem um ícone diferente em comparação com outros alertas.

    ![Exibir incidentes de segurança](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Para exibir detalhes, clique em um incidente. A folha **incidente de segurança detectado** exibe mais detalhes. A seção **informações gerais** pode oferecer uma percepção do que disparou o alerta de segurança. Ele exibe informações como o recurso de destino, endereço IP de origem (quando aplicável), se o alerta ainda estiver ativo e recomendações sobre como corrigir.  

    ![Responder a incidentes de segurança na central de segurança do Azure](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Para obter mais informações sobre cada alerta, clique em um alerta. A correção sugerida pela Central de Segurança varia de acordo com o alerta de segurança.

   > [!NOTE]
   > O mesmo alerta pode existir como parte de um incidente, bem como ser visível como um alerta autônomo.

    ![Detalhes do Alerta](./media/security-center-incident/security-center-incident-alert.png)

1. Siga as etapas de correção fornecidas para cada alerta.


## <a name="see-also"></a>Confira também
Neste documento, você aprendeu a usar os recursos de incidente de segurança na Central de Segurança do Azure. Para obter informações relacionadas, consulte os artigos a seguir:

* [Proteção contra ameaças na Central de Segurança do Azure](threat-protection.md)
* [Alertas de segurança na Central de Segurança do Azure](security-center-alerts-overview.md)
* [Gerenciar alertas de segurança](security-center-managing-and-responding-alerts.md)