---
title: O que é um dispositivo adepto a adesão a um Azure AD híbrido?
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
ms.openlocfilehash: 15cdaba7d63d72aab25757e7ba6f5eadc48e026a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512242"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos adicionados ao Azure AD híbrido

Por mais de uma década, muitas organizações têm usado o ingresso no domínio para o respectivo Active Directory local a fim de permitir que:

- Os departamentos de TI gerenciem dispositivos pertencentes à empresa de um local central.
- Os usuários entrem em seus dispositivos com as respectivas contas corporativas ou de estudante do Active Directory.

Normalmente, as organizações com uma pegada no local dependem de métodos de imagem para provisionar dispositivos, e muitas vezes usam **o Gerenciador de Configuração** ou **a política de grupo (GP)** para gerenciá-los.

Se seu ambiente tiver um espaço local do AD e você também quiser se beneficiar dos recursos fornecidos pelo Azure Active Directory, será possível implementar dispositivos adicionados ao Azure AD híbrido. Esses dispositivos são dispositivos que são unidos ao seu Diretório Ativo no local e registrados no seu Diretório Ativo do Azure.

|   | Ingresso híbrido no Azure AD |
| --- | --- |
| **Definição** | Junto uato aos AD e Azure AD no local, exigindo que a conta organizacional faça login no dispositivo |
| **Audiência primária** | Adequado para organizações híbridas com infra-estrutura AD existente no local |
|   | Aplicável a todos os usuários de uma organização |
| **Propriedade do dispositivo** | Organização |
| **Sistemas Operacionais** | Windows 10, 8.1 e 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 e 2019 |
| **Provisionamento** | Windows 10, Windows Server 2016/2019 |
|   | Domínio junte-se por TI e junte-se automaticamente via Azure AD Connect ou ADFS config |
|   | Domínio junte-se ao Windows Autopilot e junte-se automaticamente via Azure AD Connect ou ADFS config |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2 - Exigir MSI |
| **Opções de login do dispositivo** | Contas organizacionais usando: |
|   | Senha |
|   | Windows Hello for Business for Win10 |
| **Gerenciamento de dispositivos** | Política de Grupo |
|   | Gerenciador de configuração autônomo ou co-gerenciador com o Microsoft Intune |
| **Principais funcionalidades** | SSO para recursos em nuvem e on-premises |
|   | Acesso Condicional através de domínio juntar ou através do Intune se co-gerenciado |
|   | Redefinição de senha de autoatendimento e redefinição do Windows Hello PIN na tela de bloqueio |
|   | Enterprise State Roaming através de dispositivos |

![Dispositivos adicionados ao Azure AD híbrido](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Cenários

Use dispositivos híbridos Azure AD se:

- Você tem aplicativos Win32 implantados nesses dispositivos que utilizam a autenticação de computador do Active Directory.
- Você deseja continuar a usar a Diretiva de Grupo para gerenciar a configuração do dispositivo.
- Você deseja continuar a usar soluções de imagem existentes para implantar e configurar dispositivos.
- Você deve suportar dispositivos Windows 7 e 8.1 de baixo nível, além do Windows 10

## <a name="next-steps"></a>Próximas etapas

- [Planejar sua implementação de ingresso no Azure AD híbrido](hybrid-azuread-join-plan.md)
- [Gerenciar identidades de dispositivos usando o portal Azure](device-management-azure-portal.md)
- [Gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
