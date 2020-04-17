---
title: Registro combinado para SSPR e MFA – Azure Active Directory
description: Autenticação multifatorial do Azure AD e registro de redefinição de senha de autoatendimento
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48350bf8f0ffb8681d95f6f42f9aa93256395f9a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534679"
---
# <a name="combined-security-information-registration-overview"></a>Visão geral do registro de informações de segurança combinada

Antes do registro combinado, os usuários registraram métodos de autenticação para autenticação multifatorial do Azure e redefinição de senha de autoatendimento (SSPR) separadamente. As pessoas estavam confusas de que métodos semelhantes foram usados para autenticação multifatorial e SSPR, mas tiveram que se registrar para ambos os recursos. Agora, com o registro combinado, os usuários podem se cadastrar uma vez e obter os benefícios tanto da Autenticação Multifatorial quanto do SSPR.

Este artigo descreve o que é registro de segurança combinado. Para começar com o registro combinado de segurança, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [Habilite o registro de segurança combinado](howto-registration-mfa-sspr-combined.md)

![Meu perfil mostrando informações de segurança registradas para um usuário](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Antes de habilitar a nova experiência, revise essa documentação focada no administrador e a documentação focada no usuário para garantir que você entenda a funcionalidade e o efeito desse recurso. Baseie seu treinamento na [documentação](../user-help/user-help-security-info-overview.md) do usuário para preparar seus usuários para a nova experiência e ajudar a garantir um lançamento bem-sucedido.

O registro de informações de segurança combinada do Azure AD não está disponível atualmente para nuvens nacionais como o Azure US Government, Azure Germany ou Azure China 21Vianet.

> [!IMPORTANT]
> Os usuários habilitados tanto para a visualização original quanto para a experiência de registro combinada aprimorada verão o novo comportamento. Os usuários habilitados para ambas as experiências verão apenas a nova experiência do Meu Perfil. O novo Meu Perfil se alinha com o visual e a sensação de registro combinado e proporciona uma experiência perfeita para os usuários. Os usuários podem ver [https://myprofile.microsoft.com](https://myprofile.microsoft.com)Meu perfil indo para .
>
> Você pode encontrar uma mensagem de erro ao tentar acessar a opção Informações de segurança. Por exemplo, "Desculpe, não podemos inscrevê-lo". Neste caso, confirme se você não tem nenhuma configuração ou objeto de política de grupo que bloqueie cookies de terceiros no navegador da Web.

Minhas páginas de perfil são localizadas com base nas configurações de idioma do computador que acessa a página. A Microsoft armazena o idioma mais recente usado no cache do navegador, de modo que as tentativas subseqüentes de acessar as páginas continuarão a renderizar no último idioma usado. Se você limpar o cache, as páginas serão rerenderizadas. Se você quiser forçar um idioma `?lng=<language>` específico, você pode adicionar `<language>` ao final da URL, onde está o código do idioma que você deseja renderizar.

![Configure sspr ou outros métodos de verificação de segurança](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Métodos disponíveis no registro combinado

O registro combinado suporta os seguintes métodos e ações de autenticação:

|   | Registrar  | Alterar | Excluir |
| --- | --- | --- | --- |
| Microsoft Authenticator | Sim (máximo de 5) | Não | Sim |
| Outro aplicativo autenticador | Sim (máximo de 5) | Não | Sim |
| Token de hardware | Não | Não | Sim |
| Telefone | Sim | Sim | Sim |
| Telefone alternativo | Sim | Sim | Sim |
| Telefone comercial | Não | Não | Não |
| Email | Sim | Sim | Sim |
| Perguntas de segurança | Sim | Não | Sim |
| Senhas de aplicativo | Sim | Não | Sim |
| Chaves de segurança FIDO2<br />*Modo gerenciado somente a partir da página [de informações de segurança](https://mysignins.microsoft.com/security-info)*| Sim | Sim | Sim |

> [!NOTE]
> As senhas do aplicativo estão disponíveis apenas para usuários que foram aplicados para autenticação multifatorial. As senhas do aplicativo não estão disponíveis para usuários habilitados para autenticação multifatorial através de uma política de Acesso Condicional.

Os usuários podem definir uma das seguintes opções como o método padrão de autenticação multifatorial:

- Microsoft Authenticator – notificação.
- Aplicativo autenticador ou token de hardware – código.
- Telefonema.
- Mensagem de texto.

À medida que continuamos a adicionar mais métodos de autenticação ao Azure AD, esses métodos estarão disponíveis no registro combinado.

## <a name="combined-registration-modes"></a>Modos de registro combinados

Existem dois modos de registro combinado: interromper e gerenciar.

- **O modo interrupt** é uma experiência semelhante ao assistente, apresentada aos usuários quando eles se registram ou atualizam suas informações de segurança no login.
- **O modo de gerenciamento** faz parte do perfil do usuário e permite que os usuários gerenciem suas informações de segurança.

Para ambos os modos, os usuários que já registraram um método que pode ser usado para autenticação multifatorial precisarão realizar a Autenticação Multifatorial antes que possam acessar suas informações de segurança.

### <a name="interrupt-mode"></a>Modo de interrupção

O registro combinado respeita tanto as políticas de Autenticação Multifatorial quanto ssPR, se ambas estiverem habilitadas para o seu inquilino. Essas políticas controlam se um usuário é interrompido para o registro durante o login e quais métodos estão disponíveis para registro.

Aqui estão vários cenários em que os usuários podem ser solicitados a registrar ou atualizar suas informações de segurança:

- Registro de autenticação multifatorial aplicado através da Proteção de Identidade: Os usuários devem se registrar durante o login. Eles registram métodos de autenticação multifatorial e métodos SSPR (se o usuário estiver habilitado para SSPR).
- Registro de autenticação multifatorial aplicado através da autenticação multifatorial por usuário: os usuários devem se registrar durante o login. Eles registram métodos de autenticação multifatorial e métodos SSPR (se o usuário estiver habilitado para SSPR).
- Registro de autenticação multifatorial aplicado através do Acesso Condicional ou outras políticas: Os usuários são solicitados a se registrar quando usam um recurso que requer autenticação multifatorial. Eles registram métodos de autenticação multifatorial e métodos SSPR (se o usuário estiver habilitado para SSPR).
- Registro SSPR aplicado: Os usuários devem se registrar durante o login. Eles registram apenas métodos SSPR.
- Atualização SSPR aplicada: Os usuários são obrigados a revisar suas informações de segurança em um intervalo definido pelo admin. Os usuários são mostrados suas informações e podem confirmar as informações atuais ou fazer alterações, se necessário.

Quando o registro é aplicado, os usuários são mostrados o número mínimo de métodos necessários para estar em conformidade com as políticas de Autenticação Multifatorial e SSPR, da maioria para menos seguras.

Por exemplo:

- Um usuário está habilitado para SSPR. A política sspr exigia dois métodos para redefinir e habilitou código de aplicativo móvel, e-mail e telefone.
   - Este usuário é obrigado a registrar dois métodos.
      - O usuário é mostrado aplicativo autenticador e telefone por padrão.
      - O usuário pode optar por registrar e-mail em vez de aplicativo autenticador ou telefone.

Este fluxograma descreve quais métodos são mostrados a um usuário quando interrompidos para registrar durante o login:

![Fluxograma de informações de segurança combinada](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se você tiver a Autenticação Multifatorial e o SSPR ativados, recomendamos que você imponha o registro de Autenticação Multifatorial.

Se a política SSPR exigir que os usuários revisem suas informações de segurança em intervalos regulares, os usuários serão interrompidos durante o login e mostrarão todos os seus métodos registrados. Eles podem confirmar as informações atuais se está atualizada, ou podem fazer alterações se precisarem. Os usuários devem realizar a autenticação multifatorial ao acessar esta página.

### <a name="manage-mode"></a>Modo gerenciar

Os usuários podem acessar [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) o modo de gerenciamento indo ou selecionando informações de **segurança** do Meu perfil. A partir daí, os usuários podem adicionar métodos, excluir ou alterar métodos existentes, alterar o método padrão e muito mais.

## <a name="key-usage-scenarios"></a>Principais cenários de uso

### <a name="set-up-security-info-during-sign-in"></a>Configurar informações de segurança durante a entrada

Um admin impôs o registro.

Um usuário não configurou todas as informações de segurança necessárias e vai para o portal Azure. Depois de inserir o nome de usuário e a senha, o usuário é solicitado a configurar informações de segurança. Em seguida, o usuário segue as etapas mostradas no assistente para configurar as informações de segurança necessárias. Se suas configurações permitirem, o usuário pode optar por configurar métodos diferentes daqueles mostrados por padrão. Após completar o assistente, os usuários revisam os métodos configurados e seu método padrão para autenticação multifatorial. Para concluir o processo de configuração, o usuário confirma as informações e continua no portal Azure.

### <a name="set-up-security-info-from-my-profile"></a>Configure informações de segurança do Meu Perfil

Um admin não impôs o registro.

Um usuário que ainda não configurou todas [https://myprofile.microsoft.com](https://myprofile.microsoft.com)as informações de segurança necessárias vai para . O usuário seleciona **informações de segurança** no painel esquerdo. A partir daí, o usuário opta por adicionar um método, seleciona qualquer um dos métodos disponíveis e segue as etapas para configurar esse método. Quando concluído, o usuário vê o método que foi configurado na página De informações de segurança.

### <a name="delete-security-info-from-my-profile"></a>Excluir informações de segurança do Meu Perfil

Um usuário que já configurou pelo menos [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)um método navega para . O usuário opta por excluir um dos métodos previamente registrados. Quando concluído, o usuário não vê mais esse método na página de informações de segurança.

### <a name="change-the-default-method-from-my-profile"></a>Alterar o método padrão do Meu Perfil

Um usuário que já configurou pelo menos um método que pode ser [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)usado para navegar por autenticação multifatorial para . O usuário altera o método padrão atual para um método padrão diferente. Quando concluído, o usuário vê o novo método padrão na página Informações de segurança.

## <a name="next-steps"></a>Próximas etapas

Para começar, consulte os tutoriais para ativar a [redefinição de senha de autoatendimento](tutorial-enable-sspr.md) e [ativar a autenticação multifatorial do Azure](tutorial-enable-azure-mfa.md).

Saiba como [ativar o registro combinado em seu inquilino](howto-registration-mfa-sspr-combined.md) ou forçar os usuários a [recadastrar métodos de autenticação](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Você também pode rever os [métodos disponíveis para autenticação multifatorial do Azure e SSPR](concept-authentication-methods.md).
