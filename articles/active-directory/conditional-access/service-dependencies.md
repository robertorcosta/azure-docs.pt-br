---
title: Dependências de serviços de acesso condicional - Diretório Ativo do Azure
description: Saiba como as condições são usadas no Azure Active Directory Conditional Access para acionar uma política.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b39238575c05d35a2d87999e08c49c0c77e99bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74380022"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>O que são dependências de serviço no Azure Active Directory Conditional Access? 

Com políticas de acesso condicional, você pode especificar os requisitos de acesso a sites e serviços. Por exemplo, seus requisitos de acesso podem incluir a necessidade de autenticação multifatorial (MFA) ou [dispositivos gerenciados](require-managed-devices.md). 

Quando você acessa um site ou serviço diretamente, o impacto de uma política relacionada é normalmente fácil de avaliar. Por exemplo, se você tiver uma política que exija que o MFA para o SharePoint Online seja configurado, o MFA será aplicado para cada login no portal web SharePoint. No entanto, nem sempre é direto avaliar o impacto de uma política porque existem aplicativos em nuvem com dependências para outros aplicativos em nuvem. Por exemplo, as equipes da Microsoft podem fornecer acesso aos recursos no SharePoint Online. Assim, quando você acessa as Equipes Microsoft em nosso cenário atual, você também está sujeito à política do SharePoint MFA.   

## <a name="policy-enforcement"></a>Aplicação de políticas 

Se você tiver uma dependência de serviço configurada, a diretiva pode ser aplicada usando a aplicação antecipada ou vinculada tardia. 

- **A aplicação antecipada da diretiva** significa que um usuário deve satisfazer a política de serviço dependente antes de acessar o aplicativo de chamada. Por exemplo, um usuário deve satisfazer a política do SharePoint antes de entrar em Equipes MS. 
- **A aplicação da diretiva vinculada tardia** ocorre após o usuário entrar no aplicativo de chamada. A aplicação é adiada para quando se chama solicitações de aplicativos, um token para o serviço downstream. Exemplos incluem equipes MS acessando Planner e Office.com acessando o SharePoint. 

O diagrama abaixo ilustra as dependências de serviço das Equipes MS. Setas sólidas indicam a aplicação antecipada da seta tracejada para Planner indica a aplicação tardia. 

![MS Equipes de dependências de serviço](./media/service-dependencies/01.png)

Como uma prática recomendada, você deve definir políticas comuns em aplicativos e serviços relacionados sempre que possível. Ter uma postura de segurança consistente proporciona a você a melhor experiência de usuário. Por exemplo, definir uma política comum no Exchange Online, SharePoint Online, Microsoft Teams e Skype para negócios reduz significativamente os pedidos inesperados que podem surgir de diferentes políticas que estão sendo aplicadas a serviços a jusante. 

A tabela abaixo lista dependências adicionais de serviço, onde os aplicativos clientes devem satisfazer  

| Aplicativos cliente         | Serviço a jusante                          | Imposição |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Gerenciamento do Microsoft Azure (portal e API) | Início |
| Sala de aula da Microsoft | Exchange                                    | Início |
|                     | SharePoint                                  | Início |
| Equipes da Microsoft     | Exchange                                    | Início |
|                     | Planejador de MS                                  | Tarde-bound  |
|                     | SharePoint                                  | Início |
|                     | Skype for Business Online                   | Início |
| Portal do Escritório       | Exchange                                    | Tarde-bound  |
|                     | SharePoint                                  | Tarde-bound  |
| Grupos do Outlook      | Exchange                                    | Início |
|                     | SharePoint                                  | Início |
| PowerApps           | Gerenciamento do Microsoft Azure (portal e API) | Início |
|                     | Windows Azure Active Directory              | Início |
| Project             | Dynamics CRM                                | Início |
| Skype for Business  | Exchange                                    | Início |
| Visual Studio       | Gerenciamento do Microsoft Azure (portal e API) | Início |
| Microsoft Forms     | Exchange                                    | Início |
|                     | SharePoint                                  | Início |
| Microsoft To-Do     | Exchange                                    | Início |

## <a name="next-steps"></a>Próximas etapas

Para saber como implementar o Acesso Condicional em seu ambiente, confira [Planejar a implantação do Acesso Condicional no Azure Active Directory](plan-conditional-access.md).
