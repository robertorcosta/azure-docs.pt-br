---
title: Plug and Play de IoT de limites e cotas | Microsoft Docs
description: Entenda os limites, as cotas e a limitação que se aplicam quando você usa o Plug and Play de IoT.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d965d9cb8b87ce0b67f4fe0c07b660fdfd69cc07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577978"
---
# <a name="iot-plug-and-play-limits-quotas-and-throttles"></a>Limites de Plug and Play de IoT, cotas e limitadores

Este artigo explica os limites, as cotas e a limitação específicos do Plug and Play IoT. Há [cotas e limitação do Hub IOT](../iot-hub/iot-hub-devguide-quotas-throttling.md) que também se aplicam.

## <a name="iot-hub"></a>Hub IoT

Os limites e as cotas a seguir se aplicam a um hub IoT:

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
