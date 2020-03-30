---
title: MFA ou 2FA e Gerenciamento de Identidade Privilegiada - Azure AD | Microsoft Docs
description: Saiba como o PIM (Azure AD Privileged Identity Management) valida a MFA (autenticação multifator).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6df593909c3ae5962e413eb84e64196fade0326b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022146"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Autenticação multifatorial e gerenciamento de identidade privilegiada

É recomendável que você exija MFA (autenticação multifator) a todos os administradores. Isso reduz o risco de um ataque devido a uma senha comprometida.

Você pode exigir que os usuários completem um desafio de autenticação multifatorial quando fizerem login. Você também pode exigir que os usuários completem um desafio de autenticação de vários fatores quando ativarem uma função no Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Dessa forma, se o usuário não completou um desafio de autenticação multifatorial quando fez o seu assinatura, ele será solicitado a fazê-lo pelo Privileged Identity Management.

> [!IMPORTANT]
> No momento, a Autenticação Multifatorial do Azure só funciona com contas de trabalho ou escola, não contas pessoais da Microsoft (geralmente uma conta pessoal que é usada para fazer login em serviços da Microsoft, como Skype, Xbox ou Outlook.com). Por causa disso, qualquer pessoa que use uma conta pessoal não pode ser um administrador elegível porque não pode usar autenticação multifatorial para ativar suas funções. Se esses usuários precisarem continuar a gerenciar cargas de trabalho usando uma conta da Microsoft, eleve-os a administradores permanentes por enquanto.

## <a name="how-pim-validates-mfa"></a>Como o PIM valida MFA

Existem duas opções para validar a autenticação multifatorial quando um usuário ativa uma função.

A opção mais simples é contar com a Autenticação Multifatorial do Azure para usuários que estão ativando uma função privilegiada. Para fazer isso, primeiro verifique se esses usuários são licenciados, se necessário, e se registraram para autenticação multifatorial do Azure. Para obter mais informações sobre como implantar a autenticação multifatorial do Azure, consulte [Implantar autenticação multifatorial baseada em nuvem](../authentication/howto-mfa-getstarted.md). Recomenda-se, mas não é necessário, que você configure o Azure AD para impor a autenticação multifatorial para esses usuários quando eles fizerem login. Isso porque as verificações de autenticação multifatorial serão feitas pela própria Gestão de Identidade Privilegiada.

Alternativamente, se os usuários autenticarem no local, você pode fazer com que seu provedor de identidade seja responsável pela autenticação multifatorial. Por exemplo, se você tiver configurado os Serviços de Federação do AD para exigir a autenticação baseada em cartão inteligente antes de acessar o Azure AD, [Protegendo os recursos de nuvem usando a Autenticação Multifator do Azure e o AD FS](../authentication/howto-mfa-adfs.md) inclui instruções para configurar o AD FS a fim de enviar solicitações ao Azure AD. Quando um usuário tenta ativar uma função, o Gerenciamento de Identidade Privilegiada aceitará que a autenticação multifatorial já foi validada para o usuário assim que receber as reivindicações apropriadas.

## <a name="next-steps"></a>Próximas etapas

- [Configure as configurações de função AD do Azure no Gerenciamento de Identidade Privilegiada](pim-how-to-change-default-settings.md)
- [Configure as configurações de função de recurso do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
