---
title: Gerenciar alertas de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este documento ajuda você a usar recursos da Central de segurança do Azure para gerenciar e responder a alertas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 6ea951b542d893b8fef3cdf19a964ce2ef8a034d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415690"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Gerencie e responda a alertas de segurança no Azure Security Center

Este tópico mostra como visualizar e processar os alertas recebidos para proteger seus recursos. 

* Para saber mais sobre os diferentes tipos de alertas, consulte os tipos de [alerta de segurança](alerts-reference.md).
* Para obter uma visão geral de como o Security Center gera alertas, consulte [como o Azure Security Center detecta e responde a ameaças](security-center-alerts-overview.md).

> [!NOTE]
> Para habilitar as detecções avançadas, atualize para o Padrão da Central de Segurança do Azure. Há uma avaliação gratuita disponível. Para atualizar, selecione Nível de preços na [política de segurança](tutorial-security-policy.md). Consulte [Preços da Central de Segurança do Azure](security-center-pricing.md) para saber mais.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
A Central de segurança coleta, analisa e integra automaticamente os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros conectados, como firewall e soluções de proteção de ponto de extremidade, a fim de detectar ameaças reais e reduzir os falsos positivos. Uma lista priorizada de alertas de segurança é exibida na Central de Segurança, junto com as informações necessárias para investigar rapidamente o problema, e recomendações sobre como corrigir um ataque.

> [!NOTE]
> Para obter mais informações sobre como os recursos de detecção do Security Center funcionam, consulte [como o Azure Security Center detecta e responde a ameaças](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Gerencie seus alertas de segurança

1. No painel do Security Center, consulte o bloco **de proteção de ameaças** para visualizar e visão geral dos alertas.

    ![Bloco Alertas de segurança na Central de Segurança](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Para ver mais detalhes sobre os alertas, clique no azulejo.

   ![Os Alertas de segurança na Central de Segurança](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Para filtrar os alertas mostrados, clique em **Filtrar**e na lâmina **Filter** que se abre, selecione as opções de filtro que deseja aplicar. A lista é atualizada de acordo com o filtro selecionado. Filtrar pode ser muito útil. Por exemplo, convém lidar com os alertas de segurança que ocorreram nas últimas 24 horas, pois você está investigando uma possível falha no sistema.

    ![Filtragem de alertas na Central de Segurança](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Responder a alertas de segurança

1. Na lista **de alertas de segurança,** clique em um alerta de segurança. Os recursos envolvidos e as medidas que você precisa tomar para remediar um ataque são mostrados.

    ![Responder a alertas de segurança](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Depois de revisar as informações, clique em um recurso que foi atacado.

    ![Sugestões para o que fazer em alertas de segurança](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    A seção **Informações Gerais** pode oferecer uma visão do que desencadeou o alerta de segurança. Ele exibe informações como o recurso de destino, endereço IP de origem (quando aplicável), se o alerta ainda estiver ativo e recomendações sobre como remediar.  

    > [!NOTE]
    >Em alguns casos, o endereço IP de origem não está disponível, alguns registros de eventos de segurança do Windows não incluem o endereço IP.

1. As etapas de correção sugeridas pela Central de Segurança variam de acordo com o alerta de segurança. Siga-os para cada alerta. 

    Em alguns casos, para mitigar um alerta de segurança, você pode ter que usar outros controles ou serviços do Azure para implementar a remediação recomendada. 

## <a name="see-also"></a>Confira também

Neste documento, você aprendeu a configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Alertas de segurança no Azure Security Center](security-center-alerts-overview.md).
* [Lidar com incidentes de segurança](security-center-incident.md)