---
title: Comece com o registro combinado - Azure Active Directory
description: Habilite o registro combinado de autenticação multifatorial AD AD e o registro de redefinição de senha de autoatendimento
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466b063253ee49ab58c2685f359b4bb8a4079532
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639687"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Habilite o registro combinado de informações de segurança no Azure Active Directory

Antes do registro combinado, os usuários registraram métodos de autenticação para autenticação multifatorial do Azure e redefinição de senha de autoatendimento (SSPR) separadamente. As pessoas estavam confusas de que métodos semelhantes foram usados para autenticação multifatorial e SSPR, mas tiveram que se registrar para ambos os recursos. Agora, com o registro combinado, os usuários podem se cadastrar uma vez e obter os benefícios tanto da Autenticação Multifatorial quanto do SSPR.

Antes de habilitar a nova experiência, revise o artigo [Registro combinado de informações](concept-registration-mfa-sspr-combined.md) de segurança para garantir que você entenda a funcionalidade e os efeitos desse recurso.

![Experiência aprimorada de registro de informações de segurança combinada](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Habilitar o registro combinado

Complete estas etapas para habilitar o registro combinado:

1. Faça login no portal Azure como administrador de usuários ou administrador global.
2. Ir para > **configurações** > do usuário do **diretório ativo do Azure****Gerenciar configurações de visualização de recursos do usuário**.
3. Em **Usuários, os usuários podem usar recursos de visualização para registrar e gerenciar informações de segurança,** optar por habilitar para um grupo **selecionado** de usuários ou para **todos os** usuários.

   ![Habilite a experiência combinada de visualização de informações de segurança para todos os usuários](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> Depois de habilitar o registro combinado, os usuários que se registrarem ou confirmarem seu número de telefone ou aplicativo móvel através da nova experiência podem usá-los para autenticação multifatorial e SSPR, se esses métodos estiverem habilitados nas políticas de Autenticação Multifatorial e SSPR. Se você desativar essa experiência, os usuários que acessarem `https://aka.ms/ssprsetup` a página de registro sspr anterior serão obrigados a realizar a autenticação multifatorial antes que eles possam acessar a página.

Se você configurou a Lista de atribuição de site para região no Internet Explorer, os seguintes sites devem estar na mesma região:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Políticas de Acesso Condicional para registro combinado

Garantir quando e como os usuários se registram para autenticação multifatorial do Azure e redefinição de senha de autoatendimento agora é possível com ações do usuário na política de Acesso Condicional. Esse recurso está disponível para organizações que habilitaram o [recurso de registro combinado](../authentication/concept-registration-mfa-sspr-combined.md). Essa funcionalidade pode ser habilitada em organizações onde eles desejam que os usuários se registrem para autenticação multifatorial e SSPR do Azure de um local central, como um local de rede confiável durante o onboarding de RH.

Para obter mais informações sobre a criação de locais confiáveis no Acesso Condicional, consulte o artigo [Qual é a condição de localização no Azure Active Directory Conditional Access?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Crie uma diretiva para exigir o registro de um local confiável

A política a seguir se aplica a todos os usuários selecionados que tentam se registrar usando a experiência de registro combinada e bloqueia o acesso, a menos que estejam se conectando a partir de um local marcado como rede confiável.

1. No **portal Azure,** navegue até o**Acesso Condicional de** **Segurança** > do Diretório >  **Ativo do Azure**
1. Selecione **+ Nova política**
1. Digite um nome para esta diretiva, como *Registro combinado de informações de segurança em redes confiáveis*.
1. Em **Atribuições**, selecione **Usuários e grupos**. Escolha os usuários e grupos a que deseja que esta política se aplique e selecione **'Feito ''Feito ''Feito'.**

   > [!WARNING]
   > Os usuários devem ser habilitados para o registro combinado.

1. Em **aplicativos ou ações na Nuvem,** selecione ações do **Usuário**. Verifique **as informações de segurança do Registro**e selecione **"Feito".**

    ![Crie uma política de acesso condicional para controlar o registro de informações de segurança](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Em Condições**Localizações,** configure as **seguintes** > opções:
   1. Configurar **Sim**
   1. Incluir **qualquer local**
   1. Excluir **todos os locais confiáveis**
1. Selecione **'Feito** na janela *Locais',* em seguida, **selecione Feito** na janela *Condições.*
1. Em **Acesso controla** > **conceder,** escolha **bloquear acesso**e, em seguida, **selecionar**
1. Definir **ativar a política** para **on**
1. Para finalizar a diretiva, selecione **Criar**

## <a name="next-steps"></a>Próximas etapas

Se você precisar de ajuda, veja como [solucionar o registro combinado de informações de segurança](howto-registration-mfa-sspr-combined-troubleshoot.md) ou aprender Qual é a condição de localização no [Azure Active Directory Conditional Access?](../conditional-access/location-condition.md)

Para habilitar os recursos no seu inquilino Azure AD, consulte os tutoriais para ativar a [redefinição de senha de autoatendimento](tutorial-enable-sspr.md) e [ativar a autenticação multifatorial do Azure](tutorial-enable-azure-mfa.md).

Saiba como [ativar o registro combinado em seu inquilino](howto-registration-mfa-sspr-combined.md) ou forçar os usuários a [recadastrar métodos de autenticação](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Você também pode rever os [métodos disponíveis para autenticação multifatorial do Azure e SSPR](concept-authentication-methods.md).
