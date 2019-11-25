---
title: Conditional Access require managed device - Azure Active Directory
description: Learn how to configure Azure Active Directory (Azure AD) device-based Conditional Access policies that require managed devices for cloud app access.
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
ms.openlocfilehash: 0caf8e8d5e18efc0a7332f97acccc394051ed360
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452406"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>How To: Require managed devices for cloud app access with Conditional Access

Em um mundo que prioriza os dispositivos móveis e a nuvem, o Azure AD (Azure Active Directory) permite o logon único em aplicativos e serviços de qualquer lugar. Os usuários autorizados podem acessar aplicativos em nuvem em uma grande variedade de dispositivos, inclusive dispositivos para dispositivos móveis e também pessoais. No entanto, muitos ambientes tem pelo menos alguns aplicativos que devem ser acessados por dispositivos que atendem aos padrões de segurança e conformidade. Esses dispositivos também são conhecidos como dispositivos gerenciados. 

This article explains how you can configure Conditional Access policies that require managed devices to access certain cloud apps in your environment. 

## <a name="prerequisites"></a>Pré-requisitos

Requiring managed devices for cloud app access ties **Azure AD Conditional Access** and **Azure AD device management** together. Caso ainda não esteja familiarizado com uma dessas áreas, você deverá ler os seguintes tópicos primeiro:

- **[Conditional Access in Azure Active Directory](../active-directory-conditional-access-azure-portal.md)** - This article provides you with a conceptual overview of Conditional Access and the related terminology.
- **[Introduction to device management in Azure Active Directory](../devices/overview.md)** - esse artigo fornece uma visão geral das várias opções para conseguir dispositivos sob controle organizacional. 

## <a name="scenario-description"></a>Descrição do cenário

Controle do equilíbrio entre produtividade e segurança é um desafio. A proliferação de dispositivos com suporte para acessar os recursos de nuvem ajuda a melhorar a produtividade dos usuários. Por outro lado, não convém certos recursos em seu ambiente para ser acessado por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, você deve exigir que os usuários possam acessar somente usando um dispositivo gerenciado. 

With Azure AD Conditional Access, you can address this requirement with a single policy that grants access:

- Para aplicativos na nuvem selecionados
- Para usuários e grupos selecionados
- Necessidade de um dispositivo gerenciado

## <a name="managed-devices"></a>Dispositivos gerenciados  

Em termos simples, os dispositivos gerenciados são dispositivos que estão sob *algum tipo* de controle organizacional. No Microsoft Azure AD, o pré-requisito para um dispositivo gerenciado é que ele tenha sido registrado com o Azure AD. Registrar um dispositivo cria uma identidade para o dispositivo na forma de um objeto de dispositivo. Esse objeto é usado pelo Azure para rastrear as informações de status sobre um dispositivo. Como um administrador do Microsoft Azure AD, você já pode usar esse objeto para alternar (ativar/desativar) o estado de um dispositivo.
  
![Condições baseadas no dispositivo](./media/require-managed-devices/32.png)

Para obter um dispositivo registrado com o Microsoft Azure AD, você tem três opções: 

- **Azure AD registered devices** - to get a personal device registered with Azure AD
- **Azure AD joined devices** - to get an organizational Windows 10 device that is not joined to an on-premises AD registered with Azure AD. 
- **Hybrid Azure AD joined devices** - to get a Windows 10 or supported down-level device that is joined to an on-premises AD registered with Azure AD.

These three options are discussed in the article [What is a device identity?](../devices/overview.md)

Para se tornar um dispositivo gerenciado, um dispositivo registrado pode ser um **dispositivo ingressado no Microsoft Azure AD Híbrido** ou um **dispositivo que tenha sido marcado como em conformidade**.  

![Condições baseadas no dispositivo](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Requer dispositivos que tenham ingressado no Microsoft Azure AD Híbrido

In your Conditional Access policy, you can select **Require Hybrid Azure AD joined device** to state that the selected cloud apps can only be accessed using a managed device. 

![Condições baseadas no dispositivo](./media/require-managed-devices/10.png)

Essa configuração aplica-se somente ao Windows 10 ou a dispositivos de nível inferior, como o Windows 7 ou o Windows 8, associados a um AD local. Só é possível registrar esses dispositivos com o Microsoft Azure AD usando uma junção do Microsoft Azure AD Híbrido, que é um [processo automatizado](../devices/hybrid-azuread-join-plan.md) para obter um dispositivo Windows 10 registrado. 

![Condições baseadas no dispositivo](./media/require-managed-devices/45.png)

O que torna um dispositivo ingressado no Microsoft Azure AD Híbrido um dispositivo gerenciado?  Para dispositivos que fazem parte de um AD local, presume-se que o controle sobre esses dispositivos é imposto usando soluções de gerenciamento, como **System Center Configuration Manager (SCCM)** ou **política de grupo (GP)** para gerenciá-los. Como não há nenhum método para que o Microsoft Azure AD determine se qualquer um dos métodos a seguir foram aplicados a um dispositivo, exigir um dispositivo ingressado no Microsoft Azure AD híbrido é um mecanismo relativamente fraco para solicitar um dispositivo gerenciado. Cabe a você como administrador avaliar se os métodos que são aplicados a seus dispositivos ingressados no domínio local são fortes o suficiente para constituir um dispositivo gerenciado, se tal dispositivo também for um dispositivo ingressado no Microsoft Azure AD híbrido.

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

> [!NOTE]
> If you configure a policy to require compliant devices users may be prompted on Mac, iOS, and Android to select a device certificate during policy evaluation. This is a known behavior.

## <a name="next-steps"></a>Próximos passos

Before configuring a device-based Conditional Access policy in your environment, you should take a look at the [best practices for Conditional Access in Azure Active Directory](best-practices.md).
