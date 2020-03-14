---
title: Altere o método de verificação de dois fatores e as configurações-Azure Active Directory
description: Saiba como alterar o método e as configurações de verificação de segurança para sua conta corporativa ou de estudante, na página de verificação de segurança adicional.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.openlocfilehash: 5949f04ecc28a88e340a9c2de530031793f193a0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253241"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Alterar o método e as configurações de verificação de dois fatores

Depois de configurar seus métodos de verificação de segurança para sua conta corporativa ou de estudante, você pode atualizar qualquer um dos detalhes relacionados, incluindo:

- Método de verificação de segurança padrão

- Detalhes do método de verificação de segurança, como seu número de telefone

- Instalação do aplicativo autenticador ou exclusão de um dispositivo do aplicativo autenticador

## <a name="using-the-additional-security-verification-page"></a>Usando a página de verificação de segurança adicional

Se sua organização forneceu etapas específicas sobre como ativar e gerenciar sua verificação de dois fatores, você deve seguir essas instruções primeiro. Caso contrário, você pode obter as configurações do método de verificação de segurança na página de [verificação de segurança adicional](https://docs.microsoft.com/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time) .

>[!Note]
>Se o que você está vendo na tela não corresponde ao que está sendo abordado neste artigo, significa que o administrador ativou a experiência de **informações de segurança (versão prévia)** ou que sua organização forneceu seu próprio portal personalizado. Para obter mais informações sobre a nova experiência de informações de segurança, consulte [visão geral de informações de segurança (versão prévia)](user-help-security-info-overview.md). Para obter mais informações sobre o portal personalizado de sua organização, você deve entrar em contato com o suporte técnico da sua organização.

### <a name="to-get-to-the-additional-security-verification-page"></a>Para acessar a página de verificação de segurança adicional

Você pode seguir este link para a [página de verificação de segurança adicional](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1).

![Página de verificação de segurança adicional, com os detalhes do método de verificação de segurança disponível](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

Você também pode acessar a página de **verificação de segurança adicional** seguindo estas etapas:

1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Selecione o nome da conta no canto superior direito e selecione **perfil**.

1. Escolha **Verificação de segurança adicional**.  

    ![Link meus aplicativos para a página de verificação de segurança adicional](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Para obter informações sobre como usar a seção de **senhas de aplicativo** de uma página de **verificação de segurança adicional** , consulte [gerenciar senhas de aplicativo para verificação de dois fatores](multi-factor-authentication-end-user-app-passwords.md). As senhas de aplicativo devem ser usadas somente para aplicativos que não dão suporte à verificação de dois fatores.

## <a name="change-your-default-security-verification-method"></a>Alterar o método de verificação de segurança padrão

Depois de entrar em sua conta corporativa ou de estudante com seu nome de usuário e senha, você será automaticamente apresentado com o método de verificação de segurança escolhido. Dependendo dos requisitos da sua organização, isso pode ser um código de notificação ou verificação por meio de um aplicativo autenticador, uma mensagem de texto ou uma chamada telefônica.

Se você decidir que deseja alterar o método de verificação de segurança padrão que está usando, poderá fazer isso aqui.

### <a name="to-change-your-default-security-verification-method"></a>Para alterar o método de verificação de segurança padrão

1. Na página **verificação de segurança adicional** , selecione o método a ser usado na lista de **Opções qual é sua preferência** . Você verá todas as opções, mas poderá selecionar apenas aquelas disponibilizadas para você pela sua organização.

    - **Notificar-me por meio do aplicativo**: você será notificado pelo aplicativo autenticador de que você tem um prompt de verificação em espera.

    - **Ligar para meu telefone de autenticação**: você receberá uma chamada telefônica em seu dispositivo móvel, solicitando que você verifique suas informações.

    - **Código de texto para meu telefone de autenticação**: você obterá um código de verificação como parte de uma mensagem de texto em seu dispositivo móvel. Você deve inserir esse código no prompt de verificação para sua conta corporativa ou de estudante.

    - **Ligue para o meu telefone comercial**: você receberá um telefonema em seu telefone comercial, solicitando que você verifique suas informações.

    - **Usar código de verificação do aplicativo**: você usará seu aplicativo autenticador para obter um código de verificação que digitará no prompt de sua conta corporativa ou de estudante.

2. Clique em **Salvar**.

## <a name="add-or-change-your-phone-number"></a>Adicionar ou alterar seu número de telefone

Você pode adicionar novos números de telefone ou atualizar números existentes, na página de **verificação de segurança adicional** .

>[!Important]
>É altamente recomendável que você adicione um número de telefone secundário para ajudar a evitar que ele seja bloqueado se o seu telefone principal for perdido ou roubado, ou se você receber um novo telefone e não tiver mais seu número de telefone principal original.

### <a name="to-change-your-phone-numbers"></a>Para alterar seus números de telefone

1. Na seção **como você gostaria de responder?** da página de **verificação de segurança adicional** , atualize as informações de número de telefone do seu **telefone de autenticação** (seu dispositivo móvel primário) e seu **telefone comercial**.

1. Selecione a caixa ao lado da opção **telefone de autenticação alternativo** e digite um número de telefone secundário no qual você poderá receber mensagens de texto ou chamadas telefônicas se não puder acessar o dispositivo primário.

1. Clique em **Salvar**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Adicionar uma nova conta ao aplicativo Microsoft Authenticator

Você pode configurar sua conta corporativa ou de estudante no aplicativo Microsoft Authenticator para [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) ou [Ios](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Se você já tiver configurado sua conta corporativa ou de estudante no aplicativo Microsoft Authenticator, não será necessário fazer isso novamente.

1. Na seção **como você deseja responder?** da página de verificação de **segurança adicional** , selecione **Configurar aplicativo autenticador**.

    ![Configurar sua conta corporativa ou de estudante no aplicativo Microsoft Authenticator](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Siga as instruções na tela, incluindo o uso de seu dispositivo móvel para digitalizar o código QR e, em seguida, selecione **Avançar**.

    Você será solicitado a aprovar uma notificação por meio do aplicativo Microsoft Authenticator, para verificar suas informações.

1. Clique em **Salvar**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Excluir sua conta ou dispositivo do aplicativo Microsoft Authenticator

Você pode excluir sua conta do aplicativo Microsoft Authenticator e pode excluir seu dispositivo de sua conta corporativa ou de estudante. Normalmente, você exclui o dispositivo para remover permanentemente um dispositivo perdido, roubado ou antigo de sua conta e exclui sua conta para tentar corrigir alguns problemas de conexão ou para resolver uma alteração de conta, como um novo nome de usuário.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Para excluir seu dispositivo de sua conta corporativa ou de estudante

1. Na seção **como você deseja responder?** da página de verificação de **segurança adicional** , selecione o botão **Configurar aplicativo autenticador** .

1. Clique em **Salvar**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Para excluir sua conta do aplicativo Microsoft Authenticator

No aplicativo Microsoft Authenticator, selecione o botão **excluir** ao lado do dispositivo que você deseja excluir.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Ativar prompts de verificação de dois fatores em um dispositivo confiável

Dependendo das configurações da sua organização, você poderá ver uma caixa de seleção que diz **não perguntar novamente por X dias** quando executar a verificação de dois fatores no navegador. Se você tiver selecionado essa opção para interromper os prompts de verificação de dois fatores e perder o dispositivo ou se o dispositivo estiver potencialmente comprometido, você deverá ativar os prompts de verificação de dois fatores novamente para ajudar a proteger sua conta. Você deve ativar os prompts para todos os seus dispositivos ao mesmo tempo. Infelizmente, você não pode ativar os prompts novamente para apenas um dispositivo específico.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Para ativar novamente os prompts de verificação de dois fatores para seus dispositivos

Na página [ **verificação de segurança adicional** ](#to-get-to-the-additional-security-verification-page), selecione **restaurar autenticação multifator em dispositivos confiáveis anteriormente**. Na próxima vez que você entrar em qualquer dispositivo, será solicitado que você execute a verificação de dois fatores.

## <a name="next-steps"></a>Próximas etapas

Depois de adicionar ou atualizar suas configurações de verificação de dois fatores, você pode gerenciar suas senhas de aplicativo, entrar ou obter ajuda com alguns problemas comuns relacionados à verificação de dois fatores.

- [Gerencie senhas de aplicativo para verificação de dois fatores](multi-factor-authentication-end-user-app-passwords.md) para qualquer aplicativo que não ofereça suporte à verificação de dois fatores.

- [Como entrar usando a verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Resolver problemas comuns com a verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)
