---
title: Login de usuário baseado em SMS para o Azure Active Directory
description: Saiba como configurar e habilitar os usuários a fazer login no Azure Active Directory usando SMS (visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: b199854867c48906061cff65bad21e514456c911
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378698"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Configure e habilite os usuários para autenticação baseada em SMS usando o Azure Active Directory (visualização)

Para reduzir a complexidade e os riscos de segurança para os usuários entrarem em aplicativos e serviços, o Azure Active Directory (Azure AD) oferece várias opções de autenticação. A autenticação baseada em SMS, atualmente em visualização, permite que os usuários entrem sem precisar fornecer, ou mesmo saber, seu nome de usuário e senha. Depois que sua conta é criada por um administrador de identidade, eles podem inserir seu número de telefone no prompt de login e fornecer um código de autenticação enviado a eles por mensagem de texto. Este método de autenticação simplifica o acesso a aplicativos e serviços, especialmente para trabalhadores da linha de frente.

Este artigo mostra como ativar a autenticação baseada em SMS para usuários ou grupos selecionados no Azure AD.

|     |
| --- |
| A autenticação baseada em SMS para usuários é um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre visualizações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Active Directory associado à sua assinatura.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Você precisa de privilégios *de administrador global* no seu inquilino Azure AD para habilitar a autenticação baseada em SMS.
* Cada usuário habilitado na política do método de autenticação de mensagens de texto deve ser licenciado, mesmo que não o use. Cada usuário habilitado deve ter uma das seguintes licenças Azure AD ou Microsoft 365:
    * [O Azure AD Premium P1 ou P2][azuread-licensing]
    * [Microsoft 365 F1 ou F3][m365-firstline-workers-licensing]

## <a name="limitations"></a>Limitações

Durante a visualização pública da autenticação baseada em SMS, as seguintes limitações se aplicam:

* A autenticação baseada em SMS não é compatível atualmente com a autenticação multifatorial do Azure.
* Com exceção das Equipes, a autenticação baseada em SMS não é atualmente compatível com aplicativos nativos do Office.
* A autenticação baseada em SMS não é recomendada para contas B2B.
* Os usuários federados não autenticam no inquilino da casa. Eles só autenticam na nuvem.

## <a name="enable-the-sms-based-authentication-method"></a>Habilite o método de autenticação baseado em SMS

Existem três etapas principais para habilitar e usar a autenticação baseada em SMS em sua organização:

* Habilite a política do método de autenticação.
* Selecione usuários ou grupos que podem usar o método de autenticação baseado em SMS.
* Atribua um número de telefone para cada conta de usuário.
    * Este número de telefone pode ser atribuído no portal Azure (que é mostrado neste artigo), e no *Meu Pessoal* ou *no Meu Perfil*.

Primeiro, vamos habilitar a autenticação baseada em SMS para o seu inquilino Azure AD.

1. Faça login no [portal Azure][azure-portal] como *administrador global.*
1. Pesquise **Azure Active Directory** e selecione-o.
1. No menu de navegação no lado esquerdo da janela Diretório Ativo do Azure, selecione **os métodos de autenticação de segurança > > política do método de autenticação (visualização)**.

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Na lista de métodos de autenticação disponíveis, selecione **Mensagem de texto**.
1. Definir **Habilitar** para *Sim*.

    ![Habilite a autenticação de texto na janela de diretiva do método de autenticação](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Você pode optar por habilitar a autenticação baseada em SMS para *todos os usuários* ou selecionar *usuários* e grupos. Na próxima seção, você habilita a autenticação baseada em SMS para um usuário de teste.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Atribuir o método de autenticação a usuários e grupos

Com a autenticação baseada em SMS habilitada no seu inquilino Azure AD, agora selecione alguns usuários ou grupos para poder usar esse método de autenticação.

1. Na janela de diretiva de autenticação de mensagens de texto, defina **'''''Selecionar** *usuários'.*
1. Escolha **adicionar usuários ou grupos,** em seguida, selecione um usuário de teste ou grupo, como *Usuário Contoso* ou *Usuários Contoso SMS*.

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Quando você tiver selecionado seus usuários ou grupos, escolha **Selecionar**e **salvar** a diretiva de método de autenticação atualizada.

Cada usuário habilitado na política do método de autenticação de mensagens de texto deve ser licenciado, mesmo que não o use. Certifique-se de ter as licenças apropriadas para os usuários habilitados na diretiva do método de autenticação, especialmente quando você habilita o recurso para grandes grupos de usuários.

## <a name="set-a-phone-number-for-user-accounts"></a>Defina um número de telefone para contas de usuário

Os usuários agora estão habilitados para autenticação baseada em SMS, mas seu número de telefone deve ser associado ao perfil do usuário no Azure AD antes de entrar. O usuário pode [definir este número de telefone](../user-help/sms-sign-in-explainer.md) em meu *perfil,* ou você pode atribuir o número de telefone usando o portal Azure. Os números de telefone podem ser definidos por *admins globais,* *admins de autenticação*ou *admins de autenticação privilegiadas.*

Quando um número de telefone é definido para sms-sign, ele também está disponível para uso com [autenticação multifatorial do Azure][tutorial-azure-mfa] e [redefinição de senha de autoatendimento][tutorial-sspr].

1. Pesquise **Azure Active Directory** e selecione-o.
1. No menu de navegação no lado esquerdo da janela Diretório ativo do Azure, selecione **Usuários**.
1. Selecione o usuário habilitado para autenticação baseada em SMS na seção anterior, como *Usuário Contoso,* e selecione **Métodos de Autenticação**.
1. Digite o número de telefone do usuário, incluindo o código do país, como *+1 xxxxxxxxx*. O portal Azure valida que o número de telefone está no formato correto.

    ![Defina um número de telefone para um usuário no portal Azure para usar com autenticação baseada em SMS](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    O número de telefone deve ser único em seu inquilino. Se você tentar usar o mesmo número de telefone para vários usuários, uma mensagem de erro será mostrada.

1. Para aplicar o número de telefone na conta de um usuário, **selecione Salvar**.

Quando provisionado com sucesso, uma marca de seleção é exibida para *sMS Login ativado*.

## <a name="test-sms-based-sign-in"></a>Teste o login baseado em SMS

Para testar a conta de usuário agora habilitada para login baseado em SMS, complete as seguintes etapas:

1. Abra uma nova janela do navegador InPrivate ou Incognito para[https://www.office.com][office]
1. No canto superior direito, selecione **Entrar .**
1. No prompt de login, digite o número de telefone associado ao usuário na seção anterior e selecione **Next**.

    ![Digite um número de telefone no prompt de login para o usuário de teste](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Uma mensagem de texto é enviada para o número de telefone fornecido. Para concluir o processo de login, digite o código de 6 dígitos fornecido na mensagem de texto no prompt de login.

    ![Digite o código de confirmação enviado via mensagem de texto para o número de telefone do usuário](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. O usuário agora está conectado sem a necessidade de fornecer um nome de usuário ou senha.

## <a name="troubleshoot-sms-based-sign-in"></a>Solucionar problemas de login baseado em SMS

Os seguintes cenários e etapas de solução de problemas podem ser usados se você tiver problemas com a ativação e o uso de login baseado em SMS.

### <a name="phone-number-already-set-for-a-user-account"></a>Número de telefone já definido para uma conta de usuário

Se um usuário já se cadastrou para o Azure Multi-Factor Authentication e/ou self-service password reset (SSPR), ele já tem um número de telefone associado à sua conta. Este número de telefone não está disponível automaticamente para uso com login baseado em SMS.

Um usuário que tenha um número de telefone já definido para sua conta é exibido um botão *para habilitar o login de SMS em* sua página **Meu Perfil.** Selecione este botão e a conta está habilitada para uso com login baseado em SMS e o registro anterior de Autenticação Multifatorial do Azure ou SSPR.

Para obter mais informações sobre a experiência do usuário final, consulte a [experiência do usuário de login do SMS para obter número de telefone (visualização)](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Erro ao tentar definir um número de telefone na conta de um usuário

Se você receber um erro ao tentar definir um número de telefone para uma conta de usuário no portal Azure, revise as seguintes etapas de solução de problemas:

1. Certifique-se de que você está habilitado para a pré-visualização de login baseada em SMS.
1. Confirme se a conta de usuário está ativada na diretiva método de autenticação de mensagens de *texto.*
1. Certifique-se de definir o número de telefone com a formatação adequada, conforme validado no portal Azure (como *+1 4251234567*).
1. Certifique-se de que o número de telefone não seja usado em outro lugar do seu inquilino.
1. Verifique se não há número de voz definido na conta. Se um número de voz estiver definido, exclua e tente ligar novamente para o número de telefone.

## <a name="next-steps"></a>Próximas etapas

Para obter outras maneiras de entrar no Azure AD sem senha, como o Aplicativo autenticador Microsoft ou as chaves de segurança FIDO2, consulte [opções de autenticação Sem senha para a azure AD][concepts-passwordless].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
