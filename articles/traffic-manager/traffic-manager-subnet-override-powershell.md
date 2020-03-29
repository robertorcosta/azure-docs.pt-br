---
title: Sub-rede do Azure Traffic Manager é uma substituição usando o Azure PowerShell | Microsoft Docs
description: Este artigo irá ajudá-lo a entender como a substituição da sub-rede do Gerenciador de tráfego é usada para substituir o método de roteamento de um perfil do Gerenciador de tráfego para direcionar o tráfego para um ponto final com base no endereço IP do usuário final via intervalo IP predefinido para mapeamentos de pontos finais usando o Azure Powershell.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938413"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Substituição da sub-rede do Gerenciador de tráfego usando o Azure Powershell

A substituição da sub-rede do Gerenciador de Tráfego permite alterar o método de roteamento de um perfil.  A adição de uma substituição direcionará o tráfego com base no endereço IP do usuário final com um intervalo IP predefinido para mapeamento de ponto final. 

## <a name="how-subnet-override-works"></a>Como funciona a substituição da sub-rede

Quando as substituições de sub-rede forem adicionadas a um perfil de gerenciador de tráfego, o Gerenciador de Tráfego primeiro verificará se há uma substituição de sub-rede para o endereço IP do usuário final. Se um for encontrado, a consulta de DNS do usuário será direcionada para o ponto final correspondente.  Se um mapeamento não for encontrado, o Traffic Manager voltará ao método de roteamento original do perfil. 

As faixas de endereço IP podem ser especificadas como intervalos CIDR (por exemplo, 1.2.3.0/24) ou como intervalos de endereços (por exemplo, 1.2.3.4-5.6.7.8). As faixas de IP associadas a cada ponto final devem ser exclusivas desse ponto final. Qualquer sobreposição de faixas de IP entre diferentes pontos finais fará com que o perfil seja rejeitado pelo Gerenciador de Tráfego.

Existem dois tipos de perfis de roteamento que suportam substituições de sub-rede:

* **Geographic** - Se o Gerenciador de tráfego encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, ele encaminhará a consulta para o ponto final, independentemente da saúde do ponto final.
* **Desempenho** - Se o Gerenciador de tráfego encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, ele só encaminhará o tráfego para o ponto final se estiver saudável.  O Gerenciador de Tráfego voltará à heurística de roteamento de desempenho se o ponto final de substituição da sub-rede não estiver saudável.

## <a name="create-a-traffic-manager-subnet-override"></a>Criar uma substituição de sub-rede do Gerenciador de Tráfego

Para criar uma substituição de sub-rede do Gerenciador de tráfego, você pode usar o Azure PowerShell para adicionar as sub-redes para a substituição ao ponto final do Gerenciador de tráfego.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

É possível executar os comandos a seguir no [Azure Cloud Shell](https://shell.azure.com/powershell) ou executando o PowerShell no computador. O Azure Cloud Shell é um shell interativo gratuito. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Se você executar o PowerShell pelo computador, será necessário ter o módulo do Azure PowerShell do Azure, 1.0.0 ou posterior. É possível executar `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se estiver executando o PowerShell localmente, também será necessário executar `Login-AzAccount` entrar no Azure.


1. **Recupere o ponto final do Gerenciador de Tráfego:**

    Para ativar a substituição da sub-rede, recupere o ponto final que deseja adicionar a substituição e armazene-a em uma variável usando [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Substitua o Nome, O Nome do Perfil e o ResourceGroupName pelos valores do ponto final que você está alterando.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Adicione o intervalo de endereçoIP ao ponto final:**
    
    Para adicionar o intervalo de endereços IP ao ponto final, você usará [Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) para adicionar o intervalo.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Uma vez que os intervalos sejam adicionados, use [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) para atualizar o ponto final.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
A remoção da faixa de endereço IP pode ser concluída usando [remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Recupere o ponto final do Gerenciador de Tráfego:**

    Para ativar a substituição da sub-rede, recupere o ponto final que deseja adicionar a substituição e armazene-a em uma variável usando [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Substitua o Nome, O Nome do Perfil e o ResourceGroupName pelos valores do ponto final que você está alterando.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Remova a faixa de endereço IP do ponto final:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     Uma vez que os intervalos sejam removidos, use [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) para atualizar o ponto final.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Gerenciador de Tráfego.

Conheça o [método de roteamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method) da Subnet
