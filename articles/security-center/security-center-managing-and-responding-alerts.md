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
ms.date: 08/27/2019
ms.author: memildin
ms.openlocfilehash: 203e3718cedca693dc029a4ba20a86ca50ab3e32
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603852"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Gerenciar e responder a alertas de segurança na central de segurança do Azure

Este tópico mostra como exibir e processar os alertas que você recebeu para proteger seus recursos. 

* Para saber mais sobre os diferentes tipos de alertas, consulte [tipos de alertas de segurança](security-center-alerts-overview.md#security-alert-types).
* Para obter uma visão geral de como a central de segurança gera alertas, consulte [como a central de segurança do Azure detecta e responde às ameaças](security-center-alerts-overview.md#detect-threats).

> [!NOTE]
> Para habilitar as detecções avançadas, atualize para o Padrão da Central de Segurança do Azure. Há uma avaliação gratuita disponível. Para atualizar, selecione a Camada de Preços na [Política de Segurança](tutorial-security-policy.md). Consulte [Preços da Central de Segurança do Azure](security-center-pricing.md) para saber mais.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
A Central de segurança coleta, analisa e integra automaticamente os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros conectados, como firewall e soluções de proteção de ponto de extremidade, a fim de detectar ameaças reais e reduzir os falsos positivos. Uma lista priorizada de alertas de segurança é exibida na Central de Segurança, junto com as informações necessárias para investigar rapidamente o problema, e recomendações sobre como corrigir um ataque.

> [!NOTE]
> Para obter mais informações sobre como funcionam os recursos de detecção da central de segurança, consulte [como a central de segurança do Azure detecta e responde às ameaças](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Gerenciar seus alertas de segurança

1. No painel da central de segurança, consulte o bloco **proteção contra ameaças** para exibir e visão geral dos alertas.

    ![Bloco Alertas de segurança na Central de Segurança](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Para ver mais detalhes sobre os alertas, clique no bloco.

   ![Os Alertas de segurança na Central de Segurança](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Para filtrar os alertas mostrados, clique em **Filtrar**e, na folha **filtro** que é aberta, selecione as opções de filtro que você deseja aplicar. A lista é atualizada de acordo com o filtro selecionado. A filtragem pode ser muito útil. Por exemplo, convém lidar com os alertas de segurança que ocorreram nas últimas 24 horas, pois você está investigando uma possível falha no sistema.

    ![Filtragem de alertas na Central de Segurança](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Responder a alertas de segurança

1. Na lista **alertas de segurança** , clique em um alerta de segurança. Os recursos envolvidos e as etapas que precisam ser tomadas para corrigir um ataque são mostrados.

    ![Responder a alertas de segurança](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Depois de revisar as informações, clique em um recurso que foi atacado.

    ![Sugestões sobre o que fazer sobre alertas de segurança](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    A seção **informações gerais** pode oferecer uma percepção do que disparou o alerta de segurança. Ele exibe informações como o recurso de destino, endereço IP de origem (quando aplicável), se o alerta ainda estiver ativo e recomendações sobre como corrigir.  

    > [!NOTE]
    >Em alguns casos, o endereço IP de origem não está disponível, alguns logs de eventos de segurança do Windows não incluem o endereço IP.

1. As etapas de correção sugeridas pela central de segurança variam de acordo com o alerta de segurança. Siga-os para cada alerta. Em alguns casos, para atenuar um alerta de detecção de ameaças, talvez seja necessário usar outros controles ou serviços do Azure para implementar a correção recomendada. 

    Os tópicos a seguir orientam você pelos diferentes alertas, de acordo com os tipos de recursos:
    
    * [Alertas de VMs e servidores IaaS](security-center-alerts-iaas.md)
    * [Alertas de computação nativos](security-center-alerts-compute.md)
    * [Alertas de serviços de dados](security-center-alerts-data-services.md)
    
    Os tópicos a seguir explicam como a central de segurança usa a telemetria diferente que ela coleta da integração com a infraestrutura do Azure, para aplicar camadas de proteção adicionais para recursos implantados no Azure:
    
    * [Alertas da camada de serviço](security-center-alerts-service-layer.md)
    * [Detecção de ameaças para o Azure WAF e a proteção contra DDoS do Azure](security-center-alerts-integration.md)
    
## <a name="see-also"></a>Confira também

Neste documento, você aprendeu a configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Alertas de segurança na central de segurança do Azure](security-center-alerts-overview.md).
* [Lidando com incidentes de segurança](security-center-incident.md)
* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md)