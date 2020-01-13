---
title: Modelos de consumo de veículo para roteamento | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre os modelos de consumo de veículo para roteamento em mapas de Microsoft Azure.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5a8a0778ce279846b0d7a66b1729b6898e80a4b5
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911706"
---
# <a name="consumption-model"></a>Modelo de consumo

O roteamento online fornece um conjunto de parâmetros para uma descrição detalhada do modelo de consumo específico do veículo.
Dependendo do valor de **vehicleEngineType**, há suporte para dois modelos de consumo principais: _Combustion_ e _Electric_. Especificar parâmetros que pertencem a modelos diferentes na mesma solicitação é um erro.
O modelo de consumo não pode ser usado com os valores de **travelMode**_bicycle_ e _pedestrian_.

## <a name="parameter-constraints-for-consumption-model"></a>Restrições de parâmetro de modelo de consumo

Em ambos os modelos de consumo, especificar explicitamente alguns parâmetros requer a especificação de alguns outros também. Essas dependências são:

* Todos os parâmetros exigem que **constantSpeedConsumption** seja especificado pelo usuário. É um erro especificar qualquer outro parâmetro de modelo de consumo, com exceção de **vehicleWeight**, se **constantSpeedConsumption** não for especificado.
* **accelerationEfficiency** e **decelerationEfficiency** sempre deve ser especificados como um par (ou seja, ambos ou nenhum).
* Se **accelerationEfficiency** e **decelerationEfficiency** forem especificados, o produto de seus valores não deve ser maior que 1 (para evitar um movimento perpétuo).
* **uphillEfficiency** e **downhillEfficiency** sempre deve ser especificados como um par (ou seja, ambos ou nenhum).
* Se **uphillEfficiency** e **downhillEfficiency** forem especificados, o produto de seus valores não deve ser maior que 1 (para evitar um movimento perpétuo).
* Se os parâmetros de \*__Efficiency__ são especificados pelo usuário, então, **vehicleWeight** também deve ser especificado. Quando **vehicleEngineType** for _combustão_, **fuelEnergyDensityInMJoulesPerLiter** também deve ser especificado.
* **maxChargeInkWh** e **currentChargeInkWh** sempre devem ser especificados como um par (ou seja, ambos ou nenhum).

> [!NOTE]
> Se apenas **constantSpeedConsumption** for especificado, nenhum outro aspecto de consumo como inclinações acentuadas e aceleração de veículo são levados em consideração para cálculos de consumo.

## <a name="combustion-consumption-model"></a>Modelo de consumo de combustão

O modelo de consumo de combustão é usado quando **vehicleEngineType** é definido como _combustion_.
A lista de parâmetros que pertencem a esse modelo está abaixo. Consulte a seção Parâmetros para ver uma descrição detalhada.

* constantSpeedConsumptionInLitersPerHundredkm
* VehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Modelo de consumo elétrico

O modelo de consumo elétrico é usado quando **vehicleEngineType** é definido como _electric_.
A lista de parâmetros que pertencem a esse modelo está abaixo. Consulte a seção Parâmetros para ver uma descrição detalhada.

* constantSpeedConsumptionInkWhPerHundredkm
* VehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Valores razoáveis de parâmetros de consumo

Um determinado conjunto de parâmetros de consumo poderá ser rejeitado, mesmo que ele possa atender a todos os requisitos explícitos especificados acima. Isso acontece quando o valor de um parâmetro específico ou uma combinação de valores de vários parâmetros, pode resultar em magnitudes não razoáveis de valores de consumo. Se isso acontecer, ele provavelmente indicará um erro de entrada, pois é exercido um cuidado para acomodar todos os valores razoáveis de parâmetros de consumo. No caso de um determinado conjunto de parâmetros de consumo ser rejeitado, a mensagem de erro conterá uma explicação de texto sobre o(s) motivo(s).
As descrições detalhadas dos parâmetros têm exemplos de valores razoáveis para ambos os modelos.
