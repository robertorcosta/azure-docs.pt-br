---
title: 'Locatário do Azure AD para conexões VPN de usuário: autenticação do Azure AD'
description: Você pode usar a VPN do usuário da WAN virtual do Azure (ponto a site) para se conectar à sua VNet usando a autenticação do Azure AD
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 76c65d194d03dd1b7ff4cc2f3b45d84ff7909968
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753370"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Criar um locatário Azure Active Directory para conexões de protocolo VPN do usuário OpenVPN

Ao conectar-se à sua VNet, você pode usar a autenticação baseada em certificado ou a autenticação RADIUS. No entanto, ao usar o protocolo VPN aberto, você também pode usar a autenticação Azure Active Directory. Este artigo ajuda você a configurar um locatário do Azure AD para autenticação VPN de VPN de usuário de WAN virtual (ponto a site).

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para &reg; conexões de protocolo OpenVPN.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Criar o locatário do Azure AD

Crie um locatário do Azure AD usando as etapas no artigo [Criar um novo locatário](../active-directory/fundamentals/active-directory-access-create-new-tenant.md):

* Nome organizacional
* Nome de domínio inicial

Exemplo:

   ![Novo locatário do Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. criar usuários de locatário do Azure AD

Em seguida, crie duas contas de usuário. Crie uma conta de administrador global e uma conta de usuário mestre. A conta de usuário mestre é usada como a conta de incorporação mestre (conta de serviço). Ao criar uma conta de usuário do locatário do Azure AD, você ajusta a função do Diretório para o tipo de usuário que deseja criar.

Use as etapas [neste artigo](../active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois usuários para o locatário do Azure AD. Não deixe de alterar a **Função do diretório** para criar os tipos de conta:

* Administrador global
* Usuário

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. habilitar a autenticação do Azure AD no gateway de VPN

1. Localize a ID do diretório que você deseja usar para autenticação. Está listada na seção Propriedades da página Active Directory.

    ![ID do Diretório](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Copie a ID do diretório.

3. Entre no portal do Azure como usuário atribuído à função de **Administrador global**.

4. Em seguida, dê o consentimento do administrador. Copie e cole a URL pertencente ao local de implantação na barra de endereços do navegador:

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

5. Selecione a conta de **Administrador global**, se solicitado.

    ![ID do Diretório](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selecione **Aceitar** quando solicitado.

    ![Aceitar](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Em seu Azure AD, em **aplicativos empresariais**, você vê a **VPN do Azure** listada.

    ![VPN do Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Configure a autenticação do Azure AD para VPN de usuário e atribua-a a um hub virtual seguindo as etapas em [Configurar a autenticação do Azure ad para conexão ponto a site com o Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Próximas etapas

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN e associá-lo a um hub virtual. Consulte [Configurar a autenticação do Azure ad para a conexão ponto a site com o Azure](virtual-wan-point-to-site-azure-ad.md).
