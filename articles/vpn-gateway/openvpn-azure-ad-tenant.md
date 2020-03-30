---
title: 'VPN Gateway: Inquilino do Azure AD para conexões P2S VPN: autenticação Azure AD'
description: Você pode usar o P2S VPN para se conectar ao seu VNet usando a autenticação Azure AD
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: alzam
ms.openlocfilehash: f4092f651a3058c8a2e738c81d9db7e296386bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402903"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Crie um inquilino do Azure Active Directory para conexões de protocolo P2S OpenVPN

Ao se conectar ao seu VNet, você pode usar autenticação baseada em certificados ou autenticação RADIUS. No entanto, quando você usa o protocolo Open VPN, você também pode usar a autenticação do Azure Active Directory. Este artigo ajuda você a configurar um inquilino Azure AD para autenticação P2S Open VPN.

> [!NOTE]
> A Autenticação do Azure AD é compatível apenas com conexões de protocolo OpenVPN®.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Verifique o inquilino azure AD

Verifique se você tem um inquilino azure AD. Se você não tiver um inquilino Azure AD, você pode criar um usando as etapas do Criar um novo artigo [de inquilino:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nome organizacional
* Nome de domínio inicial

Exemplo:

   ![Novo inquilino azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Crie usuários inquilinos do Azure AD

O inquilino azure AD precisa das seguintes contas: uma conta de administração global e uma conta de usuário mestre. A conta de usuário mestre é usada como sua conta de incorporação mestre (conta de serviço). Quando você cria uma conta de usuário de inquilino Azure AD, você ajusta a função Diretório para o tipo de usuário que deseja criar.

Use as etapas [deste artigo](../active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois usuários para o seu inquilino Azure AD. Certifique-se de alterar a **função diretório** para criar os tipos de conta:

* Administrador global
* Usuário

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Habilite a autenticação do Azure AD no gateway VPN

1. Localize o ID do diretório do diretório que você deseja usar para autenticação. Ele está listado na seção propriedades da página Active Directory.

    ![ID do Diretório](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Copie a ID de diretório.

3. Faça login no portal Azure como um usuário que é atribuído à função **de administrador** Global.

4. Em seguida, dê o consentimento do governo. Copie e cole a URL que pertence à sua localização de implantação na barra de endereços do seu navegador:

    Público

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Alemanha

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecione a conta **do Global Admin** se solicitado.

    ![ID do Diretório](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selecione **Aceitar** quando solicitado.

    ![Aceitar](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Em seu Azure AD, em **aplicativos Corporativos,** você verá **o Azure VPN** listado.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Se você ainda não tiver um ambiente de ponto a site funcionando, siga as instruções para criar um. Veja [Criar uma VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar um gateway de VPN ponto a site com a autenticação de certificado nativa do Azure. 

    > [!IMPORTANT]
    > Não há suporte para o SKU Básico no OpenVPN.

9. Habilite a autenticação do Azure AD no gateway VPN executando os seguintes comandos, com certeza de modificar o comando para refletir seu próprio ambiente:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > Certifique-se de incluir uma barra de `AadIssuerUri` arrasto no final do valor. Caso contrário, o comando falhará.

10. Crie e baixe o perfil executando os seguintes comandos. Alterar os valores -ResourceGroupName e -Name para corresponder aos seus.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. Depois de executar os comandos, você vê um resultado semelhante ao abaixo. Copie a URL de resultado para o seu navegador para baixar o arquivo zip do perfil.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Extrair o arquivo zip baixado.

13. Navegue até a pasta "AzureVPN" sem zíper.

14. Anote a localização do arquivo "azurevpnconfig.xml". O azurevpnconfig.xml contém a configuração da conexão VPN e pode ser importado diretamente para o aplicativo Azure VPN Client. Você também pode distribuir este arquivo para todos os usuários que precisam se conectar por e-mail ou outros meios. O usuário precisará de credenciais Ad válidas do Azure para se conectar com sucesso.

## <a name="next-steps"></a>Próximas etapas

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [Configurar um cliente VPN para conexões P2S VPN](openvpn-azure-ad-client.md).
