---
title: Acesso condicional requer dispositivo gerenciado - Azure Active Directory
description: Saiba como configurar as políticas de acesso condicional baseadas em dispositivos azure Active Directory (Azure AD) baseadas em dispositivos que requerem dispositivos gerenciados para acesso a aplicativos na nuvem.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a3c71534febc3cdb6429d3092225ebc73f6cbe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481476"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Como: Exigir dispositivos gerenciados para acesso a aplicativos na nuvem com acesso condicional

Em um mundo que prioriza os dispositivos móveis e a nuvem, o Azure AD (Azure Active Directory) permite o logon único em aplicativos e serviços de qualquer lugar. Os usuários autorizados podem acessar aplicativos em nuvem em uma grande variedade de dispositivos, inclusive dispositivos para dispositivos móveis e também pessoais. No entanto, muitos ambientes tem pelo menos alguns aplicativos que devem ser acessados por dispositivos que atendem aos padrões de segurança e conformidade. Esses dispositivos também são conhecidos como dispositivos gerenciados. 

Este artigo explica como você pode configurar políticas de acesso condicional que requerem dispositivos gerenciados para acessar determinados aplicativos em nuvem em seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

Exigir dispositivos gerenciados para acesso a aplicativos em nuvem une **o Azure AD Conditional Access** e **o gerenciamento de dispositivos AD do Azure.** Caso ainda não esteja familiarizado com uma dessas áreas, você deverá ler os seguintes tópicos primeiro:

- **[Acesso Condicional no Diretório Ativo do Azure](../active-directory-conditional-access-azure-portal.md)** - Este artigo fornece uma visão geral conceitual do Acesso Condicional e da terminologia relacionada.
- **[Introduction to device management in Azure Active Directory](../devices/overview.md)** - esse artigo fornece uma visão geral das várias opções para conseguir dispositivos sob controle organizacional. 
- Para suporte ao Chrome no **Windows 10 Creators Update (versão 1703)** ou posterior, instale a [extensão contas](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)do Windows 10 . Essa extensão é necessária quando uma política de acesso condicional requer detalhes específicos do dispositivo.

>[!NOTE] 
> Recomendamos o uso da política de acesso condicional baseado em dispositivos Azure AD para obter a melhor aplicação após a autenticação inicial do dispositivo. Isso inclui sessões de fechamento se o dispositivo ficar fora de conformidade e do fluxo de código do dispositivo.


## <a name="scenario-description"></a>Descrição do cenário

Controle do equilíbrio entre produtividade e segurança é um desafio. A proliferação de dispositivos com suporte para acessar os recursos de nuvem ajuda a melhorar a produtividade dos usuários. Por outro lado, não convém certos recursos em seu ambiente para ser acessado por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, você deve exigir que os usuários possam acessar somente usando um dispositivo gerenciado. 

Com o Azure AD Conditional Access, você pode resolver esse requisito com uma única política que concede acesso:

- Para aplicativos na nuvem selecionados
- Para usuários e grupos selecionados
- Necessidade de um dispositivo gerenciado

## <a name="managed-devices"></a>Dispositivos gerenciados  

Em termos simples, os dispositivos gerenciados são dispositivos que estão sob *algum tipo* de controle organizacional. No Microsoft Azure AD, o pré-requisito para um dispositivo gerenciado é que ele tenha sido registrado com o Azure AD. Registrar um dispositivo cria uma identidade para o dispositivo na forma de um objeto de dispositivo. Esse objeto é usado pelo Azure para rastrear as informações de status sobre um dispositivo. Como um administrador do Microsoft Azure AD, você já pode usar esse objeto para alternar (ativar/desativar) o estado de um dispositivo.
  
![Condições baseadas no dispositivo](./media/require-managed-devices/32.png)

Para obter um dispositivo registrado com o Microsoft Azure AD, você tem três opções: 

- **Dispositivos registrados do Microsoft Azure AD** - para obter um dispositivo pessoal registrado com o Microsoft Azure AD
- **Dispositivos ingressados no Azure AD** – para obter um dispositivo Windows 10 organizacional que não esteja ingressado em um AD local registrado no Azure AD. 
- **Os dispositivos híbridos do Azure AD participaram de dispositivos** - para obter um dispositivo Windows 10 ou de nível inferior com suporte associado a um AD local registrado no Azure AD.

Essas três opções são discutidas no artigo [O que é uma identidade de dispositivo?](../devices/overview.md)

Para se tornar um dispositivo gerenciado, um dispositivo registrado pode ser um **dispositivo ingressado no Microsoft Azure AD Híbrido** ou um **dispositivo que tenha sido marcado como em conformidade**.  

![Condições baseadas no dispositivo](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Requer dispositivos que tenham ingressado no Microsoft Azure AD Híbrido

Em sua política de Acesso Condicional, você pode selecionar **o dispositivo de adesão do Azure AD do Require Hybrid** para afirmar que os aplicativos em nuvem selecionados só podem ser acessados usando um dispositivo gerenciado. 

![Condições baseadas no dispositivo](./media/require-managed-devices/10.png)

Essa configuração aplica-se somente ao Windows 10 ou a dispositivos de nível inferior, como o Windows 7 ou o Windows 8, associados a um AD local. Só é possível registrar esses dispositivos com o Microsoft Azure AD usando uma junção do Microsoft Azure AD Híbrido, que é um [processo automatizado](../devices/hybrid-azuread-join-plan.md) para obter um dispositivo Windows 10 registrado. 

![Condições baseadas no dispositivo](./media/require-managed-devices/45.png)

O que torna um dispositivo ingressado no Microsoft Azure AD Híbrido um dispositivo gerenciado?  Para dispositivos que são unidos a um AD no local, presume-se que o controle sobre esses dispositivos seja aplicado usando soluções de gerenciamento, como **O Gerenciador de Configuração** ou **a política de grupo (GP)** para gerenciá-los. Como não há nenhum método para que o Microsoft Azure AD determine se qualquer um dos métodos a seguir foram aplicados a um dispositivo, exigir um dispositivo ingressado no Microsoft Azure AD híbrido é um mecanismo relativamente fraco para solicitar um dispositivo gerenciado. Cabe a você como administrador avaliar se os métodos que são aplicados a seus dispositivos ingressados no domínio local são fortes o suficiente para constituir um dispositivo gerenciado, se tal dispositivo também for um dispositivo ingressado no Microsoft Azure AD híbrido.

## <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como em conformidade

A opção de *exigir que um dispositivo seja marcado como em conformidade* é a forma mais segura de solicitar um dispositivo gerenciado.

![Condições baseadas no dispositivo](./media/require-managed-devices/11.png)

Essa opção requer que um dispositivo seja registrado com o Microsoft Azure AD e também marcado como em conformidade por:
         
- Intune
- Um sistema gerenciado por dispositivo móvel de terceiros (MDM) que gerencia os dispositivos Windows 10 por meio da integração do Azure AD. Sistemas MDM de terceiros para tipos de dispositivo OS, exceto Windows 10, não são suportados.
 
![Condições baseadas no dispositivo](./media/require-managed-devices/46.png)

Para um dispositivo que esteja marcado como em conformidade, você pode presumir que: 

- Os dispositivos móveis que sua força de trabalho usa para acessar os dados da empresa são gerenciados
- Os aplicativos móveis que sua força de trabalho usa são gerenciados
- As informações da sua empresa são protegidas ao ajudar a controlar a maneira como sua força de trabalho as acessa e compartilha
- O dispositivo e seus aplicativos são compatíveis com os requisitos de segurança da empresa

### <a name="known-behavior"></a>Comportamento conhecido

No Windows 7, iOS, Android, macOS e alguns navegadores de terceiros O Azure AD identifica o dispositivo usando um certificado de cliente que é provisionado quando o dispositivo é registrado no Azure AD. Quando um usuário faz o primeiro sinal através do navegador, o usuário é solicitado a selecionar o certificado. O usuário final deve selecionar este certificado antes de continuar a usar o navegador.

## <a name="next-steps"></a>Próximas etapas

Antes de configurar uma política de acesso condicional baseada em dispositivos em seu ambiente, você deve dar uma olhada nas [práticas recomendadas de acesso condicional no Azure Active Directory](best-practices.md).
