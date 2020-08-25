---
title: Adicionar uma conta corporativa ou de estudante ao aplicativo Microsoft Authenticator - Azure Active Directory
description: Adicione sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator para verificar sua identidade ao usar a verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e003c45aa1e7d75b709b7fbf99532fb1302fcbb9
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797642"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Adicionar sua conta corporativa ou de estudante ao aplicativo Microsoft Authenticator

Se a organização usa a verificação de dois fatores, você pode configurar sua conta corporativa ou de estudante para usar o aplicativo Microsoft Authenticator como um dos métodos de verificação.

>[!Important]
>Para poder adicionar sua conta, você deverá baixar e instalar o aplicativo Microsoft Authenticator. Se você ainda não tiver feito isso, siga as etapas no artigo [Baixar e instalar o aplicativo](user-help-auth-app-download-install.md).

## <a name="add-your-work-or-school-account"></a>Adicionar a conta corporativa ou de estudante

1. Em seu computador, vá para a página [Verificação de segurança adicional](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1).

    >[!Note]
    >Caso não veja a página **Verificação de segurança adicional**, é possível que o administrador tenha ativado a experiência de informações de segurança (versão prévia). Se esse for o caso, você deverá seguir as instruções da seção [Configurar informações de segurança para usar um aplicativo Authenticator](security-info-setup-auth-app.md). Se não for o caso, você precisará entrar em contato com o suporte técnico da sua organização para obter assistência. Para saber mais sobre as informações de segurança, consulte [Visão geral sobre as informações de segurança (versão prévia)](./security-info-setup-signin.md).

2. Marque a caixa ao lado de **aplicativo do autenticador** e selecione **Configurar**.

    A página **Configure o aplicativo móvel** é exibida.

    ![Tela que fornece o código QR](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Abra o aplicativo Microsoft Authenticator, selecione **Adicionar conta** por meio do ícone **Personalizar e controlar** no canto superior direito e, em seguida, selecione **conta corporativa ou de estudante**.

    >[!Note]
    >Se esta for a primeira vez que está configurando o aplicativo Microsoft Authenticator, você poderá receber um prompt perguntando se deseja permitir que o aplicativo acesse sua câmera (iOS) ou que o aplicativo tire fotos e grave vídeo (Android). Você precisa selecionar **Permitir** para que o aplicativo autenticador possa acessar sua câmera para tirar uma foto do código QR na próxima etapa. Se não permitir acesso da câmera, você ainda poderá configurar o aplicativo autenticador, mas precisará adicionar as informações de código manualmente. Para saber mais sobre como adicionar o código manualmente, confira [Adicionar uma conta manualmente ao aplicativo](user-help-auth-app-add-account-manual.md).

4. Use a câmera do dispositivo para digitalizar o código QR da tela **Configurar aplicativo móvel** em seu computador e, em seguida, escolha **Concluído**.

    >[!Note]
    >Se a câmera não conseguir capturar o código QR, você poderá adicionar manualmente as informações da conta ao aplicativo Microsoft Authenticator para verificação de dois fatores. Confira [Adicionar sua conta manualmente](user-help-auth-app-add-account-manual.md) para obter mais informações e saber como fazê-lo.

5. Examine a tela **Contas** do aplicativo em seu dispositivo, para garantir que sua conta está correta e que há um código de verificação de seis dígitos associado. Para segurança adicional, o código de verificação muda a cada 30 segundos, evitando que alguém use o mesmo código várias vezes.

    ![Tela de contas](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Próximas etapas

- Depois de adicionar as contas ao aplicativo, você poderá entrar usando o aplicativo Authenticator em seu dispositivo. Para obter mais informações, confira [Entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Para dispositivos que executam o iOS, você também pode fazer backup das credenciais da sua conta e das configurações de aplicativos relacionadas, tais como a ordem de suas contas, na nuvem. Para obter mais informações, confira [Fazer backup e recuperar informações com o aplicativo Microsoft Authenticator](user-help-auth-app-backup-recovery.md).