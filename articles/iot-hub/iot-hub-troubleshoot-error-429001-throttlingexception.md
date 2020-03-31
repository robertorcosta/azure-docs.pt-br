---
title: Solução de problemas Erro do Hub IoT do Azure 429001 ThrottlingException
description: Entenda como corrigir o erro 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960692"
---
# <a name="429001-throttlingexception"></a>429001 ExceçãoLimitação

Este artigo descreve as causas e soluções para os erros **do 429001 ThrottlingException.**

## <a name="symptoms"></a>Sintomas

Suas solicitações ao IoT Hub falham com o erro **429001 ThrottlingException**.

## <a name="cause"></a>Causa

Os limites [de estrangulamento do](./iot-hub-devguide-quotas-throttling.md) IoT Hub foram excedidos para a operação solicitada.

## <a name="solution"></a>Solução

Verifique se você está atingindo o limite de estrangulamento comparando a métrica de *tentativas de envio de mensagens de Telemetria* com os limites especificados acima. Você também pode verificar o *número de métricas de erros de estrangulamento.* Para obter mais informações sobre essas e outras métricas disponíveis para o IoT Hub, consulte [métricas do IoT Hub e como usá-las](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them).

O IoT Hub retorna 429 ThrottlingException somente depois que o limite tiver sido violado por muito tempo. Isso é feito para que suas mensagens não sejam descartadas se seu hub de IoT receber tráfego estourado. Enquanto isso, o Hub IoT processa as mensagens na taxa de limitação da operação, o que pode ser lento se houver muito tráfego na lista de pendências. Para saber mais, consulte [modelagem de tráfego do Hub IoT](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Próximas etapas

Considere [aumentar seu IoT Hub](./iot-hub-scaling.md) se você estiver correndo em limites de cota ou estrangulamento.