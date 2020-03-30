---
title: Solução de problemas Azure IoT Hub erro 409001 DispositivoJá Existe
description: Entenda como corrigir o erro 409001 DeviceAlreadyExist
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960783"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Este artigo descreve as causas e soluções para erros **do Dispositivo 409001.**

## <a name="symptoms"></a>Sintomas

Ao tentar registrar um dispositivo no IoT Hub, a solicitação falha com o erro **409001 DeviceAlreadyExist**.

## <a name="cause"></a>Causa

Já existe um dispositivo com o mesmo dispositivo ID no hub IoT. 

## <a name="solution"></a>Solução

Use um ID de dispositivo diferente e tente novamente.