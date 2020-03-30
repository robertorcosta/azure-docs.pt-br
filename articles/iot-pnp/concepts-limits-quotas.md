---
title: Limites e cotas IoT Plug and Play Preview | Microsoft Docs
description: Entenda os limites, cotas e estrangulamento que se aplicam quando você usa ioT Plug and Play Preview.
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75531370"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limites, cotas e limites de visualização de plug e reprodução de IoT

Este artigo explica os limites, cotas e estrangulamento específicos do IoT Plug e Play que se aplicam na pré-visualização pública. Existem cotas de [IoT Hub existentes e estrangulamento](../iot-hub/iot-hub-devguide-quotas-throttling.md) que também se aplicam.

## <a name="iot-hub"></a>Hub IoT

Para a visualização pública, os seguintes limites e cotas se aplicam a um hub de IoT:

| Limites, restrições e aceleradores | Valor | Observações |
|-----|-----|-----|
| Número de modelos de capacidade de dispositivos (DCMs) ou interfaces que podem ser registradas por hub | 1500 ||
| Número máximo de interfaces que podem ser registradas por dispositivo | 40 ||
| Número máximo de DCMs que podem ser registrados por dispositivo | 1 ||
| Tamanho máximo do arquivo interface/DCM | 512 chars ||
| Tamanho máximo de um nome de interface | 256 chars ||
| Tamanho máximo de um nome de propriedade  | 64 bytes, 7 níveis de profundidade (e `$iotin`o primeiro nível é reservado para ) | Personagens permitidos: a-z, A-Z, 0-9 (não como o primeiro caractere), e sublinhar. |
| Tamanho máximo de um valor de propriedade | 512 bytes ||
| Tamanho máximo de um nome de comando | 100 bytes ||
| Tamanho do dispositivo gêmeo | O mesmo que [os Limites do Hub de IoT](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Chamadas de API de resolução em SKU (independentemente das unidades) | 100 solicitações/segundo ||

## <a name="model-repository"></a>Repositório modelo

Para a visualização pública, os seguintes limites e cotas se aplicam a um repositório de modelos:

| Limites, restrições e aceleradores| Valor |
|-----|-----|
| Número de repositórios de modelos da empresa por inquilino do Azure Active Directory | 1 |
| Número de chaves de autorização por repositório de modelo | 10  |
| Número de modelos (DCMs ou interfaces) por repositório de modelos da empresa| 1500  |
| Número de modelos (DCMs ou interfaces) no repositório de modelos públicos por inquilino do Azure Active Directory| 1500  |
| Número de DCMs ou interfaces sendo excluídos em um repositório de modelos da empresa | 10 consultas por segundo (QPS)|
| Número de repositórios de modelos sendo criados/atualizados por um inquilino| 1 QPS |
| Número de chaves de autorização sendo criadas/atualizadas/excluídas em um repositório de modelos | 1 QPS|
| Número de DCMs sendo criados em um repositório de modelos da empresa | 10 QPS |
| Número de interfaces sendo criadas em um repositório de modelos da empresa | 10 QPS|
| Número de DCMs sendo criados no repositório de modelos públicos | 10 QPS|
| Número de interfaces sendo criadas no repositório de modelos públicos | 10 QPS|

## <a name="parser-library"></a>Biblioteca de parser

A biblioteca de analisador segue os limites aplicáveis à [Linguagem de Definição Dupla Digital](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## <a name="next-steps"></a>Próximas etapas

Um próximo passo sugerido é aprender a [conectar e interagir com um dispositivo IoT Plug and Play](./howto-develop-solution.md).
