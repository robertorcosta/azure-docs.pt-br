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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774257"
---
Esta seção descreve como executar as seguintes operações de modificação para peering direto:

* Adicionar conexões de peering direto
* Remova conexões de peering direto
* Atualize ou desabaixe a largura de banda em conexões ativas.
* Adicione a sessão IPv4/IPv6 em conexões ativas.
* Remova a sessão IPv4/IPv6 em conexões ativas.

### <a name="add-direct-peering-connections"></a>Adicionar conexões de peering direto

Abaixo exemplo descreve como adicionar conexões ao peering direto existente

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

A remoção de uma conexão não é suportada no momento no PowerShell. Entre em contato com [a Microsoft .](mailto:peeringexperience@microsoft.com)

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou desfazer a largura de banda em conexões Ativas

Abaixo o exemplo descreve como adicionar 10Gbps à conexão direta existente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Adicione a sessão IPv4/IPv6 em conexões ativas.

Abaixo o exemplo descreve como adicionar sessão IPv6 em uma conexão direta existente com apenas a sessão IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Remova a sessão IPv4/IPv6 em conexões ativas.

A remoção de uma sessão IPv4/IPv6 de uma conexão existente não é suportada no momento no PowerShell. Entre em contato com [a Microsoft .](mailto:peeringexperience@microsoft.com)