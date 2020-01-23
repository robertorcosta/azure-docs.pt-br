---
title: O que é um dispositivo ingressado no Azure AD?
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
ms.openlocfilehash: 24ec4373bceb3cc3c9e5be2c7a0dab1f62197b3c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512191"
---
# <a name="azure-ad-joined-devices"></a>Dispositivos adicionados ao Azure AD

O ingresso no Azure AD destina-se a organizações que desejam ser somente na nuvem ou somente em nuvem. Qualquer organização pode implantar dispositivos ingressados no Azure AD, independentemente do tamanho ou do setor. O ingresso no Azure AD funciona mesmo em um ambiente híbrido, permitindo o acesso a aplicativos e recursos locais e na nuvem.

|   | Ingresso no AD do Azure |
| --- | --- |
| **Definição** | Ingressado somente no Azure AD exigindo que a conta organizacional se conecte ao dispositivo |
| **Público-alvo principal** | Adequado para organizações híbridas e somente de nuvem. |
|   | Aplicável a todos os usuários em uma organização |
| **Propriedade do dispositivo** | Organização |
| **Sistemas Operacionais** | Todos os dispositivos Windows 10 |
| **Provisionamento** | Autoatendimento: Windows OOBE ou configurações |
|   | Registro em massa |
|   | Windows Autopilot |
| **Opções de entrada do dispositivo** | Contas organizacionais usando: |
|   | Senha |
|   | Windows Hello for Business |
|   | Chaves de segurança do FIDO 2.0 (versão prévia) |
| **Gerenciamento de dispositivos** | Gerenciamento de dispositivo móvel (exemplo: Microsoft Intune) |
|   | Cogerenciamento com Microsoft Intune e Microsoft Endpoint Configuration Manager |
| **Principais recursos** | SSO para recursos de nuvem e locais |
|   | Acesso condicional por meio de registro de MDM e avaliação de conformidade de MDM |
|   | Redefinição de senha por autoatendimento e redefinição de PIN do Windows Hello na tela de bloqueio |
|   | Enterprise State Roaming entre dispositivos |

Os dispositivos ingressados no Azure AD são conectados ao usando uma conta organizacional do Azure AD. O acesso a recursos na organização pode ser ainda mais limitado com base nessa conta do Azure AD e [nas políticas de acesso condicional](../conditional-access/overview.md) aplicadas à identidade do dispositivo.

Os administradores podem proteger e controlar ainda mais os dispositivos adicionados ao Azure AD usando ferramentas de MDM (gerenciamento de dispositivo móvel) como Microsoft Intune ou em cenários de cogerenciamento usando o Microsoft Endpoint Configuration Manager. Essas ferramentas fornecem um meio de impor configurações necessárias à organização, como exigir que o armazenamento seja criptografado, complexidade de senha, instalações de software e atualizações de software. Os administradores podem disponibilizar aplicativos da organização para dispositivos ingressados no Azure AD usando Configuration Manager para [gerenciar aplicativos da Microsoft Store para negócios e educação](https://docs.microsoft.com/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

O ingresso no Azure AD pode ser feito usando opções de autoatendimento como a OOBE (configuração inicial do uso), o registro em massa ou o [Windows AutoPilot](https://docs.microsoft.com/intune/enrollment-autopilot).

Os dispositivos ingressados no Azure AD ainda podem manter o acesso de logon único a recursos locais quando estão na rede da organização. Os dispositivos que são ingressados no Azure AD ainda podem ser autenticados em servidores locais como arquivos, impressão e outros aplicativos.

## <a name="scenarios"></a>Cenários

Embora a adição ao Azure AD se destine basicamente às organizações que não têm uma infraestrutura do Active Directory local para Windows Server, você certamente pode usá-la em cenários em que:

- Você deseja fazer a transição para infraestrutura baseada em nuvem usando o Azure AD e um MDM como o Intune.
- Não é possível usar um ingresso no domínio local, por exemplo, caso seja necessário colocar dispositivos móveis, como tablets e telefones, sob controle.
- Os usuários precisam basicamente acessar o Office 365 ou outros aplicativos SaaS integrados ao Azure AD.
- Você quer gerenciar um grupo de usuários no Azure AD, e não no Active Directory. Esse cenário pode ser aplicado, por exemplo, a trabalhadores sazonais, prestadores de trabalho ou alunos.
- Você deseja fornecer recursos de ingresso para trabalhadores em escritórios remotos com infraestrutura local limitada.

Você pode configurar dispositivos adicionados ao Azure AD para dispositivos com Windows 10.

A meta dos dispositivos adicionados ao Azure AD é simplificar:

- As implantações de dispositivos Windows que pertencem à organização
- O acesso a aplicativos e recursos organizacionais de qualquer dispositivo Windows
- Gerenciamento baseado em nuvem de dispositivos empresariais
- Os usuários podem entrar em seus dispositivos com o Azure AD ou sincronizados Active Directory contas corporativas ou de estudante.

![Dispositivos adicionados ao Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

O Ingresso no Azure AD pode ser implantado usando um destes métodos:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Implantação em massa](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [Experiência de autoatendimento](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Próximos passos

- [Planejar sua implementação de ingresso no Azure AD](azureadjoin-plan.md)
- [Como gerenciar o grupo de administradores locais em dispositivos ingressados no Azure AD](assign-local-admin.md)
- [Gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md)
- [Gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
