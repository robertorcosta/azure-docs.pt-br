---
title: Criar um grupo de segurança de rede (clássico) usando o PowerShell | Microsoft Docs
description: Saiba como criar e implantar um grupo de segurança de rede (clássico) usando o PowerShell
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 86810b0d-0240-46a2-8548-fca22daa56f3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 2/02/2019
ms.author: genli
ms.openlocfilehash: 0f957c5d59dec06057841a95ec48a54462778a69
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672490"
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>Criar um grupo de segurança de rede (clássico) usando o PowerShell
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação clássico. Também é possível [criar NSGs no modelo de implantação do Gerenciador de Recursos](tutorial-filter-network-traffic.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

O exemplo de comando PowerShell abaixo espera um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos da forma como eles aparecem neste documento, primeiro crie o ambiente de teste [criando uma VNet](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Criar um NSG para a sub-rede de front-end

1. Se você nunca usou o Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

2. Crie um grupo de segurança de rede nomeado *NSG-FrontEnd*:

    ```powershell   
    New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
      -Label "Front end subnet NSG"
   ```

3. Crie uma regra de segurança permitindo acesso da Internet à porta 3389:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '3389'
   ```

4. Crie uma regra de segurança permitindo acesso da Internet à porta 80:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```
5. Associar o grupo de segurança de rede a uma sub-rede:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-Frontend" `
    | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName "TestVNet" `
    -Subnet "FrontEnd"
    ```
## <a name="create-an-nsg-for-the-back-end-subnet"></a>Criar um NSG para a sub-rede de back-end

1. Crie um grupo de segurança de rede nomeado *NSG-BackEnd*:
   
    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
      -Label "Back end subnet NSG"
    ```

2. Crie uma regra de segurança, permitindo o acesso na sub-rede front-end à porta 1433 (porta padrão usada pelo SQL Server):
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. Crie uma regra de segurança impedindo o acesso da sub-rede à Internet:
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```
4. Associar o grupo de segurança de rede a uma sub-rede:
    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-Backend" `
    | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName "TestVNet" `
    -Subnet "BackEnd"
    ```