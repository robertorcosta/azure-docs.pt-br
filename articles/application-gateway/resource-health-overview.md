---
title: Visão geral do recurso do Gateway do aplicativo do Azure
description: Este artigo é uma visão geral do recurso de saúde de recursos do Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67659496"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Visão geral do recurso do Gateway do aplicativo do Azure

O [Azure Resource Health](../service-health/resource-health-overview.md) ajuda a diagnosticar e obter suporte quando um problema de serviço do Azure afeta seus recursos. Ele informa sobre a integridade atual e anterior dos seus recursos. E fornece suporte técnico para ajudá-lo a mitigar os problemas.

Para o Application Gateway, a Resource Health conta com sinais emitidos pelo gateway para avaliar se é saudável ou não. Se o gateway não for saudável, a Resource Health analisa informações adicionais para determinar a origem do problema. Ele também identifica ações que a Microsoft está tomando ou o que você pode fazer para corrigir o problema.

Para obter detalhes adicionais sobre como a integridade é avaliada, revise a lista completa de tipos de recursos e verificações de integridade no [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


O estado de saúde do Application Gateway é exibido como um dos seguintes status:

## <a name="available"></a>Disponível

Um **status disponível** significa que o serviço não detectou nenhum evento que afete a saúde do recurso. Você verá a notificação **recentemente resolvida** nos casos em que o gateway se recuperou de tempo de inatividade não planejado durante as últimas 24 horas.

![Estado de saúde disponível](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Indisponível

Um status **indisponível** significa que o serviço detectou uma plataforma contínua ou um evento não-plataforma que afeta a saúde do gateway.

### <a name="platform-events"></a>Eventos de plataforma

Os eventos da plataforma são disparados por vários componentes da infraestrutura do Azure. Eles incluem ações agendadas (por exemplo, manutenção planejada) e incidentes inesperados (por exemplo, uma reinicialização do host não planejada).

O Resource Health fornece detalhes adicionais sobre o evento e o processo de recuperação. Ele também permite que você entre em contato com o suporte mesmo se você não tiver um contrato de suporte ativo da Microsoft.

![Status indisponível](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Unknown (desconhecido)

O **estado de** saúde desconhecido indica que a Resource Health não recebeu informações sobre o gateway por mais de 10 minutos. Este status não é uma indicação definitiva do estado do portal. Mas é um ponto de dados importante no processo de solução de problemas.

Se o gateway estiver funcionando como esperado, o status será alterado para **Disponível** após alguns minutos.

Se você está tendo problemas, o estado de saúde **desconhecido** pode sugerir que um evento na plataforma está afetando o gateway.

![Status desconhecido](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Degradado

O estado de saúde **degradado** indica que seu gateway detectou uma perda de desempenho, embora ainda esteja disponível para uso.

![Status desmemilhado](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a solução de problemas do Application Gateway Web Application Firewall (WAF), consulte [O FIREWALL de aplicativos da Web (Troubles) para o Azure Application Gateway](web-application-firewall-troubleshoot.md).