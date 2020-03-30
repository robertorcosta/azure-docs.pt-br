---
title: Altere seu método de verificação de dois fatores e configurações - Azure Active Directory
description: Saiba como alterar o método de verificação de segurança e as configurações da sua conta de trabalho ou escola, a partir da página de verificação de segurança adicional.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253241"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Altere seu método de verificação de dois fatores e configurações

Depois de configurar seus métodos de verificação de segurança para sua conta de trabalho ou escola, você pode atualizar qualquer um dos detalhes relacionados, incluindo:

- Método de verificação de segurança padrão

- Detalhes do método de verificação de segurança, como seu número de telefone

- Configuração do aplicativo autenticador ou exclusão de um dispositivo do aplicativo autenticador

## <a name="using-the-additional-security-verification-page"></a>Usando a página de verificação de segurança adicional

Se sua organização forneceu passos específicos sobre como ativar e gerenciar sua verificação de dois fatores, você deve seguir essas instruções primeiro. Caso contrário, você pode chegar às configurações do método de verificação de segurança a partir da página [de verificação de segurança adicional.](https://docs.microsoft.com/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time)

>[!Note]
>Se o que você está vendo na sua tela não corresponde ao que está sendo coberto neste artigo, significa que o administrador ativou a experiência **de informações de segurança (visualização)** ou que sua organização forneceu seu próprio portal personalizado. Para obter mais informações sobre a nova experiência de informações de segurança, consulte [visão geral das informações de segurança (visualização).](user-help-security-info-overview.md) Para obter mais informações sobre o portal personalizado da sua organização, você deve entrar em contato com o help desk da sua organização.

### <a name="to-get-to-the-additional-security-verification-page"></a>Para chegar à página de verificação de segurança adicional

Você pode seguir este link para a [página de verificação de segurança adicional](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1).

![Página de verificação de segurança adicional, com os detalhes disponíveis do método de verificação de segurança](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

Você também pode acessar a página **de verificação de segurança adicional** seguindo estas etapas:

1. Faça login [https://myapps.microsoft.com](https://myapps.microsoft.com)em .

1. Selecione o nome da conta no canto superior direito e selecione **perfil**.

1. Selecione **Verificação de segurança adicional**.  

    ![Link meus aplicativos para a página de verificação de segurança adicional](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Para obter informações sobre o uso da seção **senhas** do aplicativo da página **de verificação de segurança adicional,** consulte [Gerenciar senhas do aplicativo para verificação de dois fatores](multi-factor-authentication-end-user-app-passwords.md). As senhas dos aplicativos devem ser usadas apenas para aplicativos que não suportam verificação de dois fatores.

## <a name="change-your-default-security-verification-method"></a>Altere seu método de verificação de segurança padrão

Depois de entrar na sua conta de trabalho ou escola com seu nome de usuário e senha, você será automaticamente apresentado com o método de verificação de segurança escolhido. Dependendo dos requisitos da sua organização, isso pode ser um código de notificação ou verificação através de um aplicativo autenticador, uma mensagem de texto ou uma chamada telefônica.

Se você decidir que deseja alterar o método padrão de verificação de segurança que você está usando, você pode fazê-lo a partir daqui.

### <a name="to-change-your-default-security-verification-method"></a>Para alterar seu método de verificação de segurança padrão

1. Na página **de verificação de segurança adicional,** selecione o método a ser usado na lista **de opções preferidas.** Você verá todas as opções, mas você pode selecionar apenas as que são disponibilizadas para você pela sua organização.

    - **Notifique-me através do aplicativo**: Você será notificado através do seu aplicativo autenticador que você tem um prompt de verificação de espera.

    - **Ligue para o meu telefone de autenticação:** Você receberá uma chamada telefônica no seu dispositivo móvel, pedindo que você verifique suas informações.

    - **Código de texto para o meu telefone de autenticação**: Você receberá um código de verificação como parte de uma mensagem de texto em seu dispositivo móvel. Você deve inserir este código no prompt de verificação para sua conta de trabalho ou escola.

    - **Ligue para o telefone**do meu escritório: Você receberá uma ligação no seu telefone do escritório, pedindo que você verifique suas informações.

    - **Use o código de verificação do aplicativo**: Você usará seu aplicativo autenticador para obter um código de verificação que você digitará no prompt de sua conta de trabalho ou escola.

2. Selecione **Salvar**.

## <a name="add-or-change-your-phone-number"></a>Adicione ou altere seu número de telefone

Você pode adicionar novos números de telefone ou atualizar números existentes a partir da página **de verificação de segurança adicional.**

>[!Important]
>Recomendamos fortemente que você adicione um número de telefone secundário para ajudar a evitar ser bloqueado fora de sua conta se seu telefone principal for perdido ou roubado, ou se você conseguir um telefone novo e não tiver mais seu número de telefone original e principal.

### <a name="to-change-your-phone-numbers"></a>Para alterar seus números de telefone

1. A partir da **seção Como você gostaria de responder?** seção da página de verificação de **segurança adicional,** atualize as informações do número de telefone para o seu **telefone de autenticação** (seu dispositivo móvel principal) e seu **telefone Office**.

1. Selecione a caixa ao lado da opção **telefone de autenticação alternativa** e digite um número de telefone secundário onde você pode receber mensagens de texto ou chamadas telefônicas se não puder acessar seu dispositivo principal.

1. Selecione **Salvar**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Adicione uma nova conta ao aplicativo autenticador da Microsoft

Você pode configurar sua conta de trabalho ou escola no aplicativo Microsoft Authenticator para [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) ou [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Se você já configurou sua conta de trabalho ou escola no aplicativo Microsoft Authenticator, você não precisa fazê-lo novamente.

1. A partir da **seção Como você gostaria de responder?** seção da página de verificação de **segurança adicional,** **selecione Configuração do aplicativo Authenticator**.

    ![Configure sua conta de trabalho ou escola no aplicativo Microsoft Authenticator](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Siga as instruções na tela, incluindo o uso do dispositivo móvel para escanear o código QR e, em seguida, selecione **Next**.

    Você será solicitado a aprovar uma notificação através do aplicativo Microsoft Authenticator, para verificar suas informações.

1. Selecione **Salvar**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Exclua sua conta ou dispositivo do aplicativo Microsoft Authenticator

Você pode excluir sua conta do aplicativo Microsoft Authenticator e excluir seu dispositivo da sua conta de trabalho ou escola. Normalmente, você exclui seu dispositivo para remover permanentemente um dispositivo perdido, roubado ou antigo de sua conta, e exclui sua conta para tentar corrigir alguns problemas de conexão ou para resolver uma alteração de conta, como um novo nome de usuário.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Para excluir seu dispositivo de sua conta de trabalho ou escola

1. A partir da **seção Como você gostaria de responder?** seção da página de verificação de **segurança adicional,** selecione o botão **Configuração do aplicativo Autenticador.**

1. Selecione **Salvar**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Para excluir sua conta do aplicativo Microsoft Authenticator

No aplicativo Microsoft Authenticator, selecione o botão **Excluir** ao lado do dispositivo que deseja excluir.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Ativar solicitações de verificação de dois fatores em um dispositivo confiável

Dependendo das configurações da organização, você pode ver uma caixa de seleção que diz **Não pergunte novamente por x dias** quando você executar a verificação de dois fatores no seu navegador. Se você selecionou essa opção para interromper solicitações de verificação de dois fatores e, em seguida, você perder seu dispositivo ou seu dispositivo está potencialmente comprometido, você deve ativar solicitações de verificação de dois fatores de volta para ajudar a proteger sua conta. Você deve ligar os pedidos para todos os seus dispositivos ao mesmo tempo. Infelizmente, você não pode ligar os prompts de volta para apenas um dispositivo específico.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Para ativar solicitações de verificação de dois fatores para seus dispositivos

Na [ **página de verificação de segurança adicional,** ](#to-get-to-the-additional-security-verification-page)selecione **Restaurar autenticação multifatorial em dispositivos anteriormente confiáveis**. Da próxima vez que fizer login em qualquer dispositivo, você será solicitado a realizar a verificação de dois fatores.

## <a name="next-steps"></a>Próximas etapas

Depois de adicionar ou atualizar suas configurações de verificação de dois fatores, você pode gerenciar suas senhas de aplicativo, fazer login ou obter ajuda com alguns problemas comuns relacionados à verificação de dois fatores.

- [Gerencie senhas de aplicativos para verificação de dois fatores](multi-factor-authentication-end-user-app-passwords.md) para quaisquer aplicativos que não suportam verificação de dois fatores.

- [Como fazer login usando verificação de dois fatores](multi-factor-authentication-end-user-signin.md)

- [Resolver problemas comuns com verificação de dois fatores](multi-factor-authentication-end-user-troubleshoot.md)
