---
title: Solução de problemas Erro do Hub Do Zure IoT 403006 DeviceMaximumActiveFileUploadLimiteexcedido
description: Entenda como corrigir erro 403006 DeviceMaximumActiveFileUploadLimitEd
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960835"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

Este artigo descreve as causas e soluções para **403006 DeviceMaximumActiveActiveUploadLimitLimiteerros.**

## <a name="symptoms"></a>Sintomas

Sua solicitação de upload de arquivo falha com o código de erro **403006** e uma mensagem "Número de solicitações de upload de arquivo ativo não pode exceder 10".

## <a name="cause"></a>Causa

Cada cliente do dispositivo está limitado a [10 uploads de arquivos simultâneos](./iot-hub-devguide-quotas-throttling.md#other-limits). 

Você pode facilmente exceder o limite se o dispositivo não notificar o IoT Hub quando os uploads do arquivo forem concluídos. Este problema é comumente causado por uma rede lateral de dispositivo não confiável.

## <a name="solution"></a>Solução

Certifique-se de que o dispositivo pode notificar prontamente [a conclusão do upload do arquivo IoT Hub](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload). Em seguida, tente [reduzir o TTL de token SAS para a configuração de upload de arquivo](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre uploads de arquivos, consulte [Upload de arquivos com IoT Hub](./iot-hub-devguide-file-upload.md) e [Configure uploads de arquivos IoT Hub usando o portal Azure](./iot-hub-configure-file-upload.md).