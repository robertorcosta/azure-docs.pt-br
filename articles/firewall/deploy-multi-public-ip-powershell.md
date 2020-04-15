---
title: Implantar o Firewall Do Azure com vários endereços IP públicos usando o PowerShell
description: Neste artigo, você aprende a implantar um Firewall Azure com vários endereços IP públicos usando o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 21f645e64c9944ed102f538710ea6facc26c7e83
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314026"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Implantar um Firewall do Azure com vários endereços IP públicos usando o Azure PowerShell

Esse recurso permite os seguintes cenários:

- **DNAT**: várias instâncias de porta padrão podem ser traduzidas em seus servidores de back-end. Por exemplo, se você tem dois endereços IP públicos, pode traduzir a porta TCP 3389 (RDP) para os dois endereços IP.
- **SNAT**: as portas adicionais estão disponíveis para conexões SNAT de saída, reduzindo a possibilidade de esgotamento da porta SNAT. Neste momento, o Firewall do Azure seleciona aleatoriamente o endereço IP público do código-fonte a ser usado para uma conexão. Se você tiver qualquer filtragem downstream em sua rede, precisará permitir todos os endereços IP públicos associados com seu firewall.
 
O Firewall do Azure com vários endereços IP públicos está disponível por meio do portal do Azure, do Azure PowerShell, da CLI do Azure, do REST e de modelos. Você pode implantar um Firewall Azure com até 100 endereços IP públicos.

Os exemplos a seguir do Azure PowerShell mostram como você pode configurar, adicionar e remover endereços IP públicos para o Firewall Do Azure.

> [!NOTE]
> Você não pode remover a primeira configuração ipConfiguration da página de configuração pública de endereço IP do Azure Firewall. Se você quiser modificar o endereço IP, você pode usar o Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Crie um firewall com dois ou mais endereços IP públicos

Este exemplo cria um firewall conectado ao *vnet de* rede virtual com dois endereços IP públicos.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Adicione um endereço IP público a um firewall existente

Neste exemplo, o endereço IP público *azFwPublicIp1* é anexado ao firewall.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Remova um endereço IP público de um firewall existente

Neste exemplo, o endereço IP público *azFwPublicIp1* é separado do firewall.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Próximas etapas

* [Quickstart: Crie um Firewall Azure com vários endereços IP públicos - modelo do Gerenciador de recursos](quick-create-multiple-ip-template.md)
