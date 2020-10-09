---
title: Migração do Hub IoT do Azure para as configurações de diagnóstico | Microsoft Docs
description: Como atualizar o Hub IoT do Azure para usar as configurações de diagnóstico do Azure em vez do monitoramento de operações para monitorar o status das operações no seu Hub IoT em tempo real.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: ab07da38c01b052a4220274fb059683a22950a3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75750714"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Migrar seu Hub IoT do monitoramento de operações para as configurações de diagnóstico

Os clientes que usam o [monitoramento de operações](iot-hub-operations-monitoring.md) para acompanhar o status das operações no Hub IoT podem migrar o fluxo de trabalho para [as configurações de diagnóstico do Azure](../azure-monitor/platform/platform-logs-overview.md), um recurso do Azure Monitor. As configurações de diagnóstico fornecem informações de diagnóstico de nível de recurso para muitos serviços do Azure.

**A funcionalidade de monitoramento de operações do Hub IOT foi preterida**e foi removida do Portal. Este artigo fornece etapas para mover suas cargas de trabalho do monitoramento de operações para as configurações de diagnóstico. Para saber mais sobre a linha do tempo de substituição, confira [Monitorar suas soluções de IoT do Azure com o Azure Monitor e o Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Atualizar Hub IoT

Para atualizar o Hub IoT no portal do Azure, primeiro, ative as configurações de diagnóstico e desligue o monitoramento de operações.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Desligue o monitoramento de operações

> [!NOTE]
> A partir de 11 de março de 2019, o recurso de monitoramento de operações é removido da interface de portal do Azure do Hub IoT. As etapas abaixo não se aplicam mais. Para migrar, verifique se as categorias corretas estão ativadas no Azure Monitor configurações de diagnóstico acima.

Depois de testar as novas configurações de diagnóstico em seu fluxo de trabalho, você pode desativar o recurso de monitoramento de operações. 

1. No menu do Hub IoT, selecione **Monitoramento de operações**.

2. Em cada categoria de monitoramento, selecione **Nenhum**.

3. Salvar alterações no monitoramento de operações.

## <a name="update-applications-that-use-operations-monitoring"></a>Atualizar os aplicativos que usam o monitoramento de operações

Os esquemas para monitoramento de operações e configurações de diagnóstico variam um pouco. É importante que você atualize os aplicativos que usam atualmente o monitoramento de operações para mapear para o esquema usado pelas configurações de diagnóstico. 

Além disso, as configurações de diagnóstico oferecem cinco novas categorias para acompanhamento. Depois de atualizar aplicativos para o esquema existente, adicione as novas categorias também:

* Operações de dispositivo gêmeo para nuvem
* Operações de nuvem gêmea para dispositivo
* Consultas de gêmeos
* Operações de trabalhos
* Métodos diretos

Para as estruturas de esquema específicas, confira [Compreender o esquema para as configurações de diagnóstico](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Monitoramento de dispositivo se conectar e desconectar eventos com baixa latência

Para monitorar eventos de conexão e desconexão do dispositivo em produção, é recomendável assinar o [evento **dispositivo desconectado** ](iot-hub-event-grid.md#event-types) na grade de eventos para obter alertas e monitorar o estado da conexão do dispositivo. Use este [tutorial](iot-hub-how-to-order-connection-state-events.md) para saber como integrar os eventos Dispositivo Conectado e Dispositivo Desconectado do Hub IoT em sua solução de IoT.

## <a name="next-steps"></a>Próximas etapas

[Monitorar a integridade do Hub IoT do Azure e diagnosticar problemas rapidamente](iot-hub-monitor-resource-health.md)
