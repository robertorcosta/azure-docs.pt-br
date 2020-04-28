---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/18/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e950d194ab48cec1a70c7bd17617332cb858a55d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77485552"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. criar o locatário do Azure AD

Crie um locatário do Azure AD usando as etapas no artigo [criar um novo locatário](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nome organizacional
* Nome de domínio inicial

  Exemplo:

   ![Novo locatário do Azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. criar usuários de locatários

Nesta etapa, você cria dois usuários de locatário do Azure AD: uma conta de administrador global e uma conta de usuário mestre. A conta de usuário mestre é usada como sua conta de incorporação mestre (conta de serviço). Ao criar uma conta de usuário de locatário do Azure AD, você ajusta a função de diretório para o tipo de usuário que deseja criar. Use as etapas neste [artigo](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois usuários para seu locatário do Azure AD. Certifique-se de alterar a **função de diretório** para criar os tipos de conta:

* Administrador global
* Usuário

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. registrar o cliente VPN

Registre o cliente VPN no locatário do Azure AD.

1. Localize a ID de diretório do diretório que você deseja usar para autenticação. Ele é listado na seção Propriedades da página Active Directory.

    ![ID do Diretório](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Copie a ID de diretório.

3. Entre no portal do Azure como um usuário que é atribuído à função de **administrador global** .

4. Em seguida, dê consentimento ao administrador. Copie e cole a URL que pertence ao seu local de implantação na barra de endereços do seu navegador:

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

5. Selecione a conta de **administrador global** , se solicitado.

    ![ID do Diretório](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Selecione **aceitar** quando solicitado.

    ![Aceitar](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Em seu Azure AD, em **aplicativos empresariais**, você verá a **VPN do Azure** listada.

     ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. registrar aplicativos adicionais

Nesta etapa, você registra aplicativos adicionais para vários usuários e grupos.

1. Em seu Azure Active Directory, clique em **registros de aplicativo** e em **+ novo registro**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Na página **registrar um aplicativo** , insira o **nome**. Selecione os **tipos de conta com suporte**desejados e, em seguida, clique em **registrar**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Depois que o novo aplicativo tiver sido registrado, clique em **expor uma API** na folha do aplicativo.

4. Clique em **+ Adicionar um escopo**.

5. Deixe o **URI da ID do aplicativo**padrão. Clique em **Salvar e continuar**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Preencha os campos obrigatórios e verifique se o **estado** está **habilitado**. Clique em **Adicionar escopo**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Clique em **expor uma API** e em **+ Adicionar um aplicativo cliente**.  Para **ID do cliente**, insira os seguintes valores, dependendo da nuvem:

    - Insira **41b23e61-6c1e-4545-B367-cd054e0ed4b4** para o Azure **Public**
    - Insira o **51bb15d4-3a4f-4ebf-9dca-40096fe32426** para o Azure **governamental**
    - Insira o **538ee9e6-310A-468d-AFEF-ea97365856a9** para o Azure **Alemanha**
    - Insira o **49f817b6-84ae-4cc0-928C-73f27289b3aa** para o Azure **China 21vianet**

8. Clique em **Adicionar aplicativo**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Copie a **ID do aplicativo (cliente)** da página **visão geral** . Você precisará dessas informações para configurar seu gateway (s) de VPN.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Repita as etapas nesta seção [registrar aplicativos adicionais](#register-apps) para criar quantos aplicativos forem necessários para seu requisito de segurança. Cada aplicativo será associado a um gateway de VPN e pode ter um conjunto diferente de usuários. Somente um aplicativo pode ser associado a um gateway.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. atribuir usuários a aplicativos

Atribua os usuários aos seus aplicativos.

1. Em **Azure ad-> aplicativos empresariais**, selecione o aplicativo recentemente registrado e clique em **Propriedades**. Verifique se a **atribuição de usuário é necessária?** está definida como **Sim**. Clique em **Salvar**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na página do aplicativo, clique em **usuários e grupos**e, em seguida, clique em **+ Adicionar usuário**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. Em **Adicionar atribuição**, clique em **usuários e grupos**. Selecione os usuários que você deseja que possam acessar este aplicativo VPN. Clique em **Selecionar**.

    ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/user4.png)