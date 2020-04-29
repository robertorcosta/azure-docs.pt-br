---
title: Acesso condicional-informações de segurança combinadas-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para o registro de informações de segurança
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457902"
---
# <a name="conditional-access-securing-security-info-registration"></a>Acesso condicional: protegendo informações de segurança registro

A proteção de quando e como os usuários se registram para a autenticação multifator do Azure e a redefinição de senha de autoatendimento agora são possíveis com as ações do usuário na política de acesso condicional. Esse recurso de visualização está disponível para organizações que habilitaram a [visualização de registro combinado](../authentication/concept-registration-mfa-sspr-combined.md). Essa funcionalidade pode ser habilitada em organizações em que desejam usar condições como local de rede confiável para restringir o acesso ao registro para a autenticação multifator do Azure e a SSPR (redefinição de senha de autoatendimento). Para obter mais informações sobre condições utilizáveis, consulte o artigo [acesso condicional: condições](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registro de um local confiável

A política a seguir se aplica a todos os usuários selecionados, que tentam se registrar usando a experiência de registro combinada e bloqueia o acesso, a menos que eles estejam se conectando de um local marcado como rede confiável.

1. Na **portal do Azure**, navegue até **Azure Active Directory** > **Security** > **acesso condicional**de segurança.
1. Selecione **nova política**.
1. Em nome, insira um nome para essa política. Por exemplo, o **registro de informações de segurança combinadas em redes confiáveis**.
1. Em **atribuições**, selecione **usuários e grupos**e selecione os usuários e grupos aos quais você deseja que essa política se aplique.

   > [!WARNING]
   > Os usuários devem estar habilitados para o [registro combinado](../authentication/howto-registration-mfa-sspr-combined.md).

1. Em **aplicativos de nuvem ou ações**, selecione **ações do usuário**, marque **registrar informações de segurança**.
1. Em **Conditions** > **locais**de condições.
   1. Configurar **Sim**.
   1. Inclua **qualquer local**.
   1. Exclua **todos os locais confiáveis**.
   1. Selecione **concluído** na folha locais.
   1. Selecione **concluído** na folha condições.
1. Em **condições** > **aplicativos de cliente (versão prévia)**, defina **Configurar** como **Sim**e selecione **concluído**.
1. Em **Access controls** > **concessão**de controles de acesso.
   1. Seleione **Bloquear acesso**.
   1. Em seguida, clique em **Selecionar**.
1. Defina **Habilitar política** como **Ativado**.
1. Em seguida, selecione **salvar**.

Na etapa 6 desta política, as organizações têm opções que podem fazer. A política acima requer o registro de um local de rede confiável. As organizações podem optar por utilizar qualquer condição disponível em vez de **locais**. Lembre-se de que essa política é uma política de bloqueio para que tudo incluído seja bloqueado e qualquer coisa que não corresponda a include seja permitida. 

Alguns podem optar por usar o estado do dispositivo em vez do local na etapa 6 acima:

6. Em **condições** > **estado do dispositivo (versão prévia)**.
   1. Configurar **Sim**.
   1. Incluir **todo o estado do dispositivo**.
   1. Excluir dispositivo **híbrido do Azure ad ingressado** e/ou **dispositivo marcado como em conformidade**
   1. Selecione **concluído** na folha locais.
   1. Selecione **concluído** na folha condições.

> [!WARNING]
> Se você usar o estado do dispositivo como uma condição em sua política, isso poderá afetar os usuários convidados no diretório. O [modo somente de relatório](concept-conditional-access-report-only.md) pode ajudar a determinar o impacto das decisões de política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)
