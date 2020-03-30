---
title: Construindo uma política de acesso condicional - Azure Active Directory
description: Quais são todas as opções disponíveis para construir uma política de Acesso Condicional e o que significam?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 907ad8705742e4b2e38b13c3c675ebd333bd27d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295318"
---
# <a name="building-a-conditional-access-policy"></a>Construindo uma política de acesso condicional

Como explicado no artigo [What is Conditional Access](overview.md), uma política de Acesso Condicional é uma declaração if-então, de controles de **Atribuições** e **Acesso.** Uma política de Acesso Condicional reúne sinais, para tomar decisões e impor políticas organizacionais.

Como uma organização cria essas políticas? O que é necessário?

![Acesso Condicional (Sinais + Decisões + Execução = Políticas)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Atribuições

A parte de atribuições controla quem, o quê e onde da política de Acesso Condicional.

### <a name="users-and-groups"></a>Usuários e grupos

[Usuários e grupos](concept-conditional-access-users-groups.md) atribuem quem a política incluirá ou excluirá. Essa atribuição pode incluir todos os usuários, grupos específicos de usuários, funções de diretório ou usuários convidados externos. 

### <a name="cloud-apps-or-actions"></a>Aplicativos na nuvem ou ações

[Aplicativos ou ações em](concept-conditional-access-cloud-apps.md) nuvem podem incluir ou excluir aplicativos em nuvem ou ações de usuários que estarão sujeitas à política.

### <a name="conditions"></a>Condições

Uma política pode conter várias [condições](concept-conditional-access-conditions.md).

#### <a name="sign-in-risk"></a>Risco de entrada

Para organizações com [proteção de identidade Azure AD,](../identity-protection/overview.md)as detecções de risco geradas lá podem influenciar suas políticas de Acesso Condicional.

#### <a name="device-platforms"></a>Plataformas de dispositivo

Organizações com várias plataformas de sistema operacional de dispositivos podem querer aplicar políticas específicas em diferentes plataformas. 

As informações usadas para calcular a plataforma do dispositivo vêm de fontes não verificadas, como strings de agente de usuário que podem ser alteradas.

#### <a name="locations"></a>Locais

Os dados de localização são fornecidos por dados de geolocalização IP. Os administradores podem optar por definir locais e optar por marcar alguns como confiáveis como aqueles para os locais de rede de sua organização.

#### <a name="client-apps"></a>Aplicativos cliente

Por padrão, as políticas de acesso condicional se aplicam a aplicativos de navegador, aplicativos móveis e clientes de desktop que suportam autenticação moderna. 

Essa condição de atribuição permite que as políticas de Acesso Condicional direcionem aplicativos clientes específicos que não usem autenticação moderna. Esses aplicativos incluem clientes Exchange ActiveSync, aplicativos de Office mais antigos que não usam autenticação moderna e protocolos de e-mail como IMAP, MAPI, POP e SMTP.

#### <a name="device-state"></a>Estado do dispositivo

Este controle é usado para excluir dispositivos que são híbridos Azure AD unidos, ou marcado como um compatível em Intune. Essa exclusão pode ser feita para bloquear dispositivos não gerenciados. 

## <a name="access-controls"></a>Controles de acesso

A parte de controles de acesso da política de acesso condicional controla como uma política é aplicada.

### <a name="grant"></a>Conceder

[Grant](concept-conditional-access-grant.md) fornece aos administradores um meio de aplicação de políticas onde eles podem bloquear ou conceder acesso.

#### <a name="block-access"></a>Acesso bloqueado

O acesso ao bloco faz exatamente isso, ele bloqueará o acesso sob as atribuições especificadas. O controle do bloco é poderoso e deve ser exercido com o conhecimento apropriado.

#### <a name="grant-access"></a>Conceder acesso

O controle de subvenção pode desencadear a aplicação de um ou mais controles. 

- Exigir autenticação multifatorial (Autenticação Multifatorial do Azure)
- Exigir que o dispositivo seja marcado como compatível (Intune)
- Exigir um dispositivo ingressado no Azure AD Híbrido
- Exigir um aplicativo cliente aprovado
- Requer política de proteção do aplicativo

Os administradores podem optar por exigir um dos controles anteriores ou todos os controles selecionados usando as seguintes opções. O padrão para vários controles é exigir tudo.

- Requerem todos os controles selecionados (controle e controle)
- Exija um dos controles selecionados (controle ou controle)

### <a name="session"></a>Session

[Controles de sessão](concept-conditional-access-session.md) podem limitar a experiência 

- Usar restrições de aplicativo impostas
   - Atualmente funciona apenas com o Exchange Online e o SharePoint Online.
      - Passa informações do dispositivo para permitir o controle da experiência concedendo acesso total ou limitado.
- Use o controle do aplicativo de acesso condicional
   - Usa sinais do Microsoft Cloud App Security para fazer coisas como: 
      - Bloqueie o download, o corte, a cópia e a impressão de documentos confidenciais.
      - Monitore o comportamento de sessão arriscado.
      - Requerem rotulagem de arquivos confidenciais.
- Freqüência de login
   - Capacidade de alterar o sinal padrão na freqüência para autenticação moderna.
- Sessão persistente do navegador
   - Permite que os usuários permaneçam conectado após o fechamento e reabertura da janela do navegador.

## <a name="simple-policies"></a>Políticas simples

Uma política de acesso condicional deve conter, no mínimo, o seguinte a ser aplicado:

- **Nome** da apólice.
- **Atribuições**
   - **Usuários e/ou grupos** para aplicar a política.
   - **Aplicativos ou ações** em nuvem para aplicar a política.
- **Controles de acesso**
   - **Controles de concessão** ou **bloqueio**

![Política de acesso condicional em branco](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

O artigo [Políticas de Acesso Condicional Comum](concept-conditional-access-policy-common.md) inclui algumas políticas que achamos que seriam úteis para a maioria das organizações.

## <a name="next-steps"></a>Próximas etapas

[Simule o comportamento do sinal usando a ferramenta Acesso Condicional E se](troubleshoot-conditional-access-what-if.md)

[Planejar uma implantação da Autenticação Multifator do Azure baseada em nuvem](../authentication/howto-mfa-getstarted.md)

[Gerenciamento da conformidade de dispositivos com a Intune](/intune/device-compliance-get-started)

[Microsoft Cloud App Security e Acesso Condicional](/cloud-app-security/proxy-intro-aad)
