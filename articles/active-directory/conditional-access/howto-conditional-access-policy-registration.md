---
title: Acesso Condicional - Informações de segurança combinadas - Azure Active Directory
description: Crie uma política de acesso condicional personalizado para registro de informações de segurança
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8081bb8145a6654c168fb2d664e1666b32dc18
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457902"
---
# <a name="conditional-access-securing-security-info-registration"></a>Acesso Condicional: Garantindo o registro de informações de segurança

Garantir quando e como os usuários se registram para autenticação multifatorial do Azure e redefinição de senha de autoatendimento agora é possível com ações do usuário na política de Acesso Condicional. Este recurso de visualização está disponível para organizações que habilitaram a [pré-visualização de registro combinado](../authentication/concept-registration-mfa-sspr-combined.md). Essa funcionalidade pode ser habilitada em organizações onde eles desejam usar condições como localização de rede confiável para restringir o acesso ao registro de Autenticação Multifatorial do Azure e redefinição de senha de autoatendimento (SSPR). Para obter mais informações sobre condições utilizáveis, consulte o artigo [Acesso Condicional: Condições](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Crie uma diretiva para exigir o registro de um local confiável

A política a seguir se aplica a todos os usuários selecionados, que tentam se registrar usando a experiência de registro combinada e bloqueiam o acesso, a menos que estejam se conectando a partir de um local marcado como rede confiável.

1. No **portal Azure,** navegue até o**Acesso Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Em Nome, Digite um Nome para esta política. Por exemplo, **registro combinado de informações de segurança em redes confiáveis**.
1. Em **Atribuições,** selecione **Usuários e grupos**e selecione os usuários e grupos a que deseja que essa política se aplique.

   > [!WARNING]
   > Os usuários devem estar habilitados para o [registro combinado](../authentication/howto-registration-mfa-sspr-combined.md).

1. Em **aplicativos ou ações na Nuvem,** selecione ações do **usuário,** verifique **as informações de segurança do Registro**.
1. Em **Condições** > **Localizações**.
   1. Configure **Sim**.
   1. Inclua **qualquer local**.
   1. Excluir **todos os locais confiáveis**.
   1. Selecione **Feito** na lâmina Locais.
   1. Selecione **Feito** na lâmina Condições.
1. Em **Condições,** > **aplicativos do cliente (Visualização)**, **configure Configuração** para **Sim**e selecione **Feito**.
1. Sob **controles de** > **acesso, Grant**.
   1. Seleione **Bloquear acesso**.
   1. Em seguida, clique em **Selecionar**.
1. Defina **Habilitar política** como **Ativado**.
1. Em seguida, **selecione Salvar**.

Na etapa 6 desta política, as organizações têm escolhas que podem fazer. A política acima requer registro de um local de rede confiável. As organizações podem optar por utilizar quaisquer condições disponíveis no lugar de **Locais.** Lembre-se de que esta política é uma política de bloco, então qualquer coisa incluída é bloqueada e qualquer coisa que não corresponda à inclusão é permitida. 

Alguns podem optar por usar o estado do dispositivo em vez de localização na etapa 6 acima:

6. Em **Condições** > **Estado do dispositivo (Visualização)**.
   1. Configure **Sim**.
   1. Inclua **todos os estados do dispositivo**.
   1. Exclua **dispositivo Híbrido Azure AD unido** e/ou dispositivo marcado como **compatível**
   1. Selecione **Feito** na lâmina Locais.
   1. Selecione **Feito** na lâmina Condições.

> [!WARNING]
> Se você usar o estado do dispositivo como uma condição em sua política, isso pode impactar os usuários convidados no diretório. [O modo somente relatório](concept-conditional-access-report-only.md) pode ajudar a determinar o impacto das decisões políticas.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determine o impacto usando o modo somente de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simule o comportamento do sinal usando a ferramenta Acesso Condicional E se](troubleshoot-conditional-access-what-if.md)
