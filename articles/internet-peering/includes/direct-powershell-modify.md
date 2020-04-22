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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680754"
---
Esta seção descreve como executar as seguintes operações de modificação para peering direto:

* Adicione conexões de peering direto.
* Remova as conexões de peering direto.
* Atualize ou desabaixe a largura de banda em conexões ativas.
* Adicione sessões IPv4 ou IPv6 em conexões ativas.
* Remova as sessões IPv4 ou IPv6 em conexões ativas.

### <a name="add-direct-peering-connections"></a>Adicionar conexões de peering direto

Este exemplo descreve como adicionar conexões ao peering Direct existente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Remova conexões de peering direto

A remoção de uma conexão não é suportada no momento no PowerShell. Para obter mais informações, entre em contato com [a Microsoft .](mailto:peeringexperience@microsoft.com)

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou desfazer a largura de banda em conexões Ativas

Este exemplo descreve como adicionar 10 Gbps a uma conexão Direta existente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>Adicione sessões IPv4 ou IPv6 em conexões ativas

Este exemplo descreve como adicionar uma sessão IPv6 em uma conexão Direta existente com apenas uma sessão IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>Remova as sessões IPv4 ou IPv6 em conexões ativas

A remoção de uma sessão IPv4 ou IPv6 de uma conexão existente não é suportada no momento no PowerShell. Para obter mais informações, entre em contato com [a Microsoft .](mailto:peeringexperience@microsoft.com)