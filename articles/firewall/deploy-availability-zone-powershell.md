---
title: Implantar firewall azure com zonas de disponibilidade usando o PowerShell
description: Neste artigo, você aprende como implantar um Firewall Azure com Zonas de Disponibilidade usando o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195917"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Implantar um Firewall do Azure com zonas de disponibilidade usando o Azure PowerShell

O Firewall do Azure pode ser configurado durante a implantação para abranger várias Zonas de Disponibilidade para aumentar a disponibilidade.

Esse recurso permite os seguintes cenários:

- Você pode aumentar a disponibilidade para 99,99% de tempo de atividade. Para saber mais, confira o [Contrato de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) do Firewall do Azure. O SLA de tempo de atividade de 99,99% é oferecido quando duas ou mais Zonas de Disponibilidade estão selecionadas.
- Você também pode associar o Firewall do Azure a uma zona específica apenas por motivos de proximidade, usando o SLA de 99,95% padrão de serviço.

Para obter mais informações sobre as zonas de disponibilidade do Firewall do Azure, consulte [o que é o Firewall do Azure?](overview.md)

O exemplo a seguir do Azure PowerShell mostra como você pode implantar um Firewall Azure com Zonas de Disponibilidade.

## <a name="create-a-firewall-with-availability-zones"></a>Crie um firewall com zonas de disponibilidade

Este exemplo cria um firewall nas zonas 1, 2 e 3.

Quando o endereço IP público padrão é criado, nenhuma região específica é especificada. Isso cria um endereço IP redundante de zona por padrão. Os endereços IP públicos padrão podem ser configurados em todas as regiões ou em uma única região.

É importante saber, porque você não pode ter um firewall na zona 1 e um endereço IP na zona 2. Mas você pode ter um firewall na zona 1 e endereço IP em todas as regiões, ou um firewall e um endereço IP na mesma zona única para fins de proximidade.

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

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Monitor logs do Firewall do Azure](./tutorial-diagnostics.md)
