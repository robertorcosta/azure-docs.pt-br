---
title: 'Azure ExpressRoute: Configure MACsec'
description: Este artigo ajuda você a configurar o MACsec para proteger as conexões entre seus roteadores de borda e os roteadores de borda da Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 626302845dfb4b19deb921675601818b35ab8edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083540"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Configure o MACsec nas portas Direct expressRoute

Este artigo ajuda você a configurar o MACsec para proteger as conexões entre seus roteadores de borda e os roteadores de borda da Microsoft usando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, confirme o seguinte:

* Você entende [os fluxos de trabalho de provisionamento Direct do ExpressRoute](expressroute-erdirect-about.md).
* Você criou um [recurso de porta ExpressRoute Direct](expressroute-howto-erdirect.md).
* Se você quiser executar o PowerShell localmente, verifique se a versão mais recente do Azure PowerShell está instalada no seu computador.

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Faça login e selecione a assinatura certa

Para iniciar a configuração, faça login na sua conta do Azure e selecione a assinatura que deseja usar.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Crie o Cofre de Chaves do Azure, segredos MACsec e identidade do usuário

1. Crie uma instância do Key Vault para armazenar segredos MACsec em um novo grupo de recursos.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Se você já tem um cofre de chaves ou um grupo de recursos, você pode reutilizá-los. No entanto, é fundamental que você habilite o recurso [ **de exclusão suave** ](../key-vault/key-vault-ovw-soft-delete.md) no cofre de chaves existente. Se o soft-delete não estiver ativado, você poderá usar os seguintes comandos para habilitá-lo:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Crie uma identidade de usuário.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Se o New-AzUserAssignedIdentity não for reconhecido como um cmdlet PowerShell válido, instale o seguinte módulo (no modo Administrador) e execute novamente o comando acima.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Crie uma chave de associação de conectividade (CAK) e um nome-chave da associação de conectividade (CKN) e armazene-os no cofre-chave.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Atribua a permissão GET à identidade do usuário.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Agora, essa identidade pode obter os segredos, por exemplo CAK e CKN, do cofre de chaves.
5. Defina essa identidade de usuário para ser usada pelo ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Configure o MACsec nas portas Direct expressRoute

### <a name="to-enable-macsec"></a>Para habilitar o MACsec

Cada instância do ExpressRoute Direct tem duas portas físicas. Você pode optar por ativar o MACsec em ambas as portas ao mesmo tempo ou ativar o MACsec em uma porta de cada vez. Fazê-lo em uma porta de cada vez (mudando o tráfego para uma porta ativa enquanto atende a outra porta) pode ajudar a minimizar a interrupção se o ExpressRoute Direct já estiver em serviço.

1. Defina segredos macsec e cifra e associe a identidade do usuário com a porta para que o código de gerenciamento do ExpressRoute possa acessar os segredos MACsec, se necessário.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. (Opcional) Se as portas estiverem no estado administrativo para baixo, você pode executar os seguintes comandos para trazer as portas.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    Neste ponto, o MACsec está habilitado nas portas ExpressRoute Direct no lado da Microsoft. Se você não configurá-lo em seus dispositivos de borda, você pode começar a configurá-los com os mesmos segredos e cifras MACsec.

### <a name="to-disable-macsec"></a>Para desativar o MACsec

Se o MACsec não for mais desejado na instância Do ExpressRoute Direct, você poderá executar os seguintes comandos para desativá-lo.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

Neste ponto, o MACsec está desativado nas portas ExpressRoute Direct no lado da Microsoft.

### <a name="test-connectivity"></a>Testar a conectividade
Depois de configurar o MACsec (incluindo a atualização da chave MACsec) em suas portas ExpressRoute Direct, [verifique](expressroute-troubleshooting-expressroute-overview.md) se as sessões de BGP dos circuitos estão funcionando. Se você ainda não tiver nenhum circuito nas portas, crie um primeiro e configure o Azure Private Peering ou o Microsoft Peering do circuito. Se o MACsec estiver mal configurado, incluindo a incompatibilidade de chave macsec, entre seus dispositivos de rede e dispositivos de rede da Microsoft, você não verá a resolução ARP na camada 2 e no estabelecimento BGP na camada 3. Se tudo estiver configurado corretamente, você deve ver as rotas BGP anunciadas corretamente em ambas as direções e o fluxo de dados do aplicativo de acordo com o ExpressRoute.

## <a name="next-steps"></a>Próximas etapas
1. [Crie um circuito ExpressRoute no ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Vincular um circuito do ExpressRoute a uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)
3. [Verifique a conectividade ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
