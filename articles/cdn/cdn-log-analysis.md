---
title: Analisar padrões de uso da CDN do Azure | Microsoft Docs
description: Este artigo descreve os diferentes tipos de relatórios de análise disponíveis para produtos da CDN do Azure.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: allensu
ms.openlocfilehash: c78dff9f5258023d90100c70bd244e8e0d016d6f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81253604"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analisar os padrões de uso da CDN do Azure

Após habilitar a CDN para seu aplicativo, será possível monitorar o uso da CDN, verificar a integridade da sua entrega e resolver eventuais problemas. A CDN do Azure fornece esses recursos das seguintes maneiras: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Análise de núcleo por meio de logs de diagnósticos do Azure

A análise de núcleo está disponível para pontos de extremidade CDN em todos os tipos de preço. Os logs de diagnóstico do Azure permitem que a análise principal seja exportada para o armazenamento do Azure, hubs de eventos ou logs de Azure Monitor. Os logs de Azure Monitor oferecem uma solução com grafos que podem ser configurados pelo usuário e personalizáveis. Para obter mais informações sobre logs de diagnóstico do Azure, consulte [Logs de diagnóstico do Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Relatórios de núcleo da Verizon

Como um usuário da CDN do Azure com um perfil **CDN Standard do Azure da Verizon** ou **CDN Premium do Azure da Verizon**, você pode exibir os relatórios principais da Verizon no portal suplementar da Verizon. Os relatórios de núcleo da Verizon podem ser acessados por meio da opção **gerenciar** da portal do Azure e oferece uma variedade de gráficos e exibições. Para obter mais informações, consulte [Relatórios de núcleo da Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Relatórios personalizados da Verizon

Como um usuário da CDN do Azure com um perfil **CDN Standard do Azure da Verizon** ou **CDN Premium do Azure da Verizon**, você pode exibir os relatórios personalizados da Verizon no portal suplementar da Verizon. Os relatórios personalizados da Verizon podem ser acessados por meio da opção **gerenciar** do portal do Azure. A página de relatórios personalizados da Verizon mostra o número de ocorrências ou dados transferidos para cada borda CName que pertence a um perfil de CDN do Azure. Os dados podem ser agrupados por status de cache ou de código de resposta HTTP em qualquer período. Para obter mais informações, consulte [Relatórios personalizados da Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Relatórios de CDN Premium do Azure da Verizon

Com o **Azure CDN Premium da Verizon**, você também pode acessar os seguintes relatórios:
   * [Relatórios avançados de HTTP](cdn-advanced-http-reports.md)
   * [Estatísticas em tempo real](cdn-real-time-stats.md)
   * [Desempenho do nó de borda](cdn-edge-performance.md)

