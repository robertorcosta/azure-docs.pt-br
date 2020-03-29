---
title: Comece com o registro combinado - Azure Active Directory
description: Habilitar autenticação multifatorial ad ad combinada e redefinição de senha de autoatendimento (visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25c64d9e959b1d68de23e83e26d3495bd3939986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425175"
---
# <a name="enable-combined-security-information-registration-preview"></a>Habilite o registro combinado de informações de segurança (visualização)

Antes de habilitar a nova experiência, revise o artigo [Registro combinado de informações de segurança (visualização)](concept-registration-mfa-sspr-combined.md) para garantir que você entenda a funcionalidade e os efeitos desse recurso.

![Experiência aprimorada de registro de informações de segurança combinada](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| O registro combinado de informações de segurança para autenticação multifatorial do Azure e redefinição de senha do Azure Active Directory (Azure AD) é um recurso de visualização pública do Azure AD. Para obter mais informações sobre visualizações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> As organizações que habilitaram a pré-visualização anterior para registrar e gerenciar informações de segurança devem concluir as etapas abaixo para habilitar a experiência de visualização aprimorada. Para as organizações que não fizerem a troca, em 8 de outubro de 2019, a Microsoft mudará os usuários da pré-visualização anterior para registrar e gerenciar informações de segurança para a experiência aprimorada. 
> 
> Se você não tiver ativado nenhuma versão da visualização, sua organização não será impactada.

## <a name="enable-combined-registration"></a>Habilitar o registro combinado

Complete estas etapas para habilitar o registro combinado:

1. Faça login no portal Azure como administrador de usuários ou administrador global.
2. Ir para > **configurações** > do usuário do **diretório ativo do Azure****Gerenciar configurações de visualização de recursos do usuário**.
3. Em **Usuários, os usuários podem usar recursos de visualização para registrar e gerenciar informações de segurança,** optar por habilitar para um grupo **selecionado** de usuários ou para **todos os** usuários.

   ![Habilite a experiência combinada de visualização de informações de segurança para todos os usuários](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamadas telefônicas não estarão disponíveis para usuários de Autenticação Multifatorial e SSPR em inquilinos AD gratuitos/com teste. As mensagens SMS não são afetadas por essa alteração. As opções de chamadas telefônicas ainda estarão disponíveis para os usuários em inquilinos ad pagos do Azure.

> [!NOTE]
> Depois de habilitar o registro combinado, os usuários que se registrarem ou confirmarem seu número de telefone ou aplicativo móvel através da nova experiência podem usá-los para autenticação multifatorial e SSPR, se esses métodos estiverem habilitados na Autenticação Multifatorial e SSPR Políticas. Se você desativar essa experiência, os usuários que acessarem `https://aka.ms/ssprsetup` a página de registro sspr anterior serão obrigados a realizar a autenticação multifatorial antes que eles possam acessar a página.

Se você configurou a Lista de atribuição de site para região no Internet Explorer, os seguintes sites devem estar na mesma região:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Políticas de Acesso Condicional para registro combinado

Garantir quando e como os usuários se registram para autenticação multifatorial do Azure e redefinição de senha de autoatendimento agora é possível com ações do usuário na política de Acesso Condicional. Este recurso de visualização está disponível para organizações que habilitaram a [pré-visualização de registro combinado](../authentication/concept-registration-mfa-sspr-combined.md). Essa funcionalidade pode ser habilitada em organizações onde eles desejam que os usuários se registrem para autenticação multifatorial e SSPR do Azure de um local central, como um local de rede confiável durante o onboarding de RH. Para obter mais informações sobre a criação de locais confiáveis no Acesso Condicional, consulte o artigo [Qual é a condição de localização no Azure Active Directory Conditional Access?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Crie uma diretiva para exigir o registro de um local confiável

A política a seguir se aplica a todos os usuários selecionados, que tentam se registrar usando a experiência de registro combinada e bloqueiam o acesso, a menos que estejam se conectando a partir de um local marcado como rede confiável.

![Crie uma política de CA para controlar o registro de informações de segurança](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. No **portal Azure,** navegue até o**Acesso Condicional de** **Segurança** > do Diretório >  **Ativo do Azure**
1. Selecione **Nova política**
1. Em Nome, Digite um Nome para esta política. Por exemplo, **registro combinado de informações de segurança em redes confiáveis**
1. Em **Atribuições,** clique **em Usuários e grupos**e selecione os usuários e grupos que deseja que esta política se aplique a

   > [!WARNING]
   > Os usuários devem estar habilitados para a [visualização de registro combinado](../authentication/howto-registration-mfa-sspr-combined.md).

1. Em **aplicativos ou ações na Nuvem,** selecione ações do **usuário,** verifique **as informações de segurança do registro (visualização)**
1. Em **Condições** > **Localizações**
   1. Configurar **Sim**
   1. Incluir **qualquer local**
   1. Excluir **todos os locais confiáveis**
   1. Clique em **Feito** na lâmina Locais
   1. Clique em **Feito** na lâmina Condições
1. **controles de** > **acesso, Grant**
   1. Clique **em Bloquear o acesso**
   1. Em seguida, clique em **Selecionar**
1. Definir **ativar a política** para **on**
1. Em seguida, clique em **Criar**

## <a name="next-steps"></a>Próximas etapas

[Forçar os usuários a recadastrar métodos de autenticação](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Métodos disponíveis para autenticação multifatorial e SSPR](concept-authentication-methods.md)

[Configurar a redefinição de senha de autoatendimento](howto-sspr-deployment.md)

[Configurar a Autenticação Multifator do Azure](howto-mfa-getstarted.md)

[Solução de problemas registro combinado de informações de segurança](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Qual é a condição de localização no Azure Active Directory Conditional Access?](../conditional-access/location-condition.md)
