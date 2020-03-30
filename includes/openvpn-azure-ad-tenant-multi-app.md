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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77485552"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Crie o inquilino Azure AD

Criar um inquilino Azure AD usando as etapas do Criar um novo artigo [de inquilino:](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Nome organizacional
* Nome de domínio inicial

  Exemplo:

   ![Novo inquilino azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Criar usuários inquilinos

Nesta etapa, você cria dois usuários inquilinos do Azure AD: uma conta de administração global e uma conta de usuário mestre. A conta de usuário mestre é usada como sua conta de incorporação mestre (conta de serviço). Quando você cria uma conta de usuário de inquilino Azure AD, você ajusta a função Diretório para o tipo de usuário que deseja criar. Use as etapas [deste artigo](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois usuários para o seu inquilino Azure AD. Certifique-se de alterar a **função diretório** para criar os tipos de conta:

* Administrador global
* Usuário

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Registre o Cliente VPN

Registre o cliente VPN no inquilino Azure AD.

1. Localize o ID do diretório do diretório que você deseja usar para autenticação. Ele está listado na seção propriedades da página Active Directory.

    ![ID do Diretório](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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

    ![ID do Diretório](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Selecione **Aceitar** quando solicitado.

    ![Aceitar](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Em seu Azure AD, em **aplicativos Corporativos,** você verá **o Azure VPN** listado.

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Registre aplicações adicionais

Nesta etapa, você registra aplicativos adicionais para vários usuários e grupos.

1. Em seu Diretório Ativo do Azure, clique em **registros do aplicativo** **e, em seguida, + Novo registro**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Na página Registrar uma página **de aplicativo,** digite o **Nome**. Selecione os **tipos de conta suportados desejados**e clique em **Registrar**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Uma vez que o novo aplicativo tenha sido registrado, clique **em Expor uma API** a lâmina do aplicativo.

4. Clique **+ Adicionar um escopo**.

5. Deixe o URI de **id**de aplicativo padrão . Clique em **Salvar e continuar**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Preencha os campos necessários e **certifique-se de** que o Estado está **habilitado**. Clique **em Adicionar escopo**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Clique **em Expor uma API** e então + Adicionar um aplicativo **cliente**.  Para **ID do cliente,** digite os seguintes valores dependendo da nuvem:

    - Digite **41b23e61-6c1e-4545-b367-cd054e0ed4b4** para **Public** Público Azure
    - Digite **51bb15d4-3a4f-4ebf-9dca-40096fe32426** para **governo azure**
    - Digite **538ee9e6-310a-468d-afef-ea97365856a9** para Azure **Alemanha**
    - Digite **49f817b6-84ae-4cc0-928c-73f27289b3aa** para Azure **China 21Vianet**

8. Clique **em Adicionar aplicativo**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Copie o ID do **aplicativo (cliente)** da página **Visão Geral.** Você precisará dessas informações para configurar seus gateways VPN.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Repita as etapas nesta seção [de aplicativos adicionais](#register-apps) para criar o máximo de aplicativos necessários para sua exigência de segurança. Cada aplicativo será associado a um gateway VPN e poderá ter um conjunto diferente de usuários. Apenas um aplicativo pode ser associado a um gateway.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Atribuir usuários a aplicativos

Atribua os usuários aos seus aplicativos.

1. Em **aplicativos Azure AD -> Enterprise,** selecione o aplicativo recém-registrado e clique **em Propriedades**. Certifique-se de que a **yes** **atribuição de usuário necessária?** Clique em **Salvar**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na página do aplicativo, clique **em Usuários e grupos**e clique em **+Adicionar usuário**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. Em **Adicionar atribuição,** clique **em Usuários e grupos**. Selecione os usuários que deseja acessar este aplicativo VPN. Clique em **Selecionar**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)