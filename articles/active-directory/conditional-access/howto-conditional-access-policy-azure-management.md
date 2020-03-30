---
title: Acesso Condicional - Exigir MFA para gerenciamento do Azure - Diretório Ativo do Azure
description: Crie uma política de acesso condicional personalizado para exigir autenticação de vários fatores para tarefas de gerenciamento do Azure
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
ms.openlocfilehash: c90566006868c817d977699c35f2213895f3fe70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295235"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Acesso Condicional: Exigir MFA para o gerenciamento do Azure

As organizações usam uma variedade de serviços do Azure e os gerenciam a partir de ferramentas baseadas no Azure Resource Manager, como:

* Portal do Azure
* Azure PowerShell
* CLI do Azure

Essas ferramentas podem fornecer acesso altamente privilegiado aos recursos, que podem alterar configurações de toda a assinatura, configurações de serviço e faturamento por assinatura. Para proteger esses recursos privilegiados, a Microsoft recomenda exigir autenticação multifatorial para qualquer usuário que acesse esses recursos.

## <a name="user-exclusions"></a>Exclusões de usuários

As políticas de acesso condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* **Acesso de emergência** ou **contas de vidro de quebra** para evitar o bloqueio de contas em todo o inquilino. No cenário improvável em que todos os administradores estão bloqueados do seu inquilino, sua conta administrativa de acesso de emergência pode ser usada para entrar no inquilino para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, [Gerenciar contas de acesso de emergência no Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Contas de** serviço e **princípios de serviço,** como a Conta Sincronizada do Azure AD Connect. Contas de serviço são contas não interativas que não estão vinculadas a nenhum usuário em particular. Eles são normalmente usados por serviços back-end e permitem acesso programático a aplicativos. As contas de serviço devem ser excluídas, uma vez que o MFA não pode ser concluído programáticamente.
   * Se sua empresa tiver essas contas em uso em scripts ou código, considere substituí-las [por identidades gerenciadas](../managed-identities-azure-resources/overview.md). Como uma solução temporária, você pode excluir essas contas específicas da política de linha de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que as funções administrativas atribuídas realizem autenticação multifatorial.

1. Faça login no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições,** selecione **Usuários e grupos**
   1. Em **Incluir,** selecione **Todos os usuários**.
   1. Em **Exclusão,** selecione **Usuários e grupos** e escolha o acesso de emergência ou contas de vidro de quebra de sua organização. 
   1. Selecione **Feito**.
1. Em **Aplicativos ou ações** > na**Nuvem, selecione** **Selecione aplicativos,** escolha **o Microsoft Azure Management**e **selecione Select** then **Done**.
1. Em **Condições,** > **aplicativos do cliente (Visualização)**, **configure Configuração** para **Sim**e selecione **Feito**.
1. Em **Controles de acesso,** > **Grant**selecione Acesso **a subvenção,** exija **autenticação multifatorial**e **selecione Selecionar**.
1. Confirme suas configurações e **configure Ativar a diretiva** **em**.
1. Selecione **Criar** para criar para ativar sua política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determine o impacto usando o modo somente de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simule o comportamento do sinal usando a ferramenta Acesso Condicional E se](troubleshoot-conditional-access-what-if.md)
