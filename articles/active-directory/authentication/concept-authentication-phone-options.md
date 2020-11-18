---
title: Métodos de autenticação por telefone – Azure Active Directory
description: Saiba mais sobre como usar métodos de autenticação por telefone no Azure Active Directory para ajudar a melhorar e proteger eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39555c5b396e8a8fd3449331cd2fd68b96ad2087
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839991"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Métodos de autenticação em opções de Azure Active Directory-telefone

Para autenticação direta usando mensagem de texto, você pode [Configurar e habilitar usuários para a autenticação baseada em SMS (versão prévia)](howto-authentication-sms-signin.md). A entrada baseada em SMS é ótima para os trabalhadores de linha de frente. Com a entrada baseada em SMS, os usuários não precisam saber um nome de usuário e uma senha para acessar aplicativos e serviços. Em vez disso, o usuário insere seu número de telefone celular registrado, recebe uma mensagem de texto com um código de verificação e insere isso na interface de entrada.

Os usuários também podem verificar usando um telefone celular ou telefone comercial como forma secundária de autenticação usada durante a autenticação multifator do Azure AD ou a SSPR (redefinição de senha de autoatendimento).

Para funcionarem adequadamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber*, por exemplo: *+1 4251234567*.

> [!NOTE]
> Precisa haver um espaço entre o código do país/região e o número de telefone.
>
> A redefinição de senha não dá suporte a ramais telefônicos. Mesmo no formato *+1 4251234567X12345*, as extensões são removidas antes que a chamada seja completada.

## <a name="mobile-phone-verification"></a>Verificação por celular

Para a autenticação multifator do Azure AD ou o SSPR, os usuários podem optar por receber uma mensagem de texto com um código de verificação para entrar na interface de entrada ou receber uma chamada telefônica.

Se os usuários não quiserem que o número do telefone celular seja visível no diretório, mas ainda quiserem usá-lo para redefinição de senha, os administradores não deverão preencher o número de telefone no diretório. Em vez disso, os usuários devem preencher o atributo **Telefone de Autenticação** por meio do registro combinado das informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo). Os administradores poderão ver essas informações no perfil do usuário, mas elas não serão publicadas em nenhum outro lugar.

:::image type="content" source="media/concept-authentication-methods/user-authentication-methods.png" alt-text="Captura de tela do portal do Azure que mostra os métodos de autenticação com um número de telefone preenchido":::

A Microsoft não garante a entrega de prompt de autenticação multifator do Azure AD com base em voz ou SMS consistente pelo mesmo número. Pensando no melhor para nossos usuários, nós podemos adicionar ou remover códigos curtos a qualquer momento, pois fazemos ajustes de rota para melhorar a capacidade de entrega de SMS. A Microsoft não dá suporte a códigos curtos para países/regiões além dos Estados Unidos e do Canadá.

### <a name="text-message-verification"></a>Verificação por mensagem de texto

Com a verificação de mensagem de texto durante o SSPR ou a autenticação multifator do Azure AD, um SMS é enviado para o número de telefone celular que contém um código de verificação. Para concluir o processo de entrada, o código de verificação fornecido é inserido na interface de entrada.

### <a name="phone-call-verification"></a>Verificação por chamada telefônica

Com a verificação de chamada telefônica durante o SSPR ou a autenticação multifator do Azure AD, é feita uma chamada de voz automática para o número de telefone registrado pelo usuário. Para concluir o processo de entrada, o usuário será solicitado a pressionar # no respectivo teclado.

## <a name="office-phone-verification"></a>Verificação por telefone comercial

Com a verificação de chamada telefônica durante o SSPR ou a autenticação multifator do Azure AD, é feita uma chamada de voz automática para o número de telefone registrado pelo usuário. Para concluir o processo de entrada, o usuário será solicitado a pressionar # no respectivo teclado.

## <a name="troubleshooting-phone-options"></a>Solucionar problemas das opções por telefone

Se você tiver problemas com a autenticação por telefone do Azure AD, examine as seguintes etapas de solução de problemas:

* "Você atingiu nosso limite de chamadas de verificação" ou "você atingiu nosso limite em códigos de verificação de texto" ao entrar
   * Use o aplicativo Microsoft Authenticator ou o código de verificação para concluir a autenticação ou tente novamente mais tarde.
* ID de chamador bloqueada em um único dispositivo.
   * Examine os números bloqueados configurados no dispositivo.
* Número de telefone errado, código de país/região incorreto ou confusão entre número de telefone pessoal e o número de telefone comercial.
   * Solucione os problemas com o objeto do usuário e os métodos de autenticação configurados. Verifique se os números de telefone corretos estão registrados.
* PIN incorreto inserido.
   * Confirme se o usuário usou o PIN correto como registrado para sua conta (somente usuários do servidor MFA).
* Chamada encaminhada para a caixa postal.
   * Verifique se o telefone do usuário está ativado e se o serviço está disponível na área dele ou use um método alternativo.
* Usuário está bloqueado
   * Peça para o administrador do Azure AD desbloquear o usuário no portal do Azure.
* Não há assinatura para SMS no dispositivo.
   * Peça para o usuário alterar os métodos ou ativar o SMS no dispositivo.
* Provedores de telecomunicações com falha, por exemplo, nenhuma entrada de telefone detectada, problemas de tons DTMF ausentes, ID de chamador bloqueada em vários dispositivos ou bloqueio de SMS em vários dispositivos.
   * A Microsoft usa vários provedores de telecomunicações a fim de rotear chamadas telefônicas e mensagens SMS para autenticação. Se você se deparar com qualquer um dos problemas acima, peça para um usuário tentar usar o método pelo menos cinco vezes em cinco minutos e tenha as informações do usuário disponíveis ao entrar em contato com o suporte da Microsoft.

## <a name="next-steps"></a>Próximas etapas

Para começar, consulte o [tutorial para redefinição de senha de autoatendimento (SSPR)][tutorial-sspr] e [autenticação multifator do Azure ad][tutorial-azure-mfa].

Para saber mais sobre os conceitos de SSPR, confira [Como funciona a redefinição de senha self-service do Azure AD][concept-sspr].

Para saber mais sobre os conceitos de MFA, confira [como funciona a autenticação multifator do Azure ad][concept-mfa].

Saiba mais sobre como configurar métodos de autenticação usando a [API REST do Microsoft Graph versão beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
