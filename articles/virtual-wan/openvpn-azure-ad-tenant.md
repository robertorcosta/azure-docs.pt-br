---
title: 'Inquilino do Azure AD para conexões VPN do usuário: autenticação Azure AD'
description: Você pode usar o Azure Virtual WAN User VPN (ponto a ponto) para se conectar à sua VNet usando a autenticação Azure AD
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 74347ce969b6a5ffd57f5ca8396517e78590f3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059451"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Crie um inquilino do Azure Active Directory para conexões de protocolo Do usuário VPN OpenVPN

Ao se conectar ao seu VNet, você pode usar autenticação baseada em certificados ou autenticação RADIUS. No entanto, quando você usa o protocolo Open VPN, você também pode usar a autenticação do Azure Active Directory. Este artigo ajuda você a configurar um inquilino Azure AD para autenticação VPN aberta do usuário WAN Virtual (ponto a ponto).

> [!NOTE]
> A autenticação Azure AD é&reg; suportada apenas para conexões de protocolo OpenVPN.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Crie o inquilino Azure AD

Criar um inquilino Azure AD usando as etapas do Criar um novo artigo [de inquilino:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nome organizacional
* Nome de domínio inicial

Exemplo:

   ![Novo inquilino azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Crie usuários inquilinos do Azure AD

Em seguida, crie duas contas de usuário. Crie uma conta de administração global e uma conta de usuário mestre. A conta de usuário mestre é usada como sua conta de incorporação mestre (conta de serviço). Quando você cria uma conta de usuário de inquilino Azure AD, você ajusta a função Diretório para o tipo de usuário que deseja criar.

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecione a conta **do Global Admin** se solicitado.

    ![ID do Diretório](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selecione **Aceitar** quando solicitado.

    ![Aceitar](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Em seu Azure AD, em **aplicativos Corporativos,** você verá **o Azure VPN** listado.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Configure a autenticação Azure AD para VPN do usuário e atribua-a a um Hub Virtual seguindo as etapas de [autenticação Configure Azure AD para conexão Point-to-Site ao Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Próximas etapas

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN e associá-lo a um Hub Virtual. Consulte [Configurar a autenticação Azure AD para conexão point-to-site com o Azure](virtual-wan-point-to-site-azure-ad.md).
