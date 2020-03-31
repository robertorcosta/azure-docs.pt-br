---
title: Solução de problemas Erro do Hub IoT do Azure 403002 IoTHubQuotaExcedido
description: Entenda como corrigir erro 403002 IoTHubQuotaExcedido
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961108"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

Este artigo descreve as causas e soluções para erros **403002 IoTHubQuotaExcedidos.**

## <a name="symptoms"></a>Sintomas

Todas as solicitações para o IoT Hub falham com o erro **403002 IoTHubQuotaExceeded**. No portal Azure, a lista de dispositivos do hub IoT não é carregada.

## <a name="cause"></a>Causa

A cota diária de mensagens para o hub IoT é excedida. 

## <a name="solution"></a>Solução

[Atualize ou aumente o número de unidades no hub ioT](iot-hub-upgrade.md) ou aguarde o próximo dia UTC para que a cota diária seja atualizada.

## <a name="next-steps"></a>Próximas etapas

* Para entender como as operações são contadas para o contingente, como consultas duplas e métodos diretos, consulte [Entenda os preços do IoT Hub](iot-hub-devguide-pricing.md#charges-per-operation)
* Para configurar o monitoramento para o uso diário de cotas, configure um alerta com a métrica *Número total de mensagens utilizadas*. Para obter instruções passo a passo, consulte [Configurar métricas e alertas com o IoT Hub](tutorial-use-metrics-and-diags.md#set-up-metrics)