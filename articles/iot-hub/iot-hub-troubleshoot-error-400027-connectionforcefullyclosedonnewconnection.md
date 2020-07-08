---
title: Solução de problemas do Hub IoT do Azure erro 400027 ConnectionForcefullyClosedOnNewConnection
description: Entenda como corrigir o erro 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960523"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Este artigo descreve as causas e soluções para erros de **400027 ConnectionForcefullyClosedOnNewConnection** .

## <a name="symptoms"></a>Sintomas

A operação de conexão do dispositivo para a nuvem (como propriedades relatadas de leitura ou patch) ou invocação de método direto falha com o código de erro **400027**.

## <a name="cause"></a>Causa

Outro cliente criou uma nova conexão com o Hub IoT usando as mesmas credenciais, portanto, o Hub IoT fechou a conexão anterior. O Hub IoT não permite que mais de um cliente se conecte usando o mesmo conjunto de credenciais.

## <a name="solution"></a>Solução

Certifique-se de que cada cliente se conecte ao Hub IoT usando sua própria identidade.