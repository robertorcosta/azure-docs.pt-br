---
title: O que são dispositivos registrados no Azure AD?
description: Saiba como o gerenciamento de identidade do dispositivo pode ajudar você a gerenciar dispositivos que estão acessando os recursos em seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67462753"
---
# <a name="azure-ad-registered-devices"></a>Dispositivos registrados no Azure AD

O objetivo dos dispositivos registrados no Azure AD é fornecer aos seus usuários suporte para os cenários do Bring Your Own Device (BYOD) ou de dispositivos móveis. Nesses cenários, um usuário pode acessar os recursos controlados do Azure Active Directory da sua organização usando um dispositivo pessoal.

|   | Azure AD registrado |
| --- | --- |
| **Definição** | Registrado no Azure AD sem exigir que a conta organizacional faça login no dispositivo |
| **Audiência primária** | Aplicável a todos os usuários com os seguintes critérios: |
|   | BYOD (Traga seu próprio dispositivo) |
|   | Dispositivos móveis |
| **Propriedade do dispositivo** | Usuário ou Organização |
| **Sistemas Operacionais** | Windows 10, iOS, Android e MacOS |
| **Provisionamento** | Windows 10 – Configurações |
|   | iOS/Android – Portal da Empresa ou aplicativo Autenticador microsoft |
|   | MacOS – Portal da Empresa |
| **Opções de login do dispositivo** | Credenciais locais do usuário final |
|   | Senha |
|   | Windows Hello |
|   | PIN |
|   | Biometria ou Padrão para outros dispositivos |
| **Gerenciamento de dispositivos** | Gerenciamento de dispositivos móveis (exemplo: Microsoft Intune) |
|   | gerenciamento de aplicativos móveis |
| **Principais funcionalidades** | SSO para recursos em nuvem |
|   | Acesso Condicional quando inscrito em Intune |
|   | Acesso Condicional via política de proteção de aplicativos |
|   | Permite que o telefone faça login com o aplicativo Microsoft Authenticator |

![Dispositivos registrados no Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Os dispositivos registrados no Azure AD são conectados ao uso de uma conta local como uma conta da Microsoft em um dispositivo Windows 10, mas além disso têm uma conta Azure AD anexada para acesso a recursos organizacionais. O acesso aos recursos na organização pode ser ainda mais limitado com base nas políticas de conta Ad do Azure e do Acesso Condicional aplicadas à identidade do dispositivo.

Os administradores podem proteger e controlar ainda mais esses dispositivos registrados no Azure AD usando ferramentas de MDM (Mobile Device Management, gerenciamento de dispositivos móveis) como o Microsoft Intune. O MDM fornece um meio para impor configurações exigidas pela organização, como exigir que o armazenamento seja criptografado, a complexidade da senha e o software de segurança mantido atualizado. 

O registro do Azure AD pode ser realizado ao acessar um aplicativo de trabalho pela primeira vez ou manualmente usando o menu Configurações do Windows 10. 

## <a name="scenarios"></a>Cenários

Um usuário em sua organização deseja acessar ferramentas para e-mail, relatórios de tempo limite e benefícios de inscrição de seu PC doméstico. Sua organização tem essas ferramentas por trás de uma política de acesso condicional que requer acesso a partir de um dispositivo compatível com Intune. O usuário adiciona sua conta de organização e registra seu PC doméstico com o Azure AD e as políticas intune necessárias são aplicadas dando ao usuário acesso aos seus recursos.

Outro usuário quer acessar seu e-mail organizacional em seu telefone Android pessoal que foi enraizado. Sua empresa requer um dispositivo compatível e criou uma política de conformidade intune para bloquear quaisquer dispositivos enraizados. O funcionário é impedido de acessar recursos organizacionais neste dispositivo.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar identidades de dispositivos usando o portal Azure](device-management-azure-portal.md)
- [Gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
