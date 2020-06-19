---
title: Introdução ao registro combinado – Azure Active Directory
description: Habilitar o registro combinado para a Autenticação Multifator do Microsoft Azure AD e a SSPR
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
ms.openlocfilehash: 40266f1b340ebe0ab665c576ff3be0e62ba7c705
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798271"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Habilitar o registro combinado de informações de segurança no Azure Active Directory

Antes do registro combinado, os usuários registravam métodos de autenticação diferentes para a Autenticação Multifator do Microsoft Azure e a SSPR (redefinição de senha de autoatendimento) separadamente. As pessoas ficavam confusas porque métodos semelhantes eram usados para a Autenticação Multifator do Microsoft Azure e a SSPR, mas tinham que se registrar para ambos os recursos. Agora, com o registro combinado, os usuários podem registrar uma vez e obter os benefícios da Autenticação Multifator do Microsoft Azure e do SSPR.

Antes de habilitar a nova experiência, leia o artigo [Registro combinado de informações de segurança](concept-registration-mfa-sspr-combined.md) para garantir que você compreenda a funcionalidade e os efeitos desse recurso.

![Experiência aprimorada do registro combinado de informações de segurança](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Habilitar o registro combinado

Conclua estas etapas para habilitar o registro combinado:

1. Entre no portal do Azure como administrador global ou de usuários.
2. Acesse **Azure Active Directory** > **Configurações do usuário** > **Gerenciar as configurações de visualização de recursos do usuário**.
3. Em **Os usuários podem usar a experiência de registro combinado de informações de segurança**, opte por habilitar para um grupo **Seleto** de usuários ou **Todos** os usuários.

   ![Habilitar a experiência de informações de segurança combinadas para usuários](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> Depois de ativar o registro combinado, os usuários que se registrarem ou confirmarem seu número de telefone ou aplicativo móvel por meio da nova experiência poderão usá-los para a Autenticação Multifator do Microsoft Azure e a SSPR, se esses métodos estiverem ativados nas políticas da Autenticação Multifator do Microsoft Azure e da SSPR. Se você desabilitar essa experiência, os usuários que acessarem a página de registro anterior do SSPR em `https://aka.ms/ssprsetup` serão obrigados a executar a autenticação multifator antes que possam acessar a página.

Se você tiver configurado a Lista de Atribuições de Sites a Zonas no Internet Explorer, os seguintes sites deverão estar na mesma zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Políticas de acesso condicional para o registro combinado

A proteção de quando e como os usuários se registram para a Autenticação Multifator do Microsoft Azure e a redefinição de senha self-service agora é possível através de ações do usuário na política de acesso condicional. Esse recurso está disponível para as organizações que habilitaram o [recurso de registro combinado](../authentication/concept-registration-mfa-sspr-combined.md). Essa funcionalidade pode ser habilitada em organizações que desejam que os usuários se registrem para a Autenticação Multifator do Microsoft Azure e a SSPR de um local central, como um local de rede confiável durante a integração de RH.

Para mais informações sobre como criar locais confiáveis no Acesso Condicional, confira o artigo [O que é a condição de localização no Acesso Condicional do Azure Active Directory?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registro de um local confiável

A política a seguir se aplica a todos os usuários selecionados que tentam se registrar usando a experiência de registro combinado; ela bloqueia o acesso, a menos que eles estejam se conectando de um local marcado como rede confiável.

1. No **Portal do Azure**, navegue até **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **+ Nova política**.
1. Insira um nome para essa política, como *Registro combinado de informações de segurança em redes confiáveis*.
1. Em **Atribuições**, selecione **Usuários e grupos**. Escolha os usuários e os grupos aos quais você deseja que essa política seja aplicada. Depois, selecione **Concluído**.

   > [!WARNING]
   > Os usuários devem estar habilitados para registro combinado.

1. Em **Aplicativos ou ações de nuvem**, selecione **Ações do usuário**. Marque **Registrar informações de segurança** e selecione **Concluído**.

    ![Criar uma política de acesso condicional para controlar o registro de informações de segurança](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Em **Condições** > **Locais**, configure as seguintes opções:
   1. Configure **Sim**.
   1. Inclua **Qualquer localização**.
   1. Excluir **Todos os locais confiáveis**.
1. Selecione **Concluído** na janela *Locais*. Depois, selecione **Concluído** na janela *Condições*.
1. Em **Controles de acesso** > **Conceder**, escolha **Bloquear acesso** e **Selecionar**.
1. Defina **Habilitar política** como **Ativo**.
1. Para finalizar a política, selecione **Criar**.

## <a name="next-steps"></a>Próximas etapas

Se precisar de ajuda, consulte como [solucionar problemas com o registro combinado de informações de segurança](howto-registration-mfa-sspr-combined-troubleshoot.md) ou saiba [O que é a condição de localização no Acesso Condicional do Azure Active Directory?](../conditional-access/location-condition.md)

Para habilitar os recursos em seu locatário do Azure Active Directory, consulte os tutoriais para [habilitar a redefinição de senha self-service](tutorial-enable-sspr.md) e [habilitar a Autenticação Multifator do Microsoft Azure](tutorial-enable-azure-mfa.md).

Saiba como [forçar os usuários a registrar novamente os métodos de autenticação](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Você também pode examinar os [métodos disponíveis para a Autenticação Multifator do Microsoft Azure e a SSPR](concept-authentication-methods.md).
