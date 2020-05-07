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
ms.openlocfilehash: fa8d8ccef7d6ad6e1b5d9f19de61e45ee8c439fa
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82587857"
---
O cmdlet **Get-AzPeeringLocation** do PowerShell retorna uma lista de locais de emparelhamento com o parâmetro `Kind`obrigatório, que você usará em etapas posteriores.

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Os locais de emparelhamento do Exchange contêm os seguintes campos:
* Exchangname
* PeeringLocation
* País/Região
* PeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Address
* MicrosoftIPv6Address

Valide se você está presente na instalação de emparelhamento desejada fazendo referência a [PeeringDB](https://www.peeringdb.com).

Este exemplo mostra como usar Seattle como o local de emparelhamento para criar um emparelhamento.

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```
