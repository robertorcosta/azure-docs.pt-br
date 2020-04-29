---
title: Pré-limites e cotas Plug and Play visualização de IoT | Microsoft Docs
description: Entenda os limites, as cotas e a limitação que se aplicam quando você usa a visualização de Plug and Play de IoT.
author: miagdp
ms.author: miag
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cce99b7d9de09134fd01afb36c41bce3966e8536
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80518180"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limites, cotas e limitações da visualização do IoT Plug and Play

Este artigo explica os limites, as cotas e a limitação específicos do Plug and Play IoT que se aplicam à visualização pública. Há [cotas e limitação do Hub IOT](../iot-hub/iot-hub-devguide-quotas-throttling.md) que também se aplicam.

## <a name="iot-hub"></a>Hub IoT

Para a visualização pública, os limites e as cotas a seguir se aplicam a um hub IoT:

| Limites, restrições e limitação | Valor | Anotações |
|-----|-----|-----|
| Número de modelos de capacidade de dispositivo (DCMs) ou interfaces que podem ser registrados por Hub | 1500 ||
| Número máximo de interfaces que podem ser registradas por dispositivo | 40 ||
| Número máximo de DCMs que podem ser registrados por dispositivo | 1 ||
| Tamanho máximo do arquivo de interface/DCM | 512 KB ||
| Tamanho máximo de um nome de interface | caracteres de 256 ||
| Tamanho máximo de um nome de propriedade  | 64 bytes, 7 níveis em profundidade (e o primeiro nível é reservado para `$iotin`) | Caracteres permitidos: a-z, A-Z, 0-9 (não como o primeiro caractere) e sublinhado. |
| Tamanho máximo de um valor de propriedade | 512 bytes ||
| Tamanho máximo de um nome de comando | 100 bytes ||
| Tamanho do dispositivo gêmeo | Mesmo que [os limites do Hub IOT](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Resoluções de API de resolução no SKU (independentemente das unidades) | 100 solicitações/segundo ||

## <a name="model-repository"></a>Repositório de modelos

Para a visualização pública, os limites e as cotas a seguir se aplicam a um repositório de modelo:

| Limites, restrições e limitação | Valor |
|-----|-----|
| Número de repositórios de modelo da empresa por locatário Azure Active Directory | 1 |
| Número de chaves de autorização por repositório de modelo | 10  |
| Número de modelos (DCMs ou interfaces) por repositório de modelo da empresa| 1500  |
| Número de modelos (DCMs ou interfaces) no repositório de modelo público por locatário Azure Active Directory| 1500  |
| Número de DCMs ou interfaces sendo excluídas em um repositório de modelos da empresa | 10 consultas por segundo (QPS)|
| Número de repositórios de modelo sendo criados/atualizados por um locatário| 1 QPS |
| Número de chaves de autorização sendo criadas/atualizadas/excluídas em um repositório de modelos | 1 QPS|
| Número de DCMs que estão sendo criadas em um repositório de modelos da empresa | 10 QPS |
| Número de interfaces que estão sendo criadas em um repositório de modelos da empresa | 10 QPS|
| Número de DCMs que estão sendo criadas no repositório de modelos públicos | 10 QPS|
| Número de interfaces que estão sendo criadas no repositório de modelos públicos | 10 QPS|

## <a name="parser-library"></a>Biblioteca do analisador

A biblioteca do analisador segue os limites que se aplicam à [linguagem de definição de entrelaçamento digital](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## <a name="next-steps"></a>Próximas etapas

Uma próxima etapa sugerida é aprender a [se conectar e interagir com um dispositivo de plug and Play de IOT](./howto-develop-solution.md).
