---
title: 'VPN Gateway: Inquilino do Azure AD para diferentes grupos de usuários: autenticação Azure AD'
description: Você pode usar o P2S VPN para se conectar ao seu VNet usando a autenticação Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485556"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Crie um inquilino do Azure Active Directory para conexões de protocolo P2S OpenVPN

Ao se conectar ao seu VNet, você pode usar autenticação baseada em certificados ou autenticação RADIUS. No entanto, quando você usa o protocolo Open VPN, você também pode usar a autenticação do Azure Active Directory. Se você quiser que diferentes usuários possam se conectar a diferentes gateways VPN, você pode registrar vários aplicativos em AD e vinculá-los a diferentes gateways VPN. Este artigo ajuda você a configurar um inquilino Azure AD para autenticação P2S OpenVPN e criar e registrar vários aplicativos no Azure AD para permitir acesso diferente para diferentes usuários e grupos.

> [!NOTE]
> A Autenticação do Azure AD é compatível apenas com conexões de protocolo OpenVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Habilite a autenticação no gateway

Nesta etapa, você habilita a autenticação do Azure AD no gateway VPN.

1. Habilite a autenticação do Azure AD no gateway VPN executando os seguintes comandos. Certifique-se de modificar os comandos para refletir seu próprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Não use o ID de aplicativo do cliente Azure VPN nos comandos acima: Ele concederá a todos os usuários acesso ao gateway VPN. Use o ID do aplicativo registrado.

2. Crie e baixe o perfil executando os seguintes comandos. Alterar os valores -ResourcGroupName e -Name para corresponder aos seus.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Depois de executar os comandos, você vê um resultado semelhante ao abaixo. Copie a URL de resultado para o seu navegador para baixar o arquivo zip do perfil.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Extrair o arquivo zip baixado.

5. Navegue até a pasta "AzureVPN" sem zíper.

6. Anote a localização do arquivo "azurevpnconfig.xml". O azurevpnconfig.xml contém a configuração da conexão VPN e pode ser importado diretamente para o aplicativo Azure VPN Client. Você também pode distribuir este arquivo para todos os usuários que precisam se conectar por e-mail ou outros meios. O usuário precisará de credenciais Ad válidas do Azure para se conectar com sucesso.

## <a name="next-steps"></a>Próximas etapas

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [Configurar um cliente VPN para conexões P2S VPN](openvpn-azure-ad-client.md).
