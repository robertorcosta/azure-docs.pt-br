---
title: Acesso Condicional - Exigir MFA para administradores - Azure Active Directory
description: Crie uma política de acesso condicional personalizado para exigir que os administradores realizem autenticação multifatorial
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90e8a8b0926575b5a40a8c0ca7820e31827434ec
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755219"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Acesso condicional: exigir MFA para administradores

As contas que têm direitos administrativos atribuídos são alvo de invasores. Exigir autenticação multifatorial (MFA) nessas contas é uma maneira fácil de reduzir o risco de que essas contas sejam comprometidas.

A Microsoft recomenda que você exija MFA nas seguintes funções no mínimo:

* Administrador de cobrança
* Administrador de acesso condicional
* Administrador do Exchange
* Administrador global
* Administrador do Helpdesk (Senha)
* Administrador de senha
* Administrador de segurança
* Administrador do SharePoint
* Administrador de usuários

As organizações podem optar por incluir ou excluir funções como acharem melhor.

## <a name="user-exclusions"></a>Exclusões de usuários

As políticas de acesso condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* **Acesso de emergência** ou **contas de vidro de quebra** para evitar o bloqueio de contas em todo o inquilino. No cenário improvável em que todos os administradores estão bloqueados do seu inquilino, sua conta administrativa de acesso de emergência pode ser usada para entrar no inquilino para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, [Gerenciar contas de acesso de emergência no Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Contas de** serviço e **princípios de serviço,** como a Conta Sincronizada do Azure AD Connect. Contas de serviço são contas não interativas que não estão vinculadas a nenhum usuário em particular. Eles são normalmente usados por serviços back-end que permitem acesso programático aos aplicativos, mas também são usados para fazer login em sistemas para fins administrativos. Contas de serviço como essas devem ser excluídas, uma vez que o MFA não pode ser concluído programáticamente. As chamadas feitas pelos diretores do serviço não são bloqueadas pelo Conditional Access.
   * Se sua empresa tiver essas contas em uso em scripts ou código, considere substituí-las [por identidades gerenciadas](../managed-identities-azure-resources/overview.md). Como uma solução temporária, você pode excluir essas contas específicas da política de linha de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que as funções administrativas atribuídas realizem autenticação multifatorial.

1. Faça login no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições,** selecione **Usuários e grupos**
   1. Em **Incluir,** selecione **Funções de diretório (visualização)** e escolha as seguintes funções no mínimo:
      * Administrador de Autenticação
      * Administrador de cobrança
      * Administrador de acesso condicional
      * Administrador do Exchange
      * Administrador global
      * Administrador de assistência técnica
      * Administrador de senha
      * Administrador de segurança
      * Administrador do SharePoint
      * Administrador de usuários
   1. Em **Exclusão,** selecione **Usuários e grupos** e escolha o acesso de emergência ou contas de vidro de quebra de sua organização. 
   1. Selecione **Feito**.
1. Em **Aplicativos ou ações** > na**Nuvem, selecione** **Todos os aplicativos em nuvem**e selecione **Done**.
1. Em **Condições,** > **aplicativos do cliente (Visualização)**, **configure Configuração** para **Sim**e selecione **Feito**.
1. Em **Controles de acesso,** > **Grant**selecione Acesso **a subvenção,** exija **autenticação multifatorial**e **selecione Selecionar**.
1. Confirme suas configurações e **configure Ativar a diretiva** **em**.
1. Selecione **Criar** para criar para ativar sua política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determine o impacto usando o modo somente de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simule o comportamento do sinal usando a ferramenta Acesso Condicional E se](troubleshoot-conditional-access-what-if.md)
