---
title: Migrar políticas de acesso condicional-Azure Active Directory
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
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>O que é uma migração de política no Azure Active Directory acesso condicional? 

O [acesso condicional](../active-directory-conditional-access-azure-portal.md) é um recurso do Azure AD (Azure Active Directory) que permite controlar como os usuários autorizados acessam seus aplicativos de nuvem. Embora a finalidade ainda seja a mesma, o lançamento do novo portal do Azure introduziu melhorias significativas no funcionamento do acesso condicional.

Considere migrar as políticas que você não tiver criado no Portal do Azure porque:

- Agora você pode abordar cenários que você não podia manipular antes.
- Você pode consolidar as políticas e, dessa forma, reduzir o número de políticas a serem gerenciadas.   
- Você pode gerenciar todas as políticas de acesso condicional em um local central.
- O portal clássico do Azure será desativado.   

Este artigo explica o que você precisa saber para migrar suas políticas de acesso condicional existentes para a nova estrutura.
 
## <a name="classic-policies"></a>Políticas clássicas

No [portal do Azure](https://portal.azure.com), a página de [políticas de acesso condicional](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) é seu ponto de entrada para suas políticas de acesso condicional. No entanto, em seu ambiente, você também pode ter políticas de acesso condicional que você não criou usando esta página. Essas políticas são conhecidas como *políticas clássicas*. As políticas clássicas são políticas de acesso condicional, que você criou em:

- Portal clássico do Azure
- Portal clássico do Intune
- Portal de Proteção de Aplicativo do Intune

Na página **acesso condicional** , você pode acessar suas políticas clássicas clicando em [**políticas clássicas (versão prévia)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) na seção **gerenciar** . 

![Active Directory do Azure](./media/policy-migration/71.png)

O modo de exibição **Políticas clássicas** fornece uma opção para:

- Filtrar suas políticas clássicas.
 
   ![Active Directory do Azure](./media/policy-migration/72.png)

- Desativar políticas clássicas.

   ![Active Directory do Azure](./media/policy-migration/73.png)
   
- Examine as configurações de uma política clássica (e para desabilitá-la).

   ![Active Directory do Azure](./media/policy-migration/74.png)

Se você tiver desabilitado uma política clássica, você não pode mais reverter essa etapa. Isso é porque você pode modificar a associação de grupo em uma política clássica com o modo de exibição **Detalhes**. 

![Active Directory do Azure](./media/policy-migration/75.png)

Alterando os grupos selecionados ou excluindo grupos específicos, você pode testar o efeito de uma política clássica desabilitada para alguns usuários de teste antes de desabilitar a política para todos os usuários e grupos incluídos. 

## <a name="azure-ad-conditional-access-policies"></a>Políticas de acesso condicional do Azure AD

Com o acesso condicional no portal do Azure, você pode gerenciar todas as suas políticas em um único local central. Como a implementação de como o acesso condicional foi alterado, você deve se familiarizar com os conceitos básicos antes de migrar suas políticas clássicas.

Consulte:

- [O que é o acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md) para saber mais sobre os conceitos básicos e a terminologia.
- [Práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md) para obter diretrizes sobre como implantar o acesso condicional em sua organização.
- [Exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md) para se familiarizar com a interface do usuário na portal do Azure.
 
## <a name="migration-considerations"></a>Considerações sobre a migração

Neste artigo, as políticas de acesso condicional do Azure AD também são conhecidas como *novas políticas*.
Suas políticas clássicas continuam funcionando paralelamente com as novas políticas até que você as desabilite ou exclua. 

Os seguintes aspectos são importantes no contexto de consolidação de uma política:

- Uma vez que as políticas clássicas são associadas a um aplicativo de nuvem específica, você pode selecionar quantos aplicativos de nuvem você precisar de uma nova política.
- Controles de uma política clássica e uma nova política para um aplicativo de nuvem exigem que todos os controles (*AND*) sejam atendidos. 
- Em uma nova política, você pode:
   - Combine várias condições se exigido por seu cenário. 
   - Selecione vários requisitos como acesso de controle e combine-os com uma lógica *OR* (requer um dos controles selecionados) ou com uma lógica *AND* (requer todos os controles selecionados).

   ![Active Directory do Azure](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Se você deseja migrar as políticas de clássicas para **Office 365 Exchange online** que incluem **Exchange Active Sync** como condição de aplicativos cliente, você não pode consolidá-las em uma nova política. 

Isso é, por exemplo, o caso se você deseja dar suporte a todos os tipos de aplicativo cliente. Em uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente, não será possível selecionar outros aplicativos cliente.

![Active Directory do Azure](./media/policy-migration/64.png)

A consolidação em uma nova política também não é possível se suas políticas clássicas possuem várias condições. Uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente configurada não oferece suporte para outras condições:   

![Active Directory do Azure](./media/policy-migration/08.png)

Se você tiver uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente configurada, você precisa certificar-se de que todas as outras condições não estão configuradas. 

![Active Directory do Azure](./media/policy-migration/16.png)
 
As políticas clássicas [com base no aplicativo](technical-reference.md#approved-client-app-requirement) para o Office 365 Exchange online que incluem **Exchange Active Sync** como condição de aplicativos cliente permitem **plataformas de dispositivo** **com suporte** e [sem suporte](technical-reference.md#device-platform-condition). Uma vez que você não pode configurar plataformas de dispositivos individuais em uma nova política relacionada, você pode limitar o suporte somente para [plataformas de dispositivo com suporte](technical-reference.md#device-platform-condition). 

![Active Directory do Azure](./media/policy-migration/65.png)

Você pode consolidar várias políticas clássicas que incluem **Exchange Active Sync** como condição de aplicativos cliente se eles tiverem:

- Somente **Exchange Active Sync** como condição 
- Vários requisitos para concessão de acesso configurado

Um cenário comum é a consolidação de:

- Uma política clássica baseada em dispositivo do portal clássico do Azure 
- Uma política clássica com base no aplicativo no portal de proteção do aplicativo do Intune 
 
Nesse caso, você pode consolidar suas políticas clássicas em uma nova política que tem dois requisitos selecionados.

![Active Directory do Azure](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Plataformas de dispositivo

As políticas clássicas com [controles baseados em aplicativo](technical-reference.md#approved-client-app-requirement) são pré-configurados com iOS e Android como [condição de plataforma de dispositivo](technical-reference.md#device-platform-condition). 

Em uma nova política, você precisa selecionar as [plataformas de dispositivo](technical-reference.md#device-platform-condition) que você deseja dar suporte individualmente.

![Active Directory do Azure](./media/policy-migration/41.png)

## <a name="next-steps"></a>Próximas etapas

- Se você quiser saber como configurar uma política de Acesso Condicional, consulte [Exigir MFA para aplicativos específicos com Acesso Condicional do Azure Active Directory](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md). 
