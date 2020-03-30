---
title: Migrar políticas de acesso condicional – Azure Active Directory
description: Veja o que você precisa saber para migrar as políticas clássicas no portal do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1f76dd1489e00115d0f805add8d754038df84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185921"
---
# <a name="conditional-access-classic-policy-migration"></a>Migração de políticas clássicas de acesso condicional

O acesso condicional é a ferramenta usada pelo Azure Active Directory para reunir sinais, tomar decisões e impor políticas organizacionais. O acesso condicional está no centro do novo plano de controle controlado por identidade. Embora o propósito ainda seja o mesmo, o lançamento do novo portal Azure introduziu melhorias significativas na forma como o Conditional Access funciona.

Considere migrar as políticas que você não tiver criado no Portal do Azure porque:

- Agora você pode abordar cenários que você não podia manipular antes.
- Você pode consolidar as políticas e, dessa forma, reduzir o número de políticas a serem gerenciadas.
- Você pode gerenciar todas as suas políticas de acesso condicional em um local central.
- O portal clássico do Azure será desativado.

Este artigo explica o que você precisa saber para migrar suas políticas de Acesso Condicional existentes para o novo framework.

## <a name="classic-policies"></a>Políticas clássicas

No [portal Azure,](https://portal.azure.com)as políticas de acesso condicional podem ser encontradas no acesso**condicionado de****segurança** > do diretório >  **ativo do Azure.** Sua organização também pode ter políticas de acesso condicional mais antigas não criadas usando esta página. Essas políticas são conhecidas como *políticas clássicas*. Políticas clássicas são políticas de acesso condicional, você criou em:

- Portal clássico do Azure
- Portal clássico do Intune
- Portal de Proteção de Aplicativo do Intune

Na página **Acesso Condicional,** você pode acessar suas políticas clássicas clicando em [**políticas clássicas**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) na seção **Gerenciar.** 

![Acesso Condicional no Azure AD mostrando a visualização de políticas clássicas](./media/policy-migration/71.png)

O modo de exibição **Políticas clássicas** fornece uma opção para:

- Filtrar suas políticas clássicas.
- Desativar políticas clássicas.
- Revise as configurações de uma política clássica e desative-a.

   ![Detalhes clássicos da diretiva, incluindo a configuração da diretiva existente](./media/policy-migration/74.png)

> [!WARNING]
> Uma vez desativada, uma política clássica não pode ser reativada.

A exibição de detalhes de uma diretiva clássica permite documentar as configurações, modificar os grupos incluídos ou excluídos e desativar a diretiva.

![Detalhes da política - Grupos para incluir ou excluir](./media/policy-migration/75.png)

Ao alterar os grupos selecionados ou excluindo grupos específicos, você pode testar o efeito de uma diretiva clássica desativada para alguns usuários de teste antes de desativar a diretiva para todos os usuários e grupos incluídos.
 
## <a name="migration-considerations"></a>Considerações sobre a migração

Neste artigo, as políticas de acesso condicional do Azure AD também são referidas como *novas políticas*.
Suas políticas clássicas continuam funcionando paralelamente com as novas políticas até que você as desabilite ou exclua. 

Os seguintes aspectos são importantes no contexto de consolidação de uma política:

- Uma vez que as políticas clássicas são associadas a um aplicativo de nuvem específica, você pode selecionar quantos aplicativos de nuvem você precisar de uma nova política.
- Controles de uma política clássica e uma nova política para um aplicativo de nuvem exigem que todos os controles (*AND*) sejam atendidos. 
- Em uma nova política, você pode:
   - Combine várias condições se exigido por seu cenário. 
   - Selecione vários requisitos como acesso de controle e combine-os com uma lógica *OR* (requer um dos controles selecionados) ou com uma lógica *AND* (requer todos os controles selecionados).

### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Se você deseja migrar as políticas de clássicas para **Office 365 Exchange online** que incluem **Exchange Active Sync** como condição de aplicativos cliente, você não pode consolidá-las em uma nova política. 

Isso é, por exemplo, o caso se você deseja dar suporte a todos os tipos de aplicativo cliente. Em uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente, não será possível selecionar outros aplicativos cliente.

![Acesso Condicional selecionando aplicativos clientes](./media/policy-migration/64.png)

A consolidação em uma nova política também não é possível se suas políticas clássicas possuem várias condições. Uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente configurada não oferece suporte para outras condições:   

![O Exchange ActiveSync não suporta as condições selecionadas](./media/policy-migration/08.png)

Se você tiver uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente configurada, você precisa certificar-se de que todas as outras condições não estão configuradas. 

![Condições de Acesso Condicional](./media/policy-migration/16.png)
 
Políticas clássicas baseadas em aplicativos para o Office 365 Exchange Online que incluem **o Exchange Active Sync** como condição de aplicativos clientes permitem plataformas de dispositivos **suportadas** e **sem suporte.** Uma vez que você não pode configurar plataformas de dispositivos individuais em uma nova política relacionada, você pode limitar o suporte somente para [plataformas de dispositivo com suporte](concept-conditional-access-conditions.md#device-platforms). 

![Acesso condicional seleciona O Exchange ActiveSync](./media/policy-migration/65.png)

Você pode consolidar várias políticas clássicas que incluem **Exchange Active Sync** como condição de aplicativos cliente se eles tiverem:

- Somente **Exchange Active Sync** como condição 
- Vários requisitos para concessão de acesso configurado

Um cenário comum é a consolidação de:

- Uma política clássica baseada em dispositivo do portal clássico do Azure 
- Uma política clássica com base no aplicativo no portal de proteção do aplicativo do Intune 
 
Nesse caso, você pode consolidar suas políticas clássicas em uma nova política que tem dois requisitos selecionados.

![Controles de subvenção de acesso condicional](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Plataformas de dispositivo

As políticas clássicas com controles baseados em aplicativo são pré-configurados com iOS e Android como condição de plataforma de dispositivo. 

Em uma nova política, você precisa selecionar as [plataformas de dispositivo](concept-conditional-access-conditions.md#device-platforms) que você deseja dar suporte individualmente.

![Seleção de plataformas de dispositivos de acesso condicional](./media/policy-migration/41.png)

## <a name="next-steps"></a>Próximas etapas

- [Use o modo somente relatório para acesso condicional para determinar o impacto de novas decisões políticas.](concept-conditional-access-report-only.md)
- Se você quiser saber como configurar uma política de acesso condicional, consulte [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md).
- Se você estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte o artigo [Como: Planejar sua implantação de Acesso Condicional no Azure Active Directory](plan-conditional-access.md). 
