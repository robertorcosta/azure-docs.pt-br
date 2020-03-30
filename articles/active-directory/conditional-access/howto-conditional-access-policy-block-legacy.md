---
title: Acesso Condicional - Autenticação do legado do bloco - Diretório Ativo do Azure
description: Crie uma política de acesso condicional personalizado para bloquear protocolos de autenticação legado
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295226"
---
# <a name="conditional-access-block-legacy-authentication"></a>Acesso Condicional: Bloqueie a autenticação do legado

Devido ao risco aumentado associado aos protocolos de autenticação legados, a Microsoft recomenda que as organizações bloqueiem as solicitações de autenticação usando esses protocolos e exijam autenticação moderna.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para bloquear solicitações de autenticação legado. Essa política é colocada no [modo somente relatório](howto-conditional-access-report-only.md) para iniciar para que os administradores possam determinar o impacto que terão nos usuários existentes. Quando os administradores estão confortáveis de que a política se aplique como pretendem, eles podem mudar para **On** ou encenar a implantação adicionando grupos específicos e excluindo outros.

1. Faça login no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições,** selecione **Usuários e grupos**
   1. Em **Incluir,** selecione **Todos os usuários**.
   1. Em **Exclusão,** selecione **Usuários e grupos** e escolha quaisquer contas que devem manter a capacidade de usar autenticação legado. Exclua pelo menos uma conta para evitar que você seja bloqueado. Se você não excluir nenhuma conta, você não será capaz de criar esta política.
   1. Selecione **Feito**.
1. Em **aplicativos ou ações na Nuvem,** selecione Todos os aplicativos em **nuvem**.
   1. Selecione **Feito**.
1. Em **Condições,** > **aplicativos do cliente (visualização)**, defina **Configurar** como **Sim**.
   1. Verifique apenas as caixas **Aplicativos móveis e clientes** > de desktop**Outros clientes**.
   1. Selecione **Feito**.
1. Em **Controles de** > **acesso, grant,** selecione **Bloquear acesso**.
   1. Selecione **Selecionar**.
1. Confirme suas configurações e **configure Ativar** a diretiva para **somente relatório**.
1. Selecione **Criar** para criar para ativar sua política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determine o impacto usando o modo somente de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simule o comportamento do sinal usando a ferramenta Acesso Condicional E se](troubleshoot-conditional-access-what-if.md)
