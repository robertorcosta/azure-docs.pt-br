---
title: 'Habilitar MFA para usuários VPN: autenticação do Azure AD'
description: Habilitar a autenticação multifator para usuários VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964679"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Habilitar a MFA (autenticação multifator) do Azure para usuários VPN

Se desejar que os usuários sejam solicitados a fornecer um segundo fator de autenticação antes de conceder acesso, você poderá configurar a MFA (autenticação multifator) do Azure por usuário ou aproveitar a MFA (autenticação multifator) por meio do [acesso condicional](../active-directory/conditional-access/overview.md) para um controle mais refinado. A configuração da autenticação multifator por usuário pode ser habilitada sem custo adicional, no entanto, ao habilitar a MFA por usuário, o usuário receberá uma solicitação para a autenticação de dois fatores em todos os aplicativos vinculados ao locatário do Azure AD. O acesso condicional permitirá um controle mais refinado sobre como um segundo fator deve ser promovido e pode permitir a atribuição de MFA somente a VPN e não a outros aplicativos vinculados ao locatário do Azure AD.

## <a name="enableauth"></a>Habilitar autenticação

1. Navegue até **Azure Active Directory-> aplicativos empresariais-> todos os aplicativos**.
2. Na página **aplicativos empresariais – todos os aplicativos** , selecione **VPN do Azure**.

   ![ID do Diretório](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Definir configurações de entrada

Na página **VPN do Azure – Propriedades** , defina as configurações de entrada.

1. Definir **habilitado para que os usuários entrem?** para **Sim**. Isso permite que todos os usuários no locatário do AD se conectem à VPN com êxito.
2. Definir **atribuição de usuário necessária?** para **Sim** se você quiser limitar a entrada somente a usuários que têm permissões para a VPN do Azure.
3. Salve suas alterações.

   ![Permissões](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>Opção 1-habilitar a MFA (autenticação multifator) por meio do acesso condicional

O acesso condicional permite o controle de acesso refinado em uma base por aplicativo.  Observe que, para aproveitar o acesso condicional, você deve ter um licenciamento Azure AD Premium 1 ou superior aplicado aos usuários que estarão sujeitos às regras de acesso condicional.

1. Na página **aplicativos empresariais – todos os aplicativos** , selecione **VPN do Azure**, selecione **acesso condicional**e clique em **nova política**.
2. Em usuários e grupos, na guia *incluir* , marque **Selecionar usuários e grupos**, verificar **usuários e grupos**e selecione um grupo ou conjunto de usuários que devem estar sujeitos à MFA.  Clique em **Concluído**.
![Atribuições](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. Em **conceder**, marque **conceder acesso**, marque **exigir autenticação multifator**, marque **exigir todos os controles selecionados**e clique no botão **selecionar** .
![conceder acesso-MFA](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. Verifique **em** **habilitar política** e clique no botão **criar** .
![habilitar](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png) de política

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>Opção 2-habilitar a MFA (autenticação multifator) por usuário

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>Próximos passos

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para conexões VPN P2S](openvpn-azure-ad-client.md).
