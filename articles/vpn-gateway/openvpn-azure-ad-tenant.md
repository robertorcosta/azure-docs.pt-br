---
title: 'Gateway de VPN: locatário do Azure AD para conexões VPN P2S: autenticação do Azure AD'
description: Saiba como configurar um locatário do Azure AD para autenticação de VPN aberta do P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 74df878baa6ed4e2ba902a73a7830c0b075c58ce
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90981523"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um locatário do Azure Active Directory para conexões de protocolo P2S OpenVPN

Ao conectar-se à sua VNet, você pode usar a autenticação baseada em certificado ou a autenticação RADIUS. No entanto, ao usar o protocolo VPN aberto, você também pode usar a autenticação Azure Active Directory. Este artigo ajuda você a configurar um locatário do Azure AD para autenticação de VPN aberta do P2S.

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para conexões de protocolo OpenVPN® e requer o cliente VPN do Azure, que está disponível apenas para o Windows 10.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. verificar o locatário do Azure AD

Verifique se você tem um locatário do Azure AD. Se você não tiver um locatário do Azure AD, poderá criar um usando as etapas no artigo [criar um novo locatário](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nome organizacional
* Nome de domínio inicial

Exemplo:

   ![Novo locatário do Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. criar usuários de locatário do Azure AD

Seu locatário do Azure AD precisa das seguintes contas: uma conta de administrador global e uma conta de usuário mestre. A conta de usuário mestre é usada como a conta de incorporação mestre (conta de serviço). Ao criar uma conta de usuário do locatário do Azure AD, você ajusta a função do Diretório para o tipo de usuário que deseja criar.

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
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Alemanha

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecione a conta de **Administrador global**, se solicitado.

    ![ID do Diretório](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selecione **Aceitar** quando solicitado.

    ![Captura de tela mostra a mensagem permissões solicitadas de aceitação para sua organização com detalhes e a opção de aceitar.](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Em seu Azure AD, em **aplicativos empresariais**, você vê a **VPN do Azure** listada.

    ![VPN do Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Se você ainda não tiver um ambiente de ponto a site funcionando, siga as instruções para criar um. Consulte [criar uma VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar um gateway de VPN ponto a site. 

    > [!IMPORTANT]
    > Não há suporte para o SKU Básico no OpenVPN.

9. Habilite a autenticação do Azure AD no gateway de VPN navegando para **configuração de ponto a site** e selecionando **OpenVPN (SSL)** como o **tipo de túnel**. Selecione **Azure Active Directory** como o **tipo de autenticação** e preencha as informações na seção **Azure Active Directory** .

    ![VPN do Azure](./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png)


   > [!NOTE]
   > Certifique-se de incluir uma barra à direita no final do `AadIssuerUri` valor. Caso contrário, a conexão poderá falhar.

10. Crie e baixe o perfil clicando no link **baixar cliente VPN** .

11. Extraia o arquivo zip baixado.

12. Navegue até a pasta "AzureVPN" descompactada.

13. Anote o local do arquivo "azurevpnconfig.xml". O azurevpnconfig.xml contém a configuração para a conexão VPN e pode ser importado diretamente para o aplicativo cliente VPN do Azure. Você também pode distribuir esse arquivo para todos os usuários que precisam se conectar por email ou outros meios. O usuário precisará de credenciais válidas do Azure AD para se conectar com êxito.

## <a name="next-steps"></a>Próximas etapas

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para conexões VPN P2S](openvpn-azure-ad-client.md).
