---
title: 'Gateway de VPN: locatário do Azure AD para grupos de usuários diferentes: autenticação do Azure AD'
description: Você pode usar a VPN P2S para se conectar à sua VNet usando a autenticação do Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485556"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um locatário de Azure Active Directory para conexões de protocolo P2S OpenVPN

Ao conectar-se à sua VNet, você pode usar a autenticação baseada em certificado ou a autenticação RADIUS. No entanto, ao usar o protocolo VPN aberto, você também pode usar a autenticação Azure Active Directory. Se desejar que um conjunto diferente de usuários seja capaz de se conectar a diferentes gateways de VPN, você poderá registrar vários aplicativos no AD e vinculá-los a diferentes gateways de VPN. Este artigo ajuda você a configurar um locatário do Azure AD para autenticação P2S OpenVPN e criar e registrar vários aplicativos no Azure AD para permitir acesso diferente para diferentes usuários e grupos.

> [!NOTE]
> A Autenticação do Azure AD é compatível apenas com conexões de protocolo OpenVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="enable-authentication"></a>6. habilitar a autenticação no gateway

Nesta etapa, você habilita a autenticação do Azure AD no gateway de VPN.

1. Habilite a autenticação do Azure AD no gateway de VPN executando os comandos a seguir. Certifique-se de modificar os comandos para refletir seu próprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Não use a ID do aplicativo do cliente de VPN do Azure nos comandos acima: ele concederá a todos os usuários acesso ao gateway de VPN. Use a ID dos aplicativos que você registrou.

2. Crie e baixe o perfil executando os comandos a seguir. Altere os valores-ResourcGroupName e-Name para que correspondam aos seus próprios.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Depois de executar os comandos, você verá um resultado semelhante ao mostrado abaixo. Copie a URL do resultado para o navegador para baixar o arquivo zip do perfil.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Extraia o arquivo zip baixado.

5. Navegue até a pasta "AzureVPN" descompactada.

6. Anote o local do arquivo "azurevpnconfig. xml". O azurevpnconfig. xml contém a configuração para a conexão VPN e pode ser importado diretamente para o aplicativo cliente VPN do Azure. Você também pode distribuir esse arquivo para todos os usuários que precisam se conectar por email ou outros meios. O usuário precisará de credenciais válidas do Azure AD para se conectar com êxito.

## <a name="next-steps"></a>Próximas etapas

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para conexões VPN P2S](openvpn-azure-ad-client.md).
