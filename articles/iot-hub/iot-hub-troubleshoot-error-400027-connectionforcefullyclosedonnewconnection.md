---
title: Solução de problemas Erro do Hub IoT do Azure 400027 ConnectionForcefullyClosedOnNewConnection
description: Entenda como corrigir o erro 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960523"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Este artigo descreve as causas e soluções para os erros **de 400027 ConnectionForcefullyClosedOnNewConnection.**

## <a name="symptoms"></a>Sintomas

Sua operação dupla dispositivo-nuvem (como propriedades de leitura ou patch relatados) ou invocação direta do método falha com o código de erro **400027**.

## <a name="cause"></a>Causa

Outro cliente criou uma nova conexão com o IoT Hub usando as mesmas credenciais, então o IoT Hub fechou a conexão anterior. O IoT Hub não permite que mais de um cliente se conecte usando o mesmo conjunto de credenciais.

## <a name="solution"></a>Solução

Certifique-se de que cada cliente se conecte ao IoT Hub usando sua própria identidade.