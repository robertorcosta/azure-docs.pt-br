---
title: Acesso condicional – exigir MFA para administradores-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para exigir que os administradores executem a autenticação multifator
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80755219"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Acesso condicional: exigir MFA para administradores

Contas que são atribuídas direitos administrativos são direcionadas por invasores. Exigir a autenticação multifator (MFA) nessas contas é uma maneira fácil de reduzir o risco de comprometimento dessas contas.

A Microsoft recomenda que você exija MFA nas seguintes funções no mínimo:

* Administrador de cobrança
* Administrador de acesso condicional
* Administradores do Exchange
* Administrador global
* Administrador da assistência técnica (senha)
* Administrador de senha
* Administrador de segurança
* Administrador do SharePoint
* Administrador do usuário

As organizações podem optar por incluir ou excluir funções como se encontram adequadas.

## <a name="user-exclusions"></a>Exclusões de usuário

As políticas de acesso condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* Contas de **acesso de emergência** ou de **vidro** para impedir o bloqueio de conta em todo o locatário. No cenário improvável, todos os administradores são bloqueados de seu locatário, sua conta administrativa de acesso de emergência pode ser usada para fazer logon no locatário. siga as etapas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo [gerenciar contas de acesso de emergência no Azure ad](../users-groups-roles/directory-emergency-access.md).
* **Contas de serviço** e **entidades de serviço**, como a conta de sincronização de Azure ad Connect. As contas de serviço são contas não interativas que não estão ligadas a nenhum usuário específico. Normalmente, eles são usados por serviços de back-end que permitem acesso programático a aplicativos, mas também são usados para entrar em sistemas para fins administrativos. Contas de serviço como essas devem ser excluídas, pois a MFA não pode ser concluída programaticamente. Chamadas feitas por entidades de serviço não são bloqueadas pelo acesso condicional.
   * Se sua empresa tiver essas contas em uso em scripts ou código, considere substituí-las [por identidades gerenciadas](../managed-identities-azure-resources/overview.md). Como solução alternativa temporária, você pode excluir essas contas específicas da política de linha de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que as funções administrativas atribuídas executem a autenticação multifator.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **Security** > **acesso condicional**de segurança.
1. Selecione **nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **funções de diretório (versão prévia)** e escolha as seguintes funções no mínimo:
      * Administrador de Autenticação
      * Administrador de cobrança
      * Administrador de acesso condicional
      * Administradores do Exchange
      * Administrador global
      * Administrador de assistência técnica
      * Administrador de senha
      * Administrador de segurança
      * Administrador do SharePoint
      * Administrador do usuário
   1. Em **excluir**, selecione **usuários e grupos** e escolha o acesso de emergência da sua organização ou contas de vidro. 
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações** > **incluem**, selecione **todos os aplicativos de nuvem**e selecione **concluído**.
1. Em **condições** > **aplicativos de cliente (versão prévia)**, defina **Configurar** como **Sim**e selecione **concluído**.
1. Em **Access controls** > **concessão**de controles de acesso, selecione **conceder acesso**, **exigir autenticação multifator**e selecione **selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar para habilitar a política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)
