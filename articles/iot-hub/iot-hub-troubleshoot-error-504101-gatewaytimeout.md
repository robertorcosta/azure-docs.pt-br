---
title: Solução de problemas Erro do Hub IoT do Azure 504101 GatewayTimeout
description: Entenda como corrigir o erro 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960666"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Este artigo descreve as causas e soluções para erros **do GatewayTimeout 504101.**

## <a name="symptoms"></a>Sintomas

Ao tentar invocar um método direto do IoT Hub para um dispositivo, a solicitação falha com o erro **504101 GatewayTimeout**.

## <a name="cause"></a>Causa

### <a name="cause-1"></a>Causa 1

O IoT Hub encontrou um erro e não pôde confirmar se o método direto foi concluído antes do tempo de cronometragem.

### <a name="cause-2"></a>Causa 2

Ao usar uma versão anterior do Azure IoT C# SDK (<1.19.0), o link AMQP entre o dispositivo e o IoT Hub pode ser descartado silenciosamente por causa de um bug.

## <a name="solution"></a>Solução

### <a name="solution-1"></a>Solução 1

Emita uma nova tentativa.

### <a name="solution-2"></a>Solução 2

Atualize para a versão mais recente do Azure IOT C# SDK.