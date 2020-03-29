---
title: Identidade do dispositivo e virtualização da área de trabalho - Azure Active Directory
description: Saiba como as identidades dos dispositivos AD VDI e Azure podem ser usadas juntas
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900365"
---
# <a name="device-identity-and-desktop-virtualization"></a>Identidade do dispositivo e virtualização de desktop

Os administradores geralmente implantam plataformas de infra-estrutura de desktop virtual (VDI) que hospedam sistemas operacionais Windows em suas organizações. Os administradores implantam o VDI para:

- Agilizar a gestão.
- Reduzir custos através da consolidação e centralização de recursos.
- Ofereça aos usuários finais mobilidade e liberdade para acessar desktops virtuais a qualquer hora, de qualquer lugar, em qualquer dispositivo.

Existem dois tipos principais de desktops virtuais:

- Persistente
- Não-Persistente

As versões persistentes usam uma imagem de desktop exclusiva para cada usuário ou um pool de usuários. Esses desktops exclusivos podem ser personalizados e salvos para uso futuro. 

As versões não persistentes usam uma coleção de desktops que os usuários podem acessar conforme necessário. Esses desktops não persistentes são revertidos ao seu estado original após a saída do usuário.

Este artigo cobrirá a orientação da Microsoft aos administradores sobre suporte à identidade do dispositivo e VDI. Para obter mais informações sobre a identidade do dispositivo, consulte o artigo [O que é uma identidade de dispositivo](overview.md).

## <a name="supported-scenarios"></a>Cenários com suporte

Antes de configurar as identidades dos dispositivos no Azure AD para o seu ambiente VDI, familiarize-se com os cenários suportados. A tabela abaixo ilustra quais cenários de provisionamento são suportados. O provisionamento neste contexto implica que um administrador pode configurar identidades de dispositivos em escala sem exigir qualquer interação do usuário final.

| Tipo de identidade do dispositivo | Infra-estrutura de identidade | Dispositivos Windows | Versão da plataforma VDI | Com suporte |
| --- | --- | --- | --- | --- |
| Adicionado ao Azure AD híbrido | Federado* | Corrente do Windows*** e Windows de nível baixo**** | Persistente | Sim |
|   |   | Corrente do Windows | Não-persistente | Não |
|   |   | Nível inferior do Windows | Não-persistente | Sim |
|   | Gerenciado** | Corrente do Windows e Windows em baixo nível | Persistente | Sim |
|   |   | Corrente do Windows | Não-persistente | Não |
|   |   | Nível inferior do Windows | Não-persistente | Sim |
| Adicionado ao Azure AD | Federado | Corrente do Windows | Persistente | Não |
|   |   |   | Não-persistente | Não |
|   | Gerenciada | Corrente do Windows | Persistente | Não |
|   |   |   | Não-persistente | Não |
| Registrado no Azure AD | Federado | Corrente do Windows | Persistente | Não |
|   |   |   | Não-persistente | Não |
|   | Gerenciada | Corrente do Windows | Persistente | Não |
|   |   |   | Não-persistente | Não |

\*Um ambiente de infra-estrutura de identidade **federada** representa um ambiente com um provedor de identidade, como o AD FS ou outro IDP de terceiros.

\*\*Um ambiente de infra-estrutura de identidade **gerenciada** representa um ambiente com o Azure AD como o provedor de identidade implantado com [sincronia de hash (PHS)](../hybrid/whatis-phs.md) ou [autenticação de passagem (PTA)](../hybrid/how-to-connect-pta.md) com [o login único sem emendas.](../hybrid/how-to-connect-sso.md)

\*\*\*Os dispositivos **atuais do Windows** representam o Windows 10, o Windows Server 2016 e o Windows Server 2019.

\*\*\*\***Os** dispositivos de nível baixo do Windows representam o Windows 7, o Windows 8.1, o Windows Server 2008 R2, o Windows Server 2012 e o Windows Server 2012 R2. Para obter informações sobre suporte no Windows 7, consulte [o suporte para o Windows 7 está terminando](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Para obter informações sobre suporte no Windows Server 2008 R2, consulte [Prepare-se para o fim do suporte do Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Orientação da Microsoft

Os administradores devem fazer referência aos seguintes artigos, com base em sua infra-estrutura de identidade, para aprender como configurar a adesão híbrida do Azure AD.

- [Configure diretório ativo híbrido do Azure para ambiente federado](hybrid-azuread-join-federated-domains.md)
- [Configure o Azure Active Directory híbrido para ambiente gerenciado](hybrid-azuread-join-managed-domains.md)

Se você está confiando na Ferramenta de Preparação do Sistema (sysprep.exe) e se estiver usando uma imagem pré-Windows 10 1809 para instalação, certifique-se de que a imagem não é de um dispositivo que já está registrado no Azure AD como aderiu ao Azure AD híbrido.

Se você estiver confiando em um instantâneo da Máquina Virtual (VM) para criar VMs adicionais, certifique-se de que o snapshot não é de uma VM que já está registrada no Azure AD como Azure AD híbrido.

Ao implantar o VDI não persistente, os administradores de TI devem prestar muita atenção ao gerenciamento de dispositivos obsoletos no Azure AD. A Microsoft recomenda que os administradores de TI implementem a orientação abaixo. Se não o fizer, o diretório terá muitos dispositivos ad híbridos obsoletos que foram registrados a partir de sua plataforma VDI não persistente.

- Crie e use um prefixo para o nome de exibição do computador que indica a área de trabalho como baseada em VDI.
- Implemente o seguinte comando como parte do script de logoff. Este comando acionará uma chamada de esforço para o Azure AD para excluir o dispositivo.
   - Para dispositivos de nível baixo do Windows – autoworkplace.exe /leave
- Definir e implementar o processo para [o gerenciamento de dispositivos obsoletos](manage-stale-devices.md).
   - Uma vez que você tenha uma estratégia para identificar seus dispositivos híbridos azure AD não persistentes, você pode ser mais agressivo na limpeza desses dispositivos para garantir que seu diretório não seja consumido com muitos dispositivos obsoletos.
 
## <a name="next-steps"></a>Próximas etapas

[Configuração do Diretório Ativo do Azure híbrido se une para ambiente federado](hybrid-azuread-join-federated-domains.md)
