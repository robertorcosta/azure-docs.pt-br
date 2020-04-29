---
title: Configurar a autenticação multifator do Azure para área de trabalho virtual do Windows-Azure
description: Como configurar a autenticação multifator do Azure para aumentar a segurança na área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998468"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Configurar a Autenticação Multifator do Azure

O cliente do Windows para área de trabalho virtual do Windows é uma excelente opção para integrar a área de trabalho virtual do Windows com seu computador local. No entanto, quando você configura sua conta da área de trabalho virtual do Windows no cliente do Windows, há certas medidas que você precisará tomar para manter seus usuários seguros.

Quando você entra pela primeira vez, o cliente solicita seu nome de usuário, senha e MFA do Azure. Depois disso, na próxima vez que você entrar, o cliente lembrará seu token do seu aplicativo empresarial Azure Active Directory (AD). Quando você seleciona **lembrar**, os usuários podem entrar depois de reiniciar o cliente sem a necessidade de inserir novamente suas credenciais.

Embora a memorização de credenciais seja conveniente, ela também pode tornar implantações em cenários empresariais ou dispositivos pessoais menos seguros. Para proteger seus usuários, você precisará garantir que o cliente continue solicitando as credenciais da MFA (autenticação multifator) do Azure. Este artigo mostrará como configurar a política de acesso condicional para a área de trabalho virtual do Windows para habilitar essa configuração.

## <a name="prerequisites"></a>Pré-requisitos

Veja o que você precisará para começar:

- Atribua a todos os usuários uma das seguintes licenças:
  - Microsoft 365 E3 ou e5
  - Azure Active Directory Premium P1 ou P2
  - Enterprise Mobility + Security E3 ou e5
- Um grupo de Azure Active Directory com os usuários atribuídos como membros do grupo.
- Habilite o Azure MFA para todos os seus usuários. Para obter mais informações sobre como fazer isso, consulte [como exigir a verificação em duas etapas para um usuário](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

>[!NOTE]
>A configuração a seguir também se aplica ao [cliente Web da área de trabalho virtual do Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="opt-in-to-the-conditional-access-policy"></a>Aceitar a política de acesso condicional

1. Abra **Azure Active Directory**.

2. Vá para a guia **todos os aplicativos** . No menu suspenso "tipo de aplicativo", selecione **aplicativos empresariais**e, em seguida, pesquise **cliente de área de trabalho virtual do Windows**.

    ![Uma captura de tela da guia todos os aplicativos. O usuário inseriu "cliente de área de trabalho virtual do Windows" na barra de pesquisa e o aplicativo foi exibido nos resultados da pesquisa.](media/all-applications-search.png)

3. Selecione **acesso condicional**.

    ![Uma captura de tela mostrando o usuário passando o cursor do mouse sobre a guia acesso condicional.](media/conditional-access-location.png)

4. Selecione **+ nova política**.

   ![Uma captura de tela da página de acesso condicional. O usuário está focalizando o cursor do mouse sobre o botão nova política.](media/new-policy-button.png)

5. Insira um **nome** para a **regra**e, em seguida, **selecione** o * nome do **grupo** que você criou nos pré-requisitos.

6. Selecione **selecionar**e, em seguida, selecione **concluído**.

7. Em seguida, abra **aplicativos de nuvem ou ações**.

8. No painel **selecionar** , selecione o aplicativo **Windows Virtual Desktop** Enterprise.

    ![Uma captura de tela da página de aplicativos ou ações na nuvem. O usuário selecionou o aplicativo de área de trabalho virtual do Windows selecionando a marca de seleção ao lado dele. O aplicativo selecionado é realçado em vermelho.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >Você também deve ver o aplicativo cliente de área de trabalho virtual do Windows selecionado no lado esquerdo da tela, conforme mostrado na imagem a seguir. Você precisa dos aplicativos da área de trabalho virtual do Windows e do cliente empresarial do Windows para que a política funcione.
    >
    > ![Uma captura de tela da página de aplicativos ou ações na nuvem. Os aplicativos cliente de área de trabalho virtual do Windows e área de trabalho virtual do Windows são realçados em vermelho.](media/cloud-apps-enterprise-selected.png)

9. Selecione **selecionar**

10. Em seguida, abra **Grant** 

11. Selecione **exigir autenticação multifator**e, em seguida, selecione **exigir um dos controles selecionados**.
   
    ![Uma captura de tela da página de concessão. "Exigir autenticação multifator" está selecionado.](media/grant-page.png)

    >[!NOTE]
    >Se você tiver dispositivos registrados em MDM na sua organização e não quiser que eles mostrem o prompt do MFA, você também poderá selecionar **exigir que o dispositivo seja marcado como em conformidade**.

12. Selecione **sessão**.

13. Defina a **frequência de entrada** como **ativa**e, em seguida, altere seu valor para **1 hora**.

    ![Uma captura de tela da página da sessão. O menu de sessão mostra que os menus suspensos de frequência de entrada foram alterados para "1" e "horas".](media/sign-in-frequency.png)
   
    >[!NOTE]
    >As sessões ativas no ambiente de área de trabalho virtual do Windows continuarão funcionando conforme você alterar a política. No entanto, se você se desconectar ou sair, precisará fornecer suas credenciais novamente após 60 minutos. Conforme você altera as configurações, você pode estender o período de tempo limite tanto quanto desejar (desde que ele se alinhe com a política de segurança da sua organização).
    >
    >A configuração padrão é uma janela sem interrupção de 90 dias, o que significa que o cliente solicitará que os usuários entrem novamente quando tentarem acessar um recurso depois de estarem inativos em sua máquina por 90 dias ou mais.

14. Habilite a política.

15. Selecione **criar** para confirmar a política.

Tudo pronto! Fique à vontade para testar a política para garantir que sua lista de permissões funcione conforme o esperado.
