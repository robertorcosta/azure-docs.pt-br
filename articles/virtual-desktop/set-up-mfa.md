---
title: Configurar a autenticação multifatorial do Azure para o Windows Virtual Desktop - Azure
description: Como configurar a autenticação multifatorial do Azure para aumentar a segurança no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0b3c47e1bbe5efdc5ee303305e52a785a49d0c00
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586867"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Configurar a autenticação multifatorial do Azure

O cliente Windows para Windows Virtual Desktop é uma excelente opção para integrar o Windows Virtual Desktop com sua máquina local. No entanto, quando você configura sua conta do Windows Virtual Desktop no Cliente Windows, existem certas medidas que você precisará tomar para manter você e seus usuários seguros.

Quando você faz login pela primeira vez, o cliente pede seu nome de usuário, senha e MFA do Azure. Depois disso, na próxima vez que você fizer login, o cliente lembrará seu token do aplicativo corporativo Azure Active Directory (AD). Quando você selecionar **Lembre-se**de mim, seus usuários podem fazer login depois de reiniciar o cliente sem precisar reinserir suas credenciais.

Embora lembrar credenciais seja conveniente, ele também pode tornar as implantações em cenários Corporativos ou dispositivos pessoais menos seguras. Para proteger seus usuários, você precisará garantir que o cliente continue pedindo credenciais de Autenticação Multifatorial (MFA) do Azure. Este artigo mostrará como configurar a política de acesso condicional para o Windows Virtual Desktop para habilitar essa configuração.

## <a name="prerequisites"></a>Pré-requisitos

Aqui está o que você precisa para começar:

- Atribua a todos os seus usuários uma das seguintes licenças:
  - Microsoft 365 E3 ou E5
  - Azure Active Directory Premium P1 ou P2
  - Mobilidade Empresarial + Segurança E3 ou E5
- Um grupo de diretório ativo do Azure com seus usuários atribuídos como membros do grupo.
- Habilite o Azure MFA para todos os seus usuários. Para obter mais informações sobre como fazer isso, consulte [Como exigir verificação em duas etapas para um usuário](/active-directory/authentication/howto-mfa-userstates).

>[!NOTE]
>A configuração a seguir também se aplica ao [cliente web do Windows Virtual Desktop](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="opt-in-to-the-conditional-access-policy"></a>Opte pela política de Acesso Condicional

1. Abrir **diretório ativo do Azure**.

2. Vá para a guia **Todos os aplicativos.** No menu suspenso "Tipo de aplicativo", selecione **Aplicativos Corporativos**e procure o **Windows Virtual Desktop Client**.

    ![Uma captura de tela da guia Todos os aplicativos. O usuário inseriu o "cliente de desktop virtual do Windows" na barra de pesquisa, e o aplicativo apareceu nos resultados da pesquisa.](media/all-applications-search.png)

3. Selecione **Acesso Condicional**.

    ![Uma captura de tela mostrando o usuário pairando o cursor do mouse sobre a guia Acesso Condicional.](media/conditional-access-location.png)

4. Selecione **+ Nova política**.

   ![Uma captura de tela da página Acesso Condicional. O usuário está pairando o cursor do mouse sobre o novo botão de política.](media/new-policy-button.png)

5. Digite um **nome** para a **regra**e **selecione** o nome *do **grupo** que você criou nos pré-requisitos.

6. Selecione **Selecionar**e, em seguida, selecione **Feito**.

7. Em seguida, abra **aplicativos ou ações em nuvem.**

8. No painel **Selecionar,** selecione o aplicativo **Windows Virtual Desktop** Enterprise.

    ![Uma captura de tela da página de aplicativos ou ações da Cloud. O usuário selecionou o aplicativo Windows Virtual Desktop selecionando a marca de seleção ao lado. O aplicativo selecionado é destacado em vermelho.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >Você também deve ver o aplicativo Windows Virtual Desktop Client selecionado no lado esquerdo da tela, como mostrado na imagem a seguir. Você precisa dos aplicativos Windows Virtual Desktop e Windows Virtual Desktop Client Enterprise para que a política funcione.
    >
    > ![Uma captura de tela da página de aplicativos ou ações da Cloud. Os aplicativos Windows Virtual Desktop e Windows Virtual Desktop Client são destacados em vermelho.](media/cloud-apps-enterprise-selected.png)

9. Selecionar **Selecionar**

10. Em seguida, abra **Grant** 

11. Selecione **Exigir autenticação multifatorial**e, em seguida, **selecione Exigir um dos controles selecionados**.
   
    ![Uma captura de tela da página do Grant. "Exigir autenticação multifatorial" é selecionado.](media/grant-page.png)

    >[!NOTE]
    >Se você tiver dispositivos inscritos no MDM em sua organização e não quiser que eles mostrem o prompt do MFA, você também pode selecionar **Exigir que o dispositivo seja marcado como compatível**.

12. Selecionar **sessão**.

13. Defina a **freqüência de login** como **ativa**e altere seu valor para **1 Horas**.

    ![Uma captura de tela da página sessão. O menu da sessão mostra que os menus suspensos de freqüência de login foram alterados para "1" e "Horas".](media/sign-in-frequency.png)
   
    >[!NOTE]
    >As sessões ativas no ambiente de desktop virtual do Windows continuarão a funcionar à medida que você alterar a diretiva. No entanto, se você desconectar ou assinar, você precisará fornecer suas credenciais novamente após 60 minutos. À medida que você altera as configurações, você pode estender o período de tempo indeterminado o quanto quiser (desde que esteja alinhado com a política de segurança da sua organização).
    >
    >A configuração padrão é uma janela de rolamento de 90 dias, o que significa que o cliente pedirá aos usuários para fazer login novamente quando tentarem acessar um recurso depois de ficarem inativos em sua máquina por 90 dias ou mais.

14. Habilite a apólice.

15. Selecione **Criar** para confirmar a política.

Tudo pronto! Sinta-se livre para testar a política para garantir que sua lista de allow funcione como planejado.
