---
title: Configurar redes virtuais e firewalls do Azure Key Vault - Azure Key Vault
description: Instruções passo a passo para configurar redes virtuais e firewalls do Key Vault
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8617b0b71e58d22ccd2cf753e4ddc862932f68da
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536048"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurar redes virtuais e firewalls do Azure Key Vault

Este guia fornece instruções passo a passo para configurar redes virtuais e firewalls do Azure Key Vault para restringir acesso ao cofre de chaves. Os [pontos de extremidade de serviço de rede virtual para Key Vault](overview-vnet-service-endpoints.md) permitem restringir o acesso a uma rede virtual especificada e um conjunto de intervalos de endereços IPv4 (protocolo de internet versão 4).

> [!IMPORTANT]
> Depois que as regras de firewall estiverem ativas, os usuários podem realizar apenas operações de [plano de dados](secure-your-key-vault.md#data-plane-access-control) do Key Vault quando as solicitações deles originarem-se de redes virtuais ou intervalos de endereços IPv4 permitidos. Isso também aplica-se ao acessar o Key Vault a partir do portal do Azure. Embora os usuários possam navegar em um cofre de chaves a partir do portal do Azure, eles talvez não consigam listar chaves, segredos ou certificados se o computador cliente deles não estiver na lista permitida. Isso também afeta o Seletor do Cofre de Chaves de outros serviços do Azure. Os usuários poderão ver a lista de cofres de chaves, mas não listar chaves, se as regras de firewall impedirem o computador cliente.

> [!NOTE]
> Esteja ciente das seguintes limitações de configuração:
> * Um máximo de 127 regras da rede virtual e 127 regras de IPv4 são permitidas. 
> * Intervalos de endereços pequenos que usam tamanhos de prefixo "/31" ou "/32" não têm suporte. Em vez disso, esses intervalos devem ser configurados usando regras de endereço IP individuais.
> * Regras de rede IP somente são permitidas para endereços IP públicos. Os intervalos de endereços IP reservados para redes privadas (conforme definido na RFC 1918) não são permitidos em regras de IP. Redes privadas incluem endereços que começam com **10.** , **172.16-31** e **192.168.** . 
> * Atualmente, somente há suporte para endereços IPv4.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Segue como configurar redes virtuais e firewalls do Key Vault usando o portal do Azure:

1. Navegue até o cofre de chaves que você quer proteger.
2. Selecione **Rede** e, em seguida, a guia **Firewalls e redes virtuais**.
3. Em **Permitir acesso de**, clique em **Redes selecionadas**.
4. Para adicionar redes virtuais existentes a firewalls e regras da rede virtual, selecione **+ Adicionar redes virtuais existentes**.
5. Na nova folha que é aberta, selecione a assinatura, as redes virtuais e as sub-redes que você quer permitir acesso a esse cofre de chaves. Se as redes virtuais e sub-redes que você selecionar não tiverem pontos de extremidade de serviço habilitados, confirme que você quer habilitar pontos de extremidade de serviço e, em seguida, selecione **Habilitar**. Pode demorar até 15 minutos para entrar em vigor.
6. Em **Redes IP**, adicione intervalos de endereços IPv4, digitando intervalos de endereços IPv4 na [notação CIDR (Roteamento Entre Domínios Sem Classe)](https://tools.ietf.org/html/rfc4632) ou endereços IP individuais.
7. Caso deseje permitir que os Serviços Confiáveis da Microsoft ignorem o Firewall do Key Vault, selecione 'Sim'. Para obter uma lista completa dos Serviços Confiáveis do Key Vault atuais, confira o link a seguir. [Serviços Confiáveis do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
7. Clique em **Salvar**.

Também é possível adicionar novas redes virtuais e sub-redes e, em seguida, habilitar pontos de extremidade de serviço para as redes virtuais e sub-redes recém-criadas, selecionando **+ Adicionar nova rede virtual**. Em seguida, siga os prompts.

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure 

Segue como configurar redes virtuais e firewalls do Key Vault usando a CLI do Azure

1. [Instale a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e [conecte-se](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Liste as regras da rede virtual disponíveis. Se você ainda não definiu regras para esse cofre de chaves, a lista estará vazia.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Habilite um ponto de extremidade de serviço para Key Vault em uma rede virtual e sub-rede existentes.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Adicionar uma regra de rede para uma rede virtual e sub-rede.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Adicione um intervalo de endereços IP que permite tráfego.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Se o cofre de chaves precisar ser acessível por qualquer serviço confiável, defina `bypass` para `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Ative as regras de rede definindo a ação padrão como `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Segue como configurar redes virtuais e firewalls do Key Vault usando o PowerShell:

1. Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) mais recente e [conecte](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Liste as regras da rede virtual disponíveis. Se você ainda não definiu regras para esse cofre de chaves, a lista estará vazia.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Habilitar ponto de extremidade de serviço para Key Vault em uma rede virtual e sub-rede existentes.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Adicionar uma regra de rede para uma rede virtual e sub-rede.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Adicione um intervalo de endereços IP que permite tráfego.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Se o cofre de chaves precisar ser acessível por qualquer serviço confiável, defina `bypass` para `AzureServices`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Ative as regras de rede definindo a ação padrão como `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Referências
* Referência de modelo ARM: [Referência de modelo ARM do Azure Key Vault](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/vaults)
* Comandos da CLI do Azure: [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Cmdlets do Azure PowerShell: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Próximas etapas

* [Pontos de extremidade de serviço de rede virtual para Key Vault](overview-vnet-service-endpoints.md)
* [Proteger seu cofre de chaves](secure-your-key-vault.md)
