---
title: Introdução ao registro combinado-Azure Active Directory
description: Habilitar a autenticação multifator do Azure AD combinada e o registro de redefinição de senha de autoatendimento
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639687"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Habilitar o registro de informações de segurança combinadas no Azure Active Directory

Antes do registro combinado, os usuários registraram os métodos de autenticação para a autenticação multifator do Azure e a SSPR (redefinição de senha de autoatendimento) separadamente. As pessoas foram confundidas que métodos semelhantes eram usados para autenticação multifator e SSPR, mas tinham que se registrar para ambos os recursos. Agora, com o registro combinado, os usuários podem registrar uma vez e obter os benefícios da autenticação multifator e do SSPR.

Antes de habilitar a nova experiência, leia o artigo [registro de informações de segurança combinado](concept-registration-mfa-sspr-combined.md) para garantir que você compreenda a funcionalidade e os efeitos desse recurso.

![Experiência avançada de registro de informações de segurança combinada](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Habilitar o registro combinado

Conclua estas etapas para habilitar o registro combinado:

1. Entre no portal do Azure como um administrador de usuário ou administrador global.
2. Vá para **Azure Active Directory** > configurações de**usuário** > **gerenciar configurações de visualização de recurso do usuário**.
3. Em **os usuários podem usar os recursos de visualização para registrar e gerenciar informações de segurança**, escolha habilitar para um grupo de usuários **selecionado** ou para **todos** os usuários.

   ![Habilitar a experiência de visualização de informações de segurança combinadas para todos os usuários](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> Depois que você habilitar o registro combinado, os usuários que registrarem ou confirmarem seu número de telefone ou aplicativo móvel por meio da nova experiência poderão usá-los para autenticação multifator e SSPR, se esses métodos estiverem habilitados na autenticação multifator e nas políticas de SSPR. Se você desabilitar essa experiência, os usuários que vão para a página de registro SSPR anterior `https://aka.ms/ssprsetup` em serão necessários para executar a autenticação multifator antes de poderem acessar a página.

Se você tiver configurado a lista de atribuição de site a zona no Internet Explorer, os seguintes sites devem estar na mesma zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Políticas de acesso condicional para registro combinado

A proteção de quando e como os usuários se registram para a autenticação multifator do Azure e a redefinição de senha de autoatendimento agora são possíveis com as ações do usuário na política de acesso condicional. Esse recurso está disponível para organizações que habilitaram o [recurso de registro combinado](../authentication/concept-registration-mfa-sspr-combined.md). Essa funcionalidade pode ser habilitada em organizações em que eles desejam que os usuários se registrem para a autenticação multifator do Azure e SSPR de um local central, como um local de rede confiável durante a integração de RH.

Para obter mais informações sobre como criar locais confiáveis no acesso condicional, consulte o artigo [qual é a condição de local em Azure Active Directory acesso condicional?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registro de um local confiável

A política a seguir se aplica a todos os usuários selecionados que tentam se registrar usando a experiência de registro combinada e bloqueia o acesso, a menos que eles estejam se conectando de um local marcado como rede confiável.

1. Na **portal do Azure**, navegue até **Azure Active Directory** > **Security** > **acesso condicional** de segurança
1. Selecionar **+ nova política**
1. Insira um nome para essa política, como *registro de informações de segurança combinadas em redes confiáveis*.
1. Em **Atribuições**, selecione **Usuários e Grupos**. Escolha os usuários e grupos aos quais você deseja que essa política seja aplicada e, em seguida, selecione **concluído**.

   > [!WARNING]
   > Os usuários devem estar habilitados para registro combinado.

1. Em **aplicativos de nuvem ou ações**, selecione **ações do usuário**. Marque **registrar informações de segurança**e, em seguida, selecione **concluído**.

    ![Criar uma política de acesso condicional para controlar o registro de informações de segurança](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Em **Conditions** > **locais**de condições, configure as seguintes opções:
   1. Configurar **Sim**
   1. Incluir **qualquer local**
   1. Excluir **todos os locais confiáveis**
1. Selecione **concluído** na janela *locais* e, em seguida, selecione **concluído** na janela *condições* .
1. Em **Access controls** > **concessão**de controles de acesso, escolha **bloquear acesso**e, em seguida, **selecione**
1. Definir **habilitar política** como **ativado**
1. Para finalizar a política, selecione **criar**

## <a name="next-steps"></a>Próximas etapas

Se precisar de ajuda, consulte como [solucionar problemas de registro de informações de segurança combinadas](howto-registration-mfa-sspr-combined-troubleshoot.md) ou saiba [qual é a condição de localização em Azure Active Directory acesso condicional?](../conditional-access/location-condition.md)

Para habilitar os recursos em seu locatário do Azure AD, consulte os tutoriais para [habilitar a redefinição de senha de autoatendimento](tutorial-enable-sspr.md) e [habilitar a autenticação multifator do Azure](tutorial-enable-azure-mfa.md).

Saiba como [habilitar o registro combinado em seu locatário](howto-registration-mfa-sspr-combined.md) ou [forçar os usuários a registrar novamente os métodos de autenticação](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Você também pode examinar os [métodos disponíveis para a autenticação multifator do Azure e o SSPR](concept-authentication-methods.md).
