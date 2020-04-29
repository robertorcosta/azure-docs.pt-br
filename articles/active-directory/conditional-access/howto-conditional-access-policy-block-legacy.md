---
title: Acesso condicional-bloquear autenticação herdada-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para bloquear protocolos de autenticação herdados
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
ms.openlocfilehash: 6a868c8199ac34a498a280e2522d6b1e4c7ec370
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295226"
---
# <a name="conditional-access-block-legacy-authentication"></a>Acesso condicional: bloquear autenticação herdada

Devido ao maior risco associado aos protocolos de autenticação herdados, a Microsoft recomenda que as organizações bloqueiem as solicitações de autenticação usando esses protocolos e exijam autenticação moderna.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para bloquear as solicitações de autenticação herdadas. Essa política é colocada no [modo somente de relatório](howto-conditional-access-report-only.md) para iniciar, para que os administradores possam determinar o impacto que eles terão sobre os usuários existentes. Quando os administradores se sentem confortáveis de que a política se aplica conforme pretendido, eles podem **alternar para ou preparar a implantação** adicionando grupos específicos e excluindo outros.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **Security** > **acesso condicional**de segurança.
1. Selecione **nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários**.
   1. Em **excluir**, selecione **usuários e grupos** e escolha as contas que devem manter a capacidade de usar a autenticação herdada. Exclua pelo menos uma conta para impedir que você mesmo fique bloqueada. Se você não excluir nenhuma conta, não será possível criar essa política.
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações**, selecione **todos os aplicativos de nuvem**.
   1. Selecione **Concluído**.
1. Em **condições** > **aplicativos de cliente (versão prévia)**, defina **Configurar** como **Sim**.
   1. Marque somente as > caixas **aplicativos móveis e clientes de área de trabalho****outros clientes**.
   1. Selecione **Concluído**.
1. Em **Access controls** > **concessão**de controles de acesso, selecione **bloquear acesso**.
   1. Selecione **Selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **somente relatório**.
1. Selecione **criar** para criar para habilitar a política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)
