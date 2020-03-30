---
title: Converta um peering exchange legado para o recurso do Azure usando o PowerShell
titleSuffix: Azure
description: Converta um peering exchange legado para o recurso do Azure usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775388"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Converta um peering exchange legado para o recurso do Azure usando o PowerShell

Este artigo descreve como converter um exchange legado existente em peering para o recurso do Azure usando cmdlets PowerShell.

Se preferir, você pode completar este guia usando o [portal](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Antes de começar
* Revise [pré-requisitos](prerequisites.md) e [faça o passo a passo do Exchange](walkthrough-exchange-all.md) antes de iniciar a configuração.

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Converter um emparelhamento do Exchange herdado para o recurso do Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Obtenha o peering legacy do Exchange para conversão
Abaixo está o exemplo para obter o peering legacy exchange em Seattle local de peering:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

A resposta se assemelha à seguinte:
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Converter peering legado
O comando abaixo pode ser usado para converter o peering legacy do Exchange para o recurso do Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Observe que ao converter o peering legado para o recurso azure, as modificações não são suportadas&nbsp;

Abaixo está uma resposta de exemplo quando o provisionamento de ponta a ponta foi concluído com sucesso:

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>Recursos adicionais
Você pode obter descrições detalhadas de todos os parâmetros executando o seguinte comando:

```powershell
Get-Help Get-AzPeering -detailed
```
Para obter mais informações, visite [perguntas frequentes sobre peering na Internet](faqs.md)

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um peering exchange usando o PowerShell](howto-exchange-powershell.md)
