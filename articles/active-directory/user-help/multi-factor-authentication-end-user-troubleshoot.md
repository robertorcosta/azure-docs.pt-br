---
title: Problemas comuns com a autenticação de conta de dois fatores – Azure AD
description: Soluções para alguns dos problemas mais comuns com a verificação de dois fatores e sua conta corporativa ou de estudante.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 8d2e34bcfd180dfeb814dace2a496f3ac593c5bf
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738601"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Problemas comuns com a verificação de dois fatores e sua conta corporativa ou de estudante

Quando sua organização do Azure AD (Azure Active Directory) ativa a verificação de dois fatores, sua entrada na conta corporativa ou de estudante requer uma combinação de seu nome de usuário, sua senha e um dispositivo móvel ou telefone. É mais segura do que apenas uma senha, pois se baseia em duas formas de autenticação: algo que você sabe e algo que possui. A verificação de dois fatores pode ajudar a evitar a ação de hackers mal-intencionados fingindo ser você porque, mesmo que eles tenham sua senha, provavelmente não têm o seu dispositivo.

<center>

![Imagem de métodos de autenticação conceitual](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Há alguns problemas comuns com a verificação de dois fatores que parecem ocorrer com mais frequência do que gostaríamos. Nós criamos este artigo para abordar os problemas mais comuns e algumas possíveis correções.

>[!Important]
>Se você for um administrador, é possível encontrar mais informações sobre como configurar e gerenciar seu ambiente do Azure AD na [Documentação do Azure AD](https://docs.microsoft.com/azure/active-directory).
>
>Este conteúdo também se destina apenas ao uso com sua conta corporativa ou de estudante, que é a conta fornecida a você por sua organização (por exemplo, alain@contoso.com). Se você tiver problemas com a verificação de dois fatores e sua conta Microsoft pessoal, que é a conta que configurou para si mesmo (por exemplo, danielle@outlook.com), confira [Ativar ou desativar a verificação de dois fatores para sua conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Eu não tenho meu dispositivo móvel comigo

Isso acontece. Você deixou seu dispositivo móvel em casa e agora não pode usar o telefone para verificar quem é. Se você adicionou anteriormente outro método para entrar em sua conta, como o telefone comercial, deve ser capaz de usá-lo agora. Se você nunca adicionou um método de verificação adicional, precisará entrar em contato com o suporte técnico da sua organização e pedir ajuda a eles para entrar na sua conta.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Para entrar em sua conta corporativa ou de estudante usando outro método de verificação

1. Entre em sua conta, mas selecione o link **Entre de outra maneira** na página **Verificação de dois fatores**.

    ![Alterar o método de verificação de entrada](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Se você não vir o link **entre de outra forma**, isso significa que você não configurou quaisquer outros métodos de verificação. Você terá de entrar em contato com seu administrador para obter ajuda entrar em sua conta.

2. Escolha seu método de verificação alternativo e continuar o processo de autenticação de dois fatores.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Perdi meu dispositivo móvel ou ele foi roubado

Se você perdeu ou teve seu dispositivo móvel roubado, poderá entrar usando um método diferente ou pedir ao suporte técnico da sua organização para limpar suas configurações. É altamente recomendável informar o suporte técnico da sua organização se seu telefone foi perdido ou roubado, pois assim as atualizações adequadas podem ser feitas na sua conta. Após suas configurações serem limpas, você será solicitado a [registrar-se para autenticação de dois fatores](multi-factor-authentication-end-user-first-time.md) na próxima vez que você entrar.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Não estou recebendo o código de verificação enviado ao meu dispositivo móvel

Não receber o código de verificação é um problema comum e, em geral, está relacionado ao dispositivo móvel e suas configurações. Algumas coisas possíveis a serem experimentadas:

Tente isto | Informações de diretrizes
--------- | ------------
Reiniciar o dispositivo móvel | Às vezes, o dispositivo precisa apenas de uma atualização. Reiniciar o dispositivo encerra todos os processos em segundo plano ou serviços que estejam em execução no momento e pode causar problemas, juntamente com a atualização dos componentes principais do dispositivo, na reinicialização caso tenham falhado em algum momento.
Verificar se as informações de segurança estão corretas | Verifique se as informações do método de verificação de segurança estão corretas, principalmente seus números de telefone. Se você colocar o número de telefone errado, todos os alertas irão para esse número incorreto. Felizmente, esse usuário não poderá fazer nada com os alertas, mas isso não ajuda você a entrar na conta. Para confirmar se as informações estão corretas, confira as instruções no artigo [Gerenciar suas configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md).
Verificar se as notificações estão ativadas | Verifique se o dispositivo móvel tem notificações ativadas e se você selecionou um método de notificação que permita chamadas telefônicas e o envio de notificações de alerta visíveis a seu dispositivo móvel pelo seu aplicativo de autenticação e pelo aplicativo de mensagens (para mensagens de texto).
Verificar se você tem um sinal de dispositivo e uma conexão com a Internet | Verifique se suas chamadas telefônicas e mensagens de texto estão chegando ao seu dispositivo móvel. Peça para um amigo ligar e enviar uma mensagem de texto para ver se recebe ambos. Se não os receber, primeiro verifique se o dispositivo móvel está ligado. Se o dispositivo está ligado, mas você ainda não está recebendo chamada ou texto, provavelmente há um problema com a rede e você precisa conversar com seu provedor. Se você geralmente tem problemas de sinal, recomendamos que instale e use o [aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md) em seu dispositivo móvel. O aplicativo autenticador pode gerar códigos de segurança aleatórios para a conexão, sem a necessidade de um sinal de celular ou conexão de Internet.
Desativar Não perturbe | Verifique se você não ligou o recurso **Não perturbe** no dispositivo móvel. Quando esse recurso está ativo, as notificações não têm permissão para alertá-lo em seu dispositivo móvel. Consulte o manual do seu dispositivo móvel para obter instruções sobre como desligar o recurso.
Desbloquear números de telefone | No Estados Unidos, as chamadas de voz da Microsoft vêm dos seguintes números: +1 (866) 539 4191, +1 (855) 330 8653 e +1 (877) 668 6536.
Verificar as configurações relacionadas à bateria | Isso parece um pouco estranho de início, mas se você tiver configurado a otimização da bateria para impedir que aplicativos menos usados permaneçam ativos em segundo plano, o sistema de notificação provavelmente foi afetado. Para tentar corrigir esse problema, desligue a otimização da bateria para seus aplicativos de autenticação e de mensagens e tente entrar novamente na conta.
Desabilitar aplicativos de segurança de terceiros | Se você tiver um aplicativo que protege mensagens de texto ou chamadas telefônicas para minimizar chamadores desconhecidos, eles poderão impedir o recebimento do código de verificação. Tente desabilitar os aplicativos de segurança de terceiros em seu telefone e solicite a envio de outro código de verificação.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Não estou recebendo a solicitação da minha segunda informação de verificação

Se você tiver entrado em sua conta corporativa ou de estudante usando seu nome de usuário e senha, mas não tiver sido avisado sobre as informações adicionais de verificação de segurança, talvez ainda não tenha configurado seu dispositivo. Seu dispositivo móvel precisa ser configurado para funcionar com seu método de verificação de segurança adicional específico. Para ter certeza de que você ligou seu dispositivo móvel e que ele está disponível para uso com seu método de verificação, confira o artigo [Gerenciar suas configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md). Se você sabe que não configurou seu dispositivo ou sua conta, pode fazê-lo agora seguindo as etapas do artigo [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) artigo.

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Recebi um novo número de telefone e desejo adicioná-lo

Se você obtiver um novo número de telefone, precisará atualizar os detalhes do método de verificação de segurança para que os prompts de verificação levem ao local certo. Para atualizar seu método de verificação, siga as etapas na seção **Adicionar ou alterar número de telefone** do artigo [Gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) artigo.

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Tenho um novo dispositivo móvel e desejo adicioná-lo

Se você obtiver um novo dispositivo móvel, precisará configurá-lo para funcionar com a verificação de dois fatores. Esta é uma solução de várias etapas:

1. Configure seu dispositivo para trabalhar com sua conta corporativa ou de estudante seguindo as etapas do artigo [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md).

1. Atualize suas informações de conta e dispositivo na página **Verificação de segurança adicional**, excluindo o dispositivo antigo e adicionando o novo. Para obter mais informações, confira o artigo [Gerenciar suas configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md).

Etapas opcionais:

- Baixe, instale e configure o aplicativo Microsoft Authenticator em seu dispositivo móvel seguindo as etapas do artigo [Baixar e instalar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md).

- Ative a verificação de dois fatores para seus dispositivos confiáveis seguindo as etapas na seção **Ativar prompts de verificação de dois fatores em um dispositivo confiável** do artigo [Gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Estou tendo problemas para entrar no meu dispositivo móvel quando viajo

Você pode achar mais complicado usar um método de verificação relacionado a dispositivos móveis, como uma mensagem de texto, quando está fora do seu país. Também é possível que seu dispositivo móvel gere a cobrança de tarifas de roaming. Nesse caso, recomendamos que você use o aplicativo Microsoft Authenticator, com a opção de conectar-se a um hotspot Wi-Fi. Para obter mais informações sobre como baixar, instalar e configurar o aplicativo Microsoft Authenticator em seu dispositivo móvel, confira o artigo [Baixar e instalar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md).

## <a name="i-cant-get-my-app-passwords-to-work"></a>Não consigo fazer com que minhas senhas de aplicativo funcionem

A senha de aplicativo substitui a senha normal, para aplicativos de área de trabalho mais antigos que não dão suporte à autenticação de dois fatores. Primeiro, verifique se você digitou a senha corretamente. Se isso não for corrigido, tente criar uma nova senha para o aplicativo seguindo as etapas na seção **Criar e excluir senhas de aplicativo usando o portal Meus Aplicativos** do artigo [Gerenciar senhas de aplicativo para verificação em duas etapas](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page).

## <a name="i-cant-turn-two-factor-verification-off"></a>Não consigo desligar a verificação de dois fatores

Se estiver usando a verificação de dois fatores com sua conta corporativa ou de estudante (por exemplo, alain@contoso.com), provavelmente significará que sua organização decidiu que você precisa usar esse recurso de segurança adicional. Como sua organização decidiu que você deve usar esse recurso, não há como desativá-lo individualmente. Se, no entanto, estiver usando a verificação de dois fatores com uma conta pessoal, como alain@outlook.com, você poderá ligar e desligar o recurso. Para obter instruções sobre como controlar a verificação de dois fatores nas suas contas pessoais, confira [Ativando ou desativando a verificação de dois fatores para sua conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Se você não puder desligar a verificação de dois fatores, isso também poderá ser devido aos padrões de segurança que foram aplicados no nível da organização. Para obter mais informações sobre padrões de segurança, confira [O que são os padrões de segurança?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Não encontrei uma resposta para o meu problema

Se você tiver tentado essas etapas, mas ainda estiver com problemas, entre em contato com o Suporte técnico da sua organização para obter ajuda.

## <a name="related-articles"></a>Artigos relacionados

- [Gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

- [Perguntas frequentes sobre o aplicativo Microsoft Authenticator](user-help-auth-app-faq.md)
