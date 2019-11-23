---
title: Migrate Conditional Access policies - Azure Active Directory
description: Veja o que você precisa saber para migrar as políticas clássicas no portal do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d664f6e61dbbaaf0b8ab74c392596a206ff644
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380550"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>What is a policy migration in Azure Active Directory Conditional Access? 

[Conditional Access](../active-directory-conditional-access-azure-portal.md) is a capability of Azure Active directory (Azure AD) that enables you to control how authorized users access your cloud apps. While the purpose is still the same, the release of the new Azure portal has introduced significant improvements to how Conditional Access works.

Considere migrar as políticas que você não tiver criado no Portal do Azure porque:

- Agora você pode abordar cenários que você não podia manipular antes.
- Você pode consolidar as políticas e, dessa forma, reduzir o número de políticas a serem gerenciadas.   
- You can manage all your Conditional Access policies in one central location.
- O portal clássico do Azure será desativado.   

This article explains what you need to know to migrate your existing Conditional Access policies to the new framework.
 
## <a name="classic-policies"></a>Políticas clássicas

In the [Azure portal](https://portal.azure.com), the [Conditional Access - Policies](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) page is your entry point to your Conditional Access policies. However, in your environment, you might also have Conditional Access policies you have not created using this page. Essas políticas são conhecidas como *políticas clássicas*. Classic policies are Conditional Access policies, you have created in:

- Portal clássico do Azure
- Portal clássico do Intune
- Portal de Proteção de Aplicativo do Intune

On the **Conditional Access** page, you can access your classic policies by clicking [**Classic policies (preview)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) in the **Manage** section. 

![Azure Active Directory](./media/policy-migration/71.png)

O modo de exibição **Políticas clássicas** fornece uma opção para:

- Filtrar suas políticas clássicas.
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- Desativar políticas clássicas.

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- Review the settings of a classic policy (and to disable it).

   ![Azure Active Directory](./media/policy-migration/74.png)

Se você tiver desabilitado uma política clássica, você não pode mais reverter essa etapa. Isso é porque você pode modificar a associação de grupo em uma política clássica com o modo de exibição **Detalhes**. 

![Azure Active Directory](./media/policy-migration/75.png)

Alterando os grupos selecionados ou excluindo grupos específicos, você pode testar o efeito de uma política clássica desabilitada para alguns usuários de teste antes de desabilitar a política para todos os usuários e grupos incluídos. 

## <a name="azure-ad-conditional-access-policies"></a>Azure AD Conditional Access policies

With Conditional Access in the Azure portal, you can manage all your policies in one central location. Because the implementation of how Conditional Access has changed, you should familiarize yourself with the basic concepts before migrating your classic policies.

Consulte:

- [What is Conditional Access in Azure Active Directory](../active-directory-conditional-access-azure-portal.md) to learn about the basic concepts and the terminology.
- [Best practices for Conditional Access in Azure Active Directory](best-practices.md) to get some guidance on deploying Conditional Access in your organization.
- [Require MFA for specific apps with Azure Active Directory Conditional Access](app-based-mfa.md) to familiarize yourself with the user interface in the Azure portal.
 
## <a name="migration-considerations"></a>Considerações sobre a migração

In this article, Azure AD Conditional Access policies are also referred to as *new policies*.
Suas políticas clássicas continuam funcionando paralelamente com as novas políticas até que você as desabilite ou exclua. 

Os seguintes aspectos são importantes no contexto de consolidação de uma política:

- Uma vez que as políticas clássicas são associadas a um aplicativo de nuvem específica, você pode selecionar quantos aplicativos de nuvem você precisar de uma nova política.
- Controles de uma política clássica e uma nova política para um aplicativo de nuvem exigem que todos os controles (*AND*) sejam atendidos. 
- Em uma nova política, você pode:
   - Combine várias condições se exigido por seu cenário. 
   - Selecione vários requisitos como acesso de controle e combine-os com uma lógica *OR* (requer um dos controles selecionados) ou com uma lógica *AND* (requer todos os controles selecionados).

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Se você deseja migrar as políticas de clássicas para **Office 365 Exchange online** que incluem **Exchange Active Sync** como condição de aplicativos cliente, você não pode consolidá-las em uma nova política. 

Isso é, por exemplo, o caso se você deseja dar suporte a todos os tipos de aplicativo cliente. Em uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente, não será possível selecionar outros aplicativos cliente.

![Azure Active Directory](./media/policy-migration/64.png)

A consolidação em uma nova política também não é possível se suas políticas clássicas possuem várias condições. Uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente configurada não oferece suporte para outras condições:   

![Azure Active Directory](./media/policy-migration/08.png)

Se você tiver uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente configurada, você precisa certificar-se de que todas as outras condições não estão configuradas. 

![Azure Active Directory](./media/policy-migration/16.png)
 
As políticas clássicas [com base no aplicativo](technical-reference.md#approved-client-app-requirement) para o Office 365 Exchange online que incluem **Exchange Active Sync** como condição de aplicativos cliente permitem **plataformas de dispositivo** **com suporte** e [sem suporte](technical-reference.md#device-platform-condition). Uma vez que você não pode configurar plataformas de dispositivos individuais em uma nova política relacionada, você pode limitar o suporte somente para [plataformas de dispositivo com suporte](technical-reference.md#device-platform-condition). 

![Azure Active Directory](./media/policy-migration/65.png)

Você pode consolidar várias políticas clássicas que incluem **Exchange Active Sync** como condição de aplicativos cliente se eles tiverem:

- Somente **Exchange Active Sync** como condição 
- Vários requisitos para concessão de acesso configurado

Um cenário comum é a consolidação de:

- Uma política clássica baseada em dispositivo do portal clássico do Azure 
- Uma política clássica com base no aplicativo no portal de proteção do aplicativo do Intune 
 
Nesse caso, você pode consolidar suas políticas clássicas em uma nova política que tem dois requisitos selecionados.

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Plataformas de dispositivo

As políticas clássicas com [controles baseados em aplicativo](technical-reference.md#approved-client-app-requirement) são pré-configurados com iOS e Android como [condição de plataforma de dispositivo](technical-reference.md#device-platform-condition). 

Em uma nova política, você precisa selecionar as [plataformas de dispositivo](technical-reference.md#device-platform-condition) que você deseja dar suporte individualmente.

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>Próximos passos

- Se você quiser saber como configurar uma política de Acesso Condicional, consulte [Exigir MFA para aplicativos específicos com Acesso Condicional do Azure Active Directory](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md). 
