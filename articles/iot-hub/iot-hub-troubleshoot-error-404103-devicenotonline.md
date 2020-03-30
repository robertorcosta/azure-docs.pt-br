---
title: Solução de problemas Erro do Hub IoT do Azure 404103 DispositivoNotOnline
description: Entenda como corrigir o erro 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960809"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Este artigo descreve as causas e soluções para erros **do DeviceNotOnline 404103.**

## <a name="symptoms"></a>Sintomas

Um método direto para um dispositivo falha com o erro **404103 DeviceNotOnline** mesmo que o dispositivo esteja on-line. 

## <a name="cause"></a>Causa

Se você sabe que o dispositivo está online e ainda recebe o erro, é provável porque o retorno de chamada do método direto não está registrado no dispositivo.

## <a name="solution"></a>Solução

Para configurar o dispositivo corretamente para retornos de chamadas diretas do método, consulte [Manusear um método direto em um dispositivo](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).