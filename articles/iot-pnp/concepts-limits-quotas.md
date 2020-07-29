---
title: Pré-limites e cotas Plug and Play visualização de IoT | Microsoft Docs
description: Entenda os limites, as cotas e a limitação que se aplicam quando você usa a visualização de Plug and Play de IoT.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c4377120f61792b580225a22b9f5ff51b5e1b64
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337391"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limites, cotas e limitações da visualização do IoT Plug and Play

Este artigo explica os limites, as cotas e a limitação específicos do Plug and Play IoT que se aplicam à visualização pública. Há [cotas e limitação do Hub IOT](../iot-hub/iot-hub-devguide-quotas-throttling.md) que também se aplicam.

## <a name="iot-hub"></a>Hub IoT

Para a visualização pública, os limites e as cotas a seguir se aplicam a um hub IoT:

| Limites, restrições e limitação | Valor | Observações |
|-----|-----|-----|
| Número de interfaces que podem ser registradas por Hub | 1500 ||
| Tamanho máximo de um nome de componente | caracteres de 1-64 | Caracteres permitidos: a-z, A-Z, 0-9 (não como o primeiro caractere) e sublinhado (não como o primeiro ou último caractere). |
| Tamanho máximo de um nome de propriedade | caracteres de 1-64 | Caracteres permitidos: a-z, A-Z, 0-9 (não como o primeiro caractere) e sublinhado (não como o primeiro ou último caractere). |
| Tamanho máximo de um valor de propriedade | Mesmo que a [Propriedade](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property) de linguagem de definição de gêmeos digital | 5 níveis em profundidade e não podem ser uma matriz ou qualquer esquema complexo que contenha uma matriz |
| Tamanho máximo de um nome de comando | caracteres de 1-64 | Caracteres permitidos: a-z, A-Z, 0-9 (não como o primeiro caractere) e sublinhado (não como o primeiro ou último caractere).|
| Tamanho do dispositivo gêmeo | Mesmo que [os limites do Hub IOT](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||

## <a name="parser-library"></a>Biblioteca do analisador

A biblioteca do analisador segue os limites que se aplicam à [linguagem de definição de gêmeos digital](https://github.com/Azure/opendigitaltwins-dtdl).

## <a name="next-steps"></a>Próximas etapas

A próxima etapa sugerida é examinar a [arquitetura de plug and Play de IOT](concepts-architecture.md).
