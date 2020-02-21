---
title: Acesso condicional-exigir dispositivos em conformidade-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para exigir dispositivos em conformidade
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d19cc39147608dafc4576f4c8bad0533ad1b32d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77521907"
---
# <a name="conditional-access-require-compliant-devices"></a>Acesso condicional: exigir dispositivos em conformidade

As organizações que implantaram Microsoft Intune podem usar as informações retornadas de seus dispositivos para identificar os dispositivos que atendem aos requisitos de conformidade, como:

* Exigindo um PIN para desbloquear
* Exigindo criptografia do dispositivo
* Exigindo uma versão mínima ou máxima do sistema operacional
* Exigir um dispositivo não está desbloqueado ou com raiz

Essas informações de conformidade de política são encaminhadas ao Azure AD, em que o acesso condicional pode tomar decisões para conceder ou bloquear o acesso a recursos. Mais informações sobre as políticas de conformidade do dispositivo podem ser encontradas no artigo [definir regras em dispositivos para permitir o acesso a recursos em sua organização usando o Intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que os dispositivos que acessam os recursos sejam marcados como em conformidade com as políticas de conformidade do Intune da sua organização.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários**.
   1. Em **excluir**, selecione **usuários e grupos** e escolha o acesso de emergência da sua organização ou contas de vidro. 
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações** > **incluir**, selecione **todos os aplicativos de nuvem**.
   1. Se você precisar excluir aplicativos específicos de sua política, poderá selecioná-los na guia **excluir** em **selecionar aplicativos de nuvem excluídos** e escolher **selecionar**.
   1. Selecione **Concluído**.
1. Em **controles de acesso** > **concessão**, selecione **exigir que o dispositivo seja marcado como compatível**.
   1. Selecione **Selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar para habilitar a política.

### <a name="known-behavior"></a>Comportamento conhecido

No Windows 7, iOS, Android, macOS e alguns navegadores da Web de terceiros, o Azure AD identifica o dispositivo usando um certificado de cliente que é provisionado quando o dispositivo é registrado no Azure AD. Quando um usuário entra pela primeira vez por meio do navegador, é solicitado que o usuário selecione o certificado. O usuário final deve selecionar esse certificado antes que possa continuar a usar o navegador.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)

[As políticas de conformidade do dispositivo funcionam com o Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
