---
title: Solução de problemas Erro do Hub IoT do Azure 404001 Dispositivo Não Encontrado
description: Entenda como corrigir o erro 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960822"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Este artigo descreve as causas e soluções para erros **do DeviceNotFound 404001.**

## <a name="symptoms"></a>Sintomas

Durante uma comunicação cloud-to-device (C2D), como mensagem C2D, atualização dupla ou método direto, a operação falha com o erro **404001 DeviceNotFound**.

## <a name="cause"></a>Causa

A operação falhou porque o dispositivo não pode ser encontrado pelo IoT Hub. O dispositivo não está registrado ou desativado.

## <a name="solution"></a>Solução

Registre o ID do dispositivo que você usou e tente novamente.