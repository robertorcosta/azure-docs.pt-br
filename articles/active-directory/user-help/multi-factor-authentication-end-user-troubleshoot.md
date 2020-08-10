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
ms.date: 08/04/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 754d456f557b6c9500cdb64ee01a42124a18ab9c
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036667"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Problemas comuns com a verificação de dois fatores e sua conta corporativa ou de estudante

Sua organização do Azure Active Directory (Azure AD) pode ativar a verificação de dois fatores (2FV). Há alguns problemas comuns de 2FV que parecem ocorrer com mais frequência do que qualquer um de nós gostaríamos. Nós reunimos este artigo para descrever as correções para os problemas mais comuns.

Quando 2FV estiver on, sua entrada de conta exigirá uma combinação dos seguintes dados:

- Seu nome de usuário
- Sua senha
- Um dispositivo móvel ou telefone

o 2FV é mais seguro do que apenas uma senha, porque o 2FV requer algo que você _conhece_ , além de algo que você _tem_. Nenhum hacker tem seu telefone físico.

<center>

![Imagem de métodos de autenticação conceitual](../authentication/media/concept-mfa-howitworks/methods.png)</center>

>[!Important]
>Se você for um administrador, é possível encontrar mais informações sobre como configurar e gerenciar seu ambiente do Azure AD na [Documentação do Azure AD](https://docs.microsoft.com/azure/active-directory).
>
>Este conteúdo também se destina apenas ao uso com sua conta corporativa ou de estudante, que é a conta fornecida a você por sua organização (por exemplo, alain@contoso.com). Se você tiver problemas com a verificação de dois fatores e sua conta Microsoft pessoal, que é a conta que configurou para si mesmo (por exemplo, danielle@outlook.com), confira [Ativar ou desativar a verificação de dois fatores para sua conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Eu não tenho meu dispositivo móvel comigo

Isso acontece. Você deixou seu dispositivo móvel em casa e agora não pode usar seu telefone para verificar quem é. Talvez você tenha adicionado anteriormente um método alternativo para entrar em sua conta, como por meio de seu telefone comercial. Nesse caso, você pode usar esse método alternativo agora. Se você nunca adicionou um método de verificação alternativo, poderá entrar em contato com o suporte técnico da sua organização para obter ajuda.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Para entrar em sua conta corporativa ou de estudante usando outro método de verificação

1. Entre em sua conta, mas selecione o link **Entre de outra maneira** na página **Verificação de dois fatores**.

    ![Alterar o método de verificação de entrada](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Se você não vir o link **entre de outra forma**, isso significa que você não configurou quaisquer outros métodos de verificação. Você terá de entrar em contato com seu administrador para obter ajuda entrar em sua conta.

2. Escolha seu método de verificação alternativo e continuar o processo de autenticação de dois fatores.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Perdi meu dispositivo móvel ou ele foi roubado

Se você perdeu ou teve seu dispositivo móvel roubado, você pode executar uma das seguintes ações:

- Entre usando um método diferente.
- Peça ao suporte técnico da sua organização para limpar suas configurações.

É altamente recomendável permitir que o suporte técnico da sua organização saiba se o seu telefone foi perdido ou roubado. O suporte técnico pode fazer as atualizações apropriadas para sua conta. Após suas configurações serem limpas, você será solicitado a [registrar-se para autenticação de dois fatores](multi-factor-authentication-end-user-first-time.md) na próxima vez que você entrar.

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>Não estou recebendo o código de verificação enviado ao meu dispositivo móvel

Não receber seu código de verificação é um problema comum. O problema normalmente está relacionado ao seu dispositivo móvel e suas configurações. Aqui estão algumas ações que você pode tentar.

Tente isto | Informações de diretrizes
--------- | ------------
Reiniciar o dispositivo móvel | Às vezes, o dispositivo precisa apenas de uma atualização. Quando você reinicia o dispositivo, todos os processos e serviços em segundo plano são encerrados. A reinicialização também desliga os componentes principais do seu dispositivo. Qualquer serviço ou componente é atualizado quando você reinicia o dispositivo.
Verificar se as informações de segurança estão corretas | Verifique se as informações do método de verificação de segurança estão corretas, principalmente seus números de telefone. Se você colocar o número de telefone errado, todos os alertas irão para esse número incorreto. Felizmente, esse usuário não poderá fazer nada com os alertas, mas isso não ajuda você a entrar na conta. Para confirmar se as informações estão corretas, confira as instruções no artigo [Gerenciar suas configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md).
Verificar se as notificações estão ativadas | Verifique se o dispositivo móvel tem notificações ativadas. Verifique se os seguintes modos de notificação são permitidos: <br/><br/> &bull;Chamadas telefônicas <br/> &bull;Seu aplicativo de autenticação <br/> &bull;Seu aplicativo de mensagens de texto <br/><br/> Verifique se esses modos criam um alerta _visível_ em seu dispositivo.
Verificar se você tem um sinal de dispositivo e uma conexão com a Internet | Verifique se suas chamadas telefônicas e mensagens de texto estão chegando ao seu dispositivo móvel. Peça para um amigo ligar e enviar uma mensagem de texto para ver se recebe ambos. Se você não receber a chamada ou o texto, primeiro verifique se o dispositivo móvel está ligado. Se o dispositivo estiver ligado, mas você ainda não estiver recebendo a chamada ou o texto, provavelmente há um problema com a rede. Você precisará conversar com seu provedor. Se você geralmente tem problemas de sinal, recomendamos que instale e use o [aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md) em seu dispositivo móvel. O aplicativo autenticador pode gerar códigos de segurança aleatórios para a conexão, sem a necessidade de um sinal de celular ou conexão de Internet.
Desativar Não perturbe | Verifique se você não ligou o recurso **Não perturbe** no dispositivo móvel. Quando esse recurso está ativo, as notificações não têm permissão para alertá-lo em seu dispositivo móvel. Consulte o manual do seu dispositivo móvel para obter instruções sobre como desligar o recurso.
Desbloquear números de telefone | No Estados Unidos, as chamadas de voz da Microsoft vêm dos seguintes números: +1 (866) 539 4191, +1 (855) 330 8653 e +1 (877) 668 6536.
Verificar as configurações relacionadas à bateria | Isso parece um pouco estranho na superfície. Mas se você tiver configurado a otimização da bateria para impedir que aplicativos menos usados permaneçam ativos em segundo plano, o sistema de notificação provavelmente foi afetado. Para tentar corrigir esse problema, desative a otimização da bateria para seu aplicativo de autenticação e seu aplicativo de mensagens. Em seguida, tente entrar em sua conta novamente.
Desabilitar aplicativos de segurança de terceiros | Alguns aplicativos de segurança do telefone bloqueiam mensagens de texto e chamadas telefônicas de chamadores desagradáveis desconhecidos. Esses aplicativos podem impedir que seu telefone receba o código de verificação. Tente desabilitar qualquer aplicativo de segurança de terceiros em seu telefone e, em seguida, solicite que outro código de verificação seja enviado.

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>Não estou recebendo uma solicitação para minha segunda informação de verificação

Você entra em sua conta corporativa ou de estudante usando seu nome de usuário e senha. Em seguida, você deve ser solicitado a fornecer suas informações adicionais de verificação de segurança. Se não for solicitado, talvez você ainda não tenha configurado seu dispositivo. Seu dispositivo móvel precisa ser configurado para funcionar com seu método de verificação de segurança adicional específico.

Para verificar se o dispositivo móvel está ativado e disponível, consulte o artigo [gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md) . Se você sabe que não configurou seu dispositivo ou sua conta, pode fazê-lo agora seguindo as etapas do artigo [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) artigo.

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>Tenho um novo número de telefone e desejo adicioná-lo

Se você tiver um novo número de telefone, precisará atualizar os detalhes do método de verificação de segurança. Isso permite que seus prompts de verificação vá para o local certo. Para atualizar seu método de verificação, siga as etapas na seção **Adicionar ou alterar seu número de telefone** do artigo [gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) .

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>Tenho um novo dispositivo móvel e desejo adicioná-lo

Se você tiver um novo dispositivo móvel, precisará configurá-lo para funcionar com a verificação de dois fatores. Esta é uma solução de várias etapas:

1. Configure seu dispositivo para trabalhar com sua conta seguindo as etapas no artigo [configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md) .

1. Atualize suas informações de conta e dispositivo na página de **verificação de segurança adicional** . Execute a atualização excluindo o dispositivo antigo e adicionando o novo. Para obter mais informações, confira o artigo [Gerenciar suas configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md).

Etapas opcionais:

- Instale e configure o aplicativo Microsoft Authenticator em seu dispositivo móvel. Instale o seguindo as etapas no artigo [baixar e instalar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md) .

- Ative a verificação de dois fatores (2FV) para seus dispositivos confiáveis. Ative o 2FV seguindo as etapas na seção **Ativar prompts de verificação de dois fatores em um dispositivo confiável** do artigo [gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) .

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Estou tendo problemas para entrar no meu dispositivo móvel quando viajo

Você pode achar mais complicado usar um método de verificação relacionado a dispositivos móveis, como uma mensagem de texto, quando está fora do seu país. Também é possível que seu dispositivo móvel gere a cobrança de tarifas de roaming. Nesse caso, recomendamos que você use o aplicativo Microsoft Authenticator, com a opção de conectar-se a um hotspot Wi-Fi. Para obter mais informações sobre como configurar o aplicativo Microsoft Authenticator em seu dispositivo móvel, consulte o artigo [baixar e instalar o aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md) .

## <a name="i-cant-get-my-app-passwords-to-work"></a>Não consigo fazer com que minhas senhas de aplicativo funcionem

A senha de aplicativo substitui a senha normal, para aplicativos de área de trabalho mais antigos que não dão suporte à autenticação de dois fatores. Primeiro, verifique se você digitou a senha corretamente. Se isso não corrigir, tente criar uma nova senha de aplicativo para o aplicativo. Faça isso seguindo as etapas na seção **criar e excluir senhas de aplicativo usando o portal meus aplicativos** do artigo [gerenciar senhas de aplicativo para a verificação em duas etapas](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="i-cant-turn-off-two-factor-verification"></a>Não consigo desativar a verificação de dois fatores

Se estiver usando a verificação de dois fatores com sua conta corporativa ou de estudante (por exemplo, alain@contoso.com), provavelmente significará que sua organização decidiu que você precisa usar esse recurso de segurança adicional. Como sua organização decidiu que você deve usar esse recurso, não há como desativá-lo individualmente. Se, no entanto, estiver usando a verificação de dois fatores com uma conta pessoal, como alain@outlook.com, você poderá ligar e desligar o recurso. Para obter instruções sobre como controlar a verificação de dois fatores nas suas contas pessoais, confira [Ativando ou desativando a verificação de dois fatores para sua conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Se você não puder desligar a verificação de dois fatores, isso também poderá ser devido aos padrões de segurança que foram aplicados no nível da organização. Para obter mais informações sobre padrões de segurança, confira [O que são os padrões de segurança?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Não encontrei uma resposta para o meu problema

Se você tiver tentado essas etapas, mas ainda estiver com problemas, entre em contato com o Suporte técnico da sua organização para obter ajuda.

## <a name="related-articles"></a>Artigos relacionados

- [Gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

- [Perguntas frequentes sobre o aplicativo Microsoft Authenticator](user-help-auth-app-faq.md)
