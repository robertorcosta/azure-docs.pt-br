---
title: Acesso Condicional - Exigir dispositivos compatíveis - Azure Active Directory
description: Crie uma política de acesso condicional personalizado para exigir dispositivos compatíveis
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
ms.openlocfilehash: cc6bd486c1e8338eaf875c7026764c80d49e2f05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295195"
---
# <a name="conditional-access-require-compliant-devices"></a>Acesso Condicional: Exigir dispositivos compatíveis

As organizações que implantaram o Microsoft Intune podem usar as informações devolvidas de seus dispositivos para identificar dispositivos que atendam aos requisitos de conformidade, tais como:

* Exigindo um PIN para desbloquear
* Exigindo criptografia de dispositivos
* Exigindo uma versão mínima ou máxima do sistema operacional
* Exigir um dispositivo não é jailbroken ou rooted

Essas informações de conformidade de políticas são encaminhadas ao Azure AD, onde o Conditional Access pode tomar decisões para conceder ou bloquear o acesso aos recursos. Mais informações sobre políticas de conformidade de dispositivos podem ser encontradas no artigo, [defina regras sobre dispositivos para permitir o acesso a recursos em sua organização usando](/intune/protect/device-compliance-get-started) o Intune

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que os dispositivos que acessam recursos sejam marcados como compatíveis com as políticas de conformidade intune da sua organização.

1. Faça login no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições,** selecione **Usuários e grupos**
   1. Em **Incluir,** selecione **Todos os usuários**.
   1. Em **Exclusão,** selecione **Usuários e grupos** e escolha o acesso de emergência ou contas de vidro de quebra de sua organização. 
   1. Selecione **Feito**.
1. Em **Aplicativos ou ações** > na**Nuvem, selecione** **Todos os aplicativos em nuvem.**
   1. Se você tiver que excluir aplicativos específicos da sua política, você pode escolhê-los na guia **Excluir** em **Selecionar aplicativos de nuvem excluídos** e escolher **Selecionar**.
   1. Selecione **Feito**.
1. Em **Condições,** > **aplicativos do cliente (Visualização)**, **configure Configuração** para **Sim**e selecione **Feito**.
1. Em **Conceder controles** > de**acesso,** **selecione Exigir que o dispositivo seja marcado como compatível**.
   1. Selecione **Selecionar**.
1. Confirme suas configurações e **configure Ativar a diretiva** **em**.
1. Selecione **Criar** para criar para ativar sua política.

> [!NOTE]
> Você pode inscrever seus novos dispositivos no Intune mesmo se você selecionar **Exigir que o dispositivo seja marcado como compatível** para todos os **usuários** e todos os aplicativos **de nuvem** usando as etapas acima. **Exigir que o dispositivo seja marcado como controle compatível** não bloqueia a inscrição do Intune. 

### <a name="known-behavior"></a>Comportamento conhecido

No Windows 7, iOS, Android, macOS e alguns navegadores de terceiros O Azure AD identifica o dispositivo usando um certificado de cliente que é provisionado quando o dispositivo é registrado no Azure AD. Quando um usuário faz o primeiro sinal através do navegador, o usuário é solicitado a selecionar o certificado. O usuário final deve selecionar este certificado antes de continuar a usar o navegador.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determine o impacto usando o modo somente de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simule o comportamento do sinal usando a ferramenta Acesso Condicional E se](troubleshoot-conditional-access-what-if.md)

[As políticas de conformidade do dispositivo funcionam com o Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
