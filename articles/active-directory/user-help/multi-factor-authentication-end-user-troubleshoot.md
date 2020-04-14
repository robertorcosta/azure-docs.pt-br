---
title: Problemas comuns com autenticação de fator de conta dois - Azure AD
description: Soluções para alguns dos problemas mais comuns de verificação de dois fatores e sua conta de trabalho ou escola.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.openlocfilehash: 3df79fb1e103ddfaafeb348641e675cd250f5858
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271685"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>Problemas comuns com verificação de dois fatores e sua conta de trabalho ou escola

Quando a organização do Azure Active Directory (Azure AD) ativa a verificação de dois fatores, o login da sua conta de trabalho ou escola requer uma combinação do seu nome de usuário, sua senha e um dispositivo móvel ou telefone. É mais seguro do que apenas uma senha, contando com duas formas de autenticação: algo que você sabe, e algo que você tem com você. A verificação de dois fatores pode ajudar a impedir que hackers mal-intencionados fintem ser você, porque mesmo que eles tenham sua senha, as chances são de que eles não têm o seu dispositivo também.

<center>

![Imagem de métodos de autenticação conceitual](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Existem alguns problemas comuns de verificação de dois fatores que parecem acontecer com mais freqüência do que qualquer um de nós gostaria. Reunimos este artigo para resolver os problemas mais comuns e algumas possíveis correções.

>[!Important]
>Se você é um administrador, você pode encontrar mais informações sobre como configurar e gerenciar seu ambiente Azure AD na [documentação azure AD](https://docs.microsoft.com/azure/active-directory).
>
>Este conteúdo também destina-se apenas para uso com sua conta de trabalho ou escola, alain@contoso.comque é a conta fornecida a você pela sua organização (por exemplo, ). Se você estiver tendo problemas com a verificação de dois fatores e sua conta danielle@outlook.commicrosoft pessoal, que é uma conta que você configura para si mesmo (por exemplo, ), consulte [Ligar ou desativar a verificação de dois fatores para sua conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-dont-have-my-mobile-device-with-me"></a>Eu não tenho meu dispositivo móvel comigo

Acontece. Você deixou seu dispositivo móvel em casa e agora não pode usar seu telefone para verificar quem você é. Se você adicionou anteriormente outro método para entrar em sua conta, como seu telefone de escritório, você deve ser capaz de usar esse método agora. Se você nunca adicionou um método adicional de verificação, você terá que entrar em contato com o help desk da sua organização e ajudá-los a voltar para sua conta.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Para entrar na sua conta de trabalho ou escola usando outro método de verificação

1. Faça login na sua conta, mas selecione o **link Assinar em outra forma** na página de **verificação de dois fatores.**

    ![Alterar o método de verificação de entrada](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    Se você não vir o link **entre de outra forma**, isso significa que você não configurou quaisquer outros métodos de verificação. Você terá de entrar em contato com seu administrador para obter ajuda entrar em sua conta.

2. Escolha seu método alternativo de verificação e continue com o processo de verificação de dois fatores.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Perdi meu dispositivo móvel ou foi roubado.

Se você perdeu ou teve seu dispositivo móvel roubado, você pode fazer login usando um método diferente ou pode pedir ao help desk da sua organização para limpar suas configurações. Recomendamos fortemente informar o help desk da sua organização se seu telefone foi perdido ou roubado, para que as atualizações apropriadas possam ser feitas em sua conta. Depois que suas configurações forem liberadas, você será solicitado a [registrar-se para verificação de dois fatores](multi-factor-authentication-end-user-first-time.md) na próxima vez que fizer login.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Não estou recebendo o código de verificação enviado para o meu dispositivo móvel

Não obter seu código de verificação é um problema comum e está tipicamente relacionado ao seu dispositivo móvel e suas configurações. Algumas coisas possíveis para tentar:

Experimente isto | Informações de orientação
--------- | ------------
Reinicie seu dispositivo móvel | Às vezes, seu dispositivo só precisa de uma atualização. Reiniciar o dispositivo encerra todos os processos ou serviços em segundo plano que estejam em execução no momento e podem causar problemas, juntamente com a atualização dos componentes principais do dispositivo, reiniciando-os no caso de eles caírem em algum momento.
Verifique se suas informações de segurança estão corretas | Certifique-se de que as informações do seu método de verificação de segurança sejam precisas, especialmente seus números de telefone. Se você colocar o número de telefone errado, todos os seus alertas irão para esse número incorreto. Felizmente, esse usuário não será capaz de fazer nada com os alertas, mas também não vai ajudá-lo a entrar na sua conta. Para ter certeza de que suas informações estão corretas, consulte as instruções no [artigo Gerenciar as configurações do método de verificação de dois fatores.](multi-factor-authentication-end-user-manage-settings.md)
Verifique se suas notificações estão ligadas | Certifique-se de que seu dispositivo móvel tenha notificações ligadas e que você selecionou um método de notificação que permite chamadas telefônicas, seu aplicativo de autenticação e seu aplicativo de mensagens (para mensagens de texto) para enviar notificações de alerta visíveis para o seu dispositivo móvel.
Certifique-se de ter um sinal de dispositivo e conexão com a Internet | Certifique-se de que suas chamadas telefônicas e mensagens de texto estão chegando ao seu dispositivo móvel. Peça a um amigo para ligar para você e enviar uma mensagem de texto para garantir que você receba ambos. Se você não fizer isso, primeiro verifique se o seu dispositivo móvel está ligado. Se o seu dispositivo estiver ligado, mas você ainda não está recebendo a chamada ou texto, é mais provável que seja um problema com sua rede e você precisará falar com seu provedor. Se você tem problemas relacionados ao sinal, recomendamos que você instale e use o [aplicativo Microsoft Authenticator](user-help-auth-app-download-install.md) em seu dispositivo móvel. O aplicativo autenticador pode gerar códigos de segurança aleatórios para o login, sem exigir qualquer sinal de celular ou conexão com a Internet.
Desligue Não perturbe | Certifique-se de que não ligou o recurso **Não perturbe** para o seu dispositivo móvel. Quando esse recurso é ligado, as notificações não podem alertá-lo em seu dispositivo móvel. Consulte o manual do seu dispositivo móvel para obter instruções sobre como desativar este recurso.
Desbloquear números de telefone | Nos Estados Unidos, as chamadas de voz da Microsoft vêm dos seguintes números: +1 (866) 539 4191, +1 (855) 330 8653 e +1 (877) 668 6536.
Verifique as configurações relacionadas à bateria | Este parece um pouco estranho na superfície, mas se você configurar sua otimização de bateria para impedir que aplicativos menos usados permaneçam ativos em segundo plano, seu sistema de notificação provavelmente foi afetado. Para tentar corrigir esse problema, desligue a otimização da bateria para seu aplicativo de autenticação e seu aplicativo de mensagens e tente entrar na sua conta novamente.
Desativar aplicativos de segurança de terceiros | Se você tiver um aplicativo que proteja mensagens de texto ou chamadas telefônicas para minimizar os chamadores desconhecidos, eles podem impedir que o código de verificação seja recebido. Tente desativar quaisquer aplicativos de segurança de terceiros em seu telefone e, em seguida, solicite que outro código de verificação seja enviado.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Eu não estou sendo solicitado para a minha segunda informação de verificação

Se você fez o trabalho ou a conta da escola usando seu nome de usuário e senha, mas não foi solicitado sobre suas informações adicionais de verificação de segurança, talvez você ainda não tenha configurado seu dispositivo. Seu dispositivo móvel deve ser configurado para trabalhar com seu método específico de verificação de segurança adicional. Para ter certeza de que você ligou seu dispositivo móvel e que ele está disponível para usar com seu método de verificação, consulte o artigo [Gerenciar o artigo de configurações do método de verificação de dois fatores.](multi-factor-authentication-end-user-manage-settings.md) Se você sabe que não configurou seu dispositivo ou sua conta, você pode [fazê-lo](multi-factor-authentication-end-user-first-time.md) agora seguindo as etapas do Configurar minha conta para um artigo de verificação em duas etapas.

## <a name="i-got-a-new-phone-number-and-i-want-to-add-it"></a>Eu tenho um novo número de telefone e eu quero adicioná-lo

Se você tiver um novo número de telefone, você precisará atualizar os detalhes do seu método de verificação de segurança para que suas solicitações de verificação vão para o local certo. Para atualizar seu método de verificação, siga as etapas do **artigo Adicionar ou alterar o número** de telefone do artigo Gerenciar o artigo de [configurações do método de verificação de dois fatores.](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)

## <a name="i-got-a-new-mobile-device-and-i-want-to-add-it"></a>Eu tenho um novo dispositivo móvel e eu quero adicioná-lo

Se você conseguiu um novo dispositivo móvel, você precisará configurá-lo para trabalhar com verificação de dois fatores. Esta é uma solução em várias etapas:

1. Configure seu dispositivo para trabalhar com sua conta de trabalho ou escola seguindo as etapas do Configurar minha conta para um artigo [de verificação em duas etapas.](multi-factor-authentication-end-user-first-time.md)

1. Atualize suas informações de conta e dispositivo na página **de verificação de segurança adicional,** excluindo seu dispositivo antigo e adicionando o novo. Para obter mais informações, consulte o [artigo Gerenciar o artigo de configurações do método de verificação de dois fatores.](multi-factor-authentication-end-user-manage-settings.md)

Etapas opcionais:

- Baixe, instale e configure o aplicativo Microsoft Authenticator em seu dispositivo móvel seguindo as etapas do Download e instale o artigo [do aplicativo Microsoft Authenticator.](user-help-auth-app-download-install.md)

- Ative a verificação de dois fatores para seus dispositivos confiáveis seguindo as etapas da **verificação de dois fatores em uma** seção de dispositivo confiável do artigo [Gerenciar o artigo de configurações do método de verificação de dois fatores.](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Estou tendo problemas para entrar no meu dispositivo móvel enquanto viajo

Você pode achar mais difícil usar um método de verificação relacionado a dispositivos móveis, como uma mensagem de texto, enquanto estiver em um local internacional. Também é possível que seu dispositivo móvel possa fazer com que você incorra em taxas de roaming. Para esta situação, recomendamos que você use o aplicativo Microsoft Authenticator, com a opção de se conectar a um hotspot Wi-Fi. Para obter mais informações sobre como baixar, instalar e configurar o aplicativo Microsoft Authenticator em seu dispositivo móvel, consulte o Download e instale o artigo [do aplicativo Microsoft Authenticator.](user-help-auth-app-download-install.md)

## <a name="i-cant-get-my-app-passwords-to-work"></a>Não consigo fazer minhas senhas de aplicativos funcionarem

As senhas do aplicativo substituem sua senha normal por aplicativos de desktop mais antigos que não suportam verificação de dois fatores. Primeiro, verifique se você digitou a senha corretamente. Se isso não corrigi-lo, tente criar uma nova senha de aplicativo para o aplicativo seguindo as etapas do **Criar e excluir senhas** do aplicativo usando a seção Do portal Meus aplicativos do [artigo Gerenciar senhas do aplicativo para verificação em duas etapas.](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)

## <a name="i-cant-turn-two-factor-verification-off"></a>Não posso desligar a verificação de dois fatores.

Se você estiver usando a verificação de dois fatores alain@contoso.comcom sua conta de trabalho ou escola (por exemplo, ), provavelmente significa que sua organização decidiu que você deve usar esse recurso de segurança adicionado. Como sua organização decidiu que você deve usar esse recurso, não há como desativá-lo individualmente. Se, no entanto, você estiver usando a verificação de dois fatores com uma conta pessoal, como, alain@outlook.comvocê tem a capacidade de ativar e desativar o recurso. Para obter instruções sobre como controlar a verificação de dois fatores para suas contas pessoais, consulte [Ligar ou desativar a verificação de dois fatores para sua conta Microsoft](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

Se você não pode desativar a verificação de dois fatores, também pode ser por causa dos padrões de segurança que foram aplicados no nível da organização. Para obter mais informações sobre padrões de segurança, consulte [O que é defulta de segurança?](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Não encontrei uma resposta para o meu problema

Se você já tentou essas etapas, mas ainda está com problemas, entre em contato com o help desk da sua organização para obter assistência.

## <a name="related-articles"></a>Artigos relacionados

- [Gerenciar as configurações do método de verificação de dois fatores](multi-factor-authentication-end-user-manage-settings.md)

- [Configurar minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

- [Perguntas frequentes sobre o aplicativo Microsoft Authenticator](user-help-auth-app-faq.md)
