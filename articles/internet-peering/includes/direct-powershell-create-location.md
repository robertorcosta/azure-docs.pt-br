---
title: incluir arquivo
titleSuffix: Azure
description: incluir arquivo
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680812"
---
O cmdlet **Get-AzPeeringLocation** do PowerShell retorna uma lista de `Kind`locais de observação com o parâmetro obrigatório , que você usará em etapas posteriores.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Os locais de peering direto contêm os seguintes campos:
* Localização de peering 
* País/Região
* PeeringDBFacilityId
* PeeringDBFacilityLink
* Largura de bandaOferece

Valide se você está presente na instalação de peering desejada, referindo-se ao [PeeringDB](https://wwww.peeringdb.com).

Este exemplo mostra como usar Seattle como o local de peering para criar um peering direto.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```