---
title: O que é um dispositivo adepto do Azure AD?
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
ms.openlocfilehash: 40f89fbe19b93601f9e0525f0387e402bd175fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672674"
---
# <a name="azure-ad-joined-devices"></a>Dispositivos adicionados ao Azure AD

O adesão ao Azure AD é destinado a organizações que desejam ser somente em nuvem ou somente em nuvem. Qualquer organização pode implantar dispositivos azure AD unidos, não importa o tamanho ou a indústria. A adesão ao Azure AD funciona mesmo em um ambiente híbrido, permitindo o acesso a aplicativos e recursos em nuvem e on-premises.

|   | Ingresso no AD do Azure |
| --- | --- |
| **Definição** | Juntou-se apenas ao Azure AD exigindo que a conta organizacional faça login no dispositivo |
| **Audiência primária** | Adequado para organizações somente em nuvem e híbridas. |
|   | Aplicável a todos os usuários de uma organização |
| **Propriedade do dispositivo** | Organização |
| **Sistemas Operacionais** | Todos os dispositivos Windows 10 |
| **Provisionamento** | Self-service: Windows OOBE ou Configurações |
|   | Registro em massa |
|   | Windows Autopilot |
| **Opções de login do dispositivo** | Contas organizacionais usando: |
|   | Senha |
|   | Windows Hello for Business |
|   | Chaves de segurança FIDO2.0 (visualização) |
| **Gerenciamento de dispositivos** | Gerenciamento de dispositivos móveis (exemplo: Microsoft Intune) |
|   | Co-gerenciamento com o Microsoft Intune e o Microsoft Endpoint Configuration Manager |
| **Principais funcionalidades** | SSO para recursos em nuvem e on-premises |
|   | Acesso Condicional através de inscrição em MDM e avaliação de conformidade com MDM |
|   | Redefinição de senha de autoatendimento e redefinição do Windows Hello PIN na tela de bloqueio |
|   | Enterprise State Roaming através de dispositivos |

Os dispositivos azure AD são autorizados a usar uma conta AD organizacional do Azure. O acesso aos recursos na organização pode ser ainda mais limitado com base nas políticas de conta Ad do Azure e [do Acesso Condicional](../conditional-access/overview.md) aplicadas à identidade do dispositivo.

Os administradores podem proteger e controlar ainda mais os dispositivos aderidos ao Azure AD usando ferramentas de MDM (Mobile Device Management, gerenciamento de dispositivos móveis) como o Microsoft Intune ou em cenários de cogerenciamento usando o Microsoft Endpoint Configuration Manager. Essas ferramentas fornecem um meio para impor configurações exigidas pela organização, como exigir que o armazenamento seja criptografado, complexidade de senha, instalações de software e atualizações de software. Os administradores podem disponibilizar aplicativos de organização para dispositivos aderidos ao Azure AD usando o Gerenciador de Configuração para [gerenciar aplicativos da Microsoft Store para Negócios e Educação](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

O adesão ao Azure AD pode ser realizado usando opções de autoatendimento como o Out of Box Experience (OOBE), inscrição em massa ou [Piloto Automático do Windows.](/intune/enrollment-autopilot)

Os dispositivos azure AD ainda podem manter acesso único de logon aos recursos locais quando estiverem na rede da organização. Os dispositivos que são aderidos ao Azure AD ainda podem autenticar em servidores locais, como arquivos, impressão e outros aplicativos.

## <a name="scenarios"></a>Cenários

Embora a adição ao Azure AD se destine basicamente às organizações que não têm uma infraestrutura do Active Directory local para Windows Server, você certamente pode usá-la em cenários em que:

- Você deseja fazer a transição para infraestrutura baseada em nuvem usando o Azure AD e um MDM como o Intune.
- Não é possível usar um ingresso no domínio local, por exemplo, caso seja necessário colocar dispositivos móveis, como tablets e telefones, sob controle.
- Os usuários precisam basicamente acessar o Office 365 ou outros aplicativos SaaS integrados ao Azure AD.
- Você quer gerenciar um grupo de usuários no Azure AD, e não no Active Directory. Esse cenário pode se aplicar, por exemplo, a trabalhadores sazonais, contratados ou estudantes.
- Você deseja fornecer recursos de ingresso para trabalhadores em escritórios remotos com infraestrutura local limitada.

Você pode configurar dispositivos adicionados ao Azure AD para dispositivos com Windows 10.

A meta dos dispositivos adicionados ao Azure AD é simplificar:

- As implantações de dispositivos Windows que pertencem à organização
- O acesso a aplicativos e recursos organizacionais de qualquer dispositivo Windows
- Gerenciamento baseado em nuvem de dispositivos empresariais
- Os usuários para fazer login em seus dispositivos com seu Azure AD ou sincronizado trabalho active diretório ou contas escolares.

![Dispositivos adicionados ao Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

O Ingresso no Azure AD pode ser implantado usando um destes métodos:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Implantação em massa](/intune/windows-bulk-enroll)
- [Experiência de autoatendimento](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Próximas etapas

- [Planejar sua implementação de ingresso no Azure AD](azureadjoin-plan.md)
- [Como gerenciar o grupo de administradores locais nos dispositivos do Microsoft Azure Active Directory](assign-local-admin.md)
- [Gerenciar identidades de dispositivos usando o portal Azure](device-management-azure-portal.md)
- [Gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
