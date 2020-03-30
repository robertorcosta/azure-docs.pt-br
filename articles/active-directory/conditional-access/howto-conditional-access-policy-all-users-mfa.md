---
title: Acesso Condicional - Exigir MFA para todos os usuários - Azure Active Directory
description: Crie uma política de acesso condicional personalizado para exigir que todos os usuários realizem autenticação multifatorial
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
ms.openlocfilehash: ae67a9e90b4d18829fcbc17262258d108752575a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295245"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Acesso Condicional: Exigir MFA para todos os usuários

Como Alex Weinert, o Diretório de Segurança de Identidade da Microsoft, menciona em seu post no blog [Seu Pa$$word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> Sua senha não importa, mas mfa faz! Com base em nossos estudos, sua conta tem mais de 99,9% menos chances de ser comprometida se você usar MFA.

A orientação neste artigo ajudará sua organização a criar uma política de MFA equilibrada para o seu ambiente.

## <a name="user-exclusions"></a>Exclusões de usuários

As políticas de acesso condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* **Acesso de emergência** ou **contas de vidro de quebra** para evitar o bloqueio de contas em todo o inquilino. No cenário improvável em que todos os administradores estão bloqueados do seu inquilino, sua conta administrativa de acesso de emergência pode ser usada para entrar no inquilino para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, [Gerenciar contas de acesso de emergência no Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Contas de** serviço e **princípios de serviço,** como a Conta Sincronizada do Azure AD Connect. Contas de serviço são contas não interativas que não estão vinculadas a nenhum usuário em particular. Eles são normalmente usados por serviços back-end e permitem acesso programático a aplicativos. As contas de serviço devem ser excluídas, uma vez que o MFA não pode ser concluído programáticamente.
   * Se sua empresa tiver essas contas em uso em scripts ou código, considere substituí-las [por identidades gerenciadas](../managed-identities-azure-resources/overview.md). Como uma solução temporária, você pode excluir essas contas específicas da política de linha de base.

## <a name="application-exclusions"></a>Exclusões de aplicativos

As organizações podem ter muitos aplicativos em nuvem em uso. Nem todos esses aplicativos podem exigir igual segurança. Por exemplo, a folha de pagamento e os aplicativos de atendimento podem exigir MFA, mas o refeitório provavelmente não. Os administradores podem optar por excluir aplicativos específicos de sua política.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que as funções administrativas atribuídas realizem autenticação multifatorial.

1. Faça login no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições,** selecione **Usuários e grupos**
   1. Em **Incluir,** selecione **Todos os usuários**
   1. Em **Exclusão,** selecione **Usuários e grupos** e escolha o acesso de emergência ou contas de vidro de quebra de sua organização. 
   1. Selecione **Feito**.
1. Em **Aplicativos ou ações** > na**Nuvem, selecione** **Todos os aplicativos em nuvem.**
   1. Em **Exclusão,** selecione quaisquer aplicativos que não exijam autenticação multifatorial.
1. Em **Condições,** > **aplicativos do cliente (Visualização)**, **configure Configuração** para **Sim**e selecione **Feito**.
1. Em **Controles de acesso,** > **Grant**selecione Acesso **a subvenção,** exija **autenticação multifatorial**e **selecione Selecionar**.
1. Confirme suas configurações e **configure Ativar a diretiva** **em**.
1. Selecione **Criar** para criar para ativar sua política.

### <a name="named-locations"></a>Localizações nomeadas

As organizações podem optar por incorporar locais de rede conhecidos como **locais nomeados** às suas políticas de Acesso Condicional. Esses locais nomeados podem incluir redes IPv4 confiáveis como aquelas para uma localização do escritório principal. Para obter mais informações sobre a configuração de locais nomeados, consulte o artigo [Qual é a condição de localização no Azure Active Directory Conditional Access?](location-condition.md)

Na política de exemplo acima, uma organização pode optar por não exigir autenticação multifatorial se acessar um aplicativo em nuvem de sua rede corporativa. Neste caso, eles poderiam adicionar a seguinte configuração à diretiva:

1. Em **Atribuições,** selecione**Locais** **de Condições** > .
   1. Configure **Sim**.
   1. Inclua **qualquer local**.
   1. Excluir **todos os locais confiáveis**.
   1. Selecione **Feito**.
1. Selecione **Feito**.
1. **Salve** suas alterações de política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determine o impacto usando o modo somente de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simule o comportamento do sinal usando a ferramenta Acesso Condicional E se](troubleshoot-conditional-access-what-if.md)
