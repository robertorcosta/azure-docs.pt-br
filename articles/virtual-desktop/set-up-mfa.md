---
title: Configurar a autenticação multifator do Azure para área de trabalho virtual do Windows-Azure
description: Como configurar a autenticação multifator do Azure para aumentar a segurança na área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8a8f5cb792f524354754b4368c0b68d5f9d40699
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291380"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Habilitar a Autenticação Multifator do Azure para a Área de Trabalho Virtual do Windows

>[!IMPORTANT]
> Se você estiver visitando esta página na documentação da área de trabalho virtual do Windows (clássico), certifique-se de [retornar à documentação da área de trabalho virtual do Windows (clássica)](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) quando tiver terminado.

O cliente do Windows para área de trabalho virtual do Windows é uma excelente opção para integrar a área de trabalho virtual do Windows com seu computador local. No entanto, quando você configura sua conta da área de trabalho virtual do Windows no cliente do Windows, há certas medidas que você precisará tomar para manter seus usuários seguros.

Quando você entra pela primeira vez, o cliente solicita seu nome de usuário, senha e MFA do Azure. Depois disso, na próxima vez que você entrar, o cliente lembrará seu token do seu aplicativo empresarial Azure Active Directory (AD). Quando você seleciona **lembrar**, os usuários podem entrar depois de reiniciar o cliente sem a necessidade de inserir novamente suas credenciais.

Embora a memorização de credenciais seja conveniente, ela também pode tornar implantações em cenários empresariais ou dispositivos pessoais menos seguros. Para proteger seus usuários, você precisará garantir que o cliente continue solicitando as credenciais da MFA (autenticação multifator) do Azure. Este artigo mostrará como configurar a política de acesso condicional para a área de trabalho virtual do Windows para habilitar essa configuração.

## <a name="prerequisites"></a>Pré-requisitos

Veja o que você precisará para começar:

- Atribua aos usuários uma licença que inclui Azure Active Directory Premium P1 ou P2.
- Um grupo de Azure Active Directory com os usuários atribuídos como membros do grupo.
- Habilite o Azure MFA para todos os seus usuários. Para obter mais informações sobre como fazer isso, consulte [como exigir a verificação em duas etapas para um usuário](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> A configuração a seguir também se aplica ao [cliente Web da área de trabalho virtual do Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

Veja como criar uma política de acesso condicional que requer autenticação multifator ao se conectar à área de trabalho virtual do Windows:

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
2. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
3. Selecione **Nova política**.
4. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
5. Em **Atribuições**, selecione **Usuários e grupos**.
6. Em **incluir**, selecione **Selecionar usuários e grupos**  >  **usuários e grupos** > escolha o grupo criado no estágio [pré-requisitos](#prerequisites) .
7. Selecione **Concluído**.
8. Em **aplicativos de nuvem ou ações**  >  **incluem**, selecione **selecionar aplicativos**.
9. Selecione um dos seguintes grupos de aplicativos com base em qual versão da área de trabalho virtual do Windows você está usando.
   - Se você estiver usando a área de trabalho virtual do Windows (clássico), escolha estes dois aplicativos:
       - **Área de trabalho virtual do Windows** (ID do aplicativo 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Cliente de área de trabalho virtual do Windows** (ID do aplicativo fa4345a4-A730-4230-84a8-7d9651b86739)
   - Se você estiver usando a área de trabalho virtual do Windows, escolha esses dois aplicativos:
       -  **Área de trabalho virtual do Windows** (ID do aplicativo 9cdead84-A844-4324-93f2-b2e6bb768d07)
       -  **Cliente de área de trabalho virtual do Windows** (ID do aplicativo a85cf173-4192-42F8-81fa-777a763e6e2c)

   >[!IMPORTANT]
   > Os aplicativos cliente de área de trabalho virtual do Windows são usados para o cliente Web. No entanto, não selecione o aplicativo chamado provedor de Azure Resource Manager de área de trabalho virtual do Windows (50e95039-B200-4007-bc97-8d5790743a63). Este aplicativo é usado somente para recuperar o feed do usuário e não deve ter MFA.
  
1. Depois de selecionar seu aplicativo, escolha **selecionar**e, em seguida, selecione **concluído**.

   > [!div class="mx-imgBorder"]
   > ![Uma captura de tela da página de aplicativos ou ações na nuvem. Os aplicativos cliente de área de trabalho virtual do Windows e área de trabalho virtual do Windows são realçados em vermelho.](media/cloud-apps-enterprise.png)

   >[!NOTE]
   >Para localizar a ID do aplicativo que você deseja selecionar, vá para **aplicativos empresariais** e selecione aplicativos da **Microsoft** no menu suspenso tipo de aplicativo.

10. Em **Access controls**  >  **concessão**de controles de acesso, selecione **conceder acesso**, **exigir autenticação multifator**e, em seguida, **selecione**.
11. Em **Access controls**  >  **sessão**de controles de acesso, selecione **frequência de entrada**, defina o valor como **1** e a unidade para **horas**e selecione **selecionar**.
12. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
13. Selecione **criar** para habilitar sua política.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre as políticas de acesso condicional](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Saiba mais sobre a frequência de entrada do usuário](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
